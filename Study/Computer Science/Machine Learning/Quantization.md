F### 1. Dynamic Quantization (동적 양자화)

- 가장 간단한 양자화 기법
- 모델의 가중치(weight)에 대해서만 양자화 진행
- 활성화(activations)는 추론할 때 동적으로 양자화
    - activations는 메모리에 부동소수점 형태로 read, write 됨.
    - inference시에만 floating-point kernel를 이용해여 weights를 int8을 float32로 convert됨. Activation은 항상 floating point로 저장되어져 있습니다. 그래서 quantized kernels processing을 지원하는 operator의 경우에는 activation을 processing전에 dynamic하게 8bit로 quantized하고 processing후에 다시 dequantization하게 됩니다.
    - weights들은 training 후에 quantize
    - activations은 inference time에 dynamic하게 quantized
- 모델을 메모리에 로딩하는 속도 개선
- 연산속도 향상 효과 미비 (inference kernel 연산이 필요하기 때문)
- CPU 환경에서만 inference 가능 (프레임워크나 프레임워크의 버전에따라 GPU 환경에서도 동작할 순 있음)
    - With PyTorch 1.7.0, we could do dynamic quantization using x86-64 and aarch64 CPUs. However, NVIDIA GPUs have not been supported for PyTorch dynamic quantization yet.
- 모델의 weights를 메모리에 loading하는 것이 execution time에 큰 영향을 미치는 BERT와 같은 모델에 적합
    - BERT를 Quantization하는 튜토리얼(Pytorch): [https://pytorch.org/tutorials/intermediate/dynamic_quantization_bert_tutorial.html](https://pytorch.org/tutorials/intermediate/dynamic_quantization_bert_tutorial.html)
    - Dynamic Quantization is used for situations where the model execution time is dominated by loading weights from memory rather than computing the matrix multiplications
- 예시
    
    ```null
    # create a model instance
    model_fp32 = M()
    # create a quantized model instance
    model_int8 = torch.quantization.quantize_dynamic(
        model_fp32,  # the original model
        {torch.nn.Linear},  # a set of layers to dynamically quantize
        dtype=torch.qint8)  # the target dtype for quantized weights
    ```
    
- Diagram

```null
# original model
# all tensors and computations are in floating point
previous_layer_fp32 -- linear_fp32 -- activation_fp32 -- next_layer_fp32
                 /
linear_weight_fp32

# dynamically quantized model
# linear and LSTM weights are in int8
previous_layer_fp32 -- linear_int8_w_fp32_inp -- activation_fp32 -- next_layer_fp32
                     /
   linear_weight_int8
```

- 참고자료
    - [https://medium.com/@joel_34050/quantization-in-deep-learning-478417eab72b](https://medium.com/@joel_34050/quantization-in-deep-learning-478417eab72b)

### 2. Static Quantization / Post-Training Static Quantization (정적 양자화)

- 모델의 가중치와 활성화(activations) 모두 양자화를 사전에 진행
    
    - 가중치와 활성화 fusion
    - calibration하는 동안 활성화가 설정됨
        - *calibration: 직역하면 눈금 매김 (미세조정 같은 것으로 이해)
- static quantization의 활성화 quantization을 위해 activation의 preceding layer와 fusion 수행  
    - fusion = 각각의 기능을 수행하는 layer를 하나로 합침.  
    - activation, convolution 등 layer를 합쳐 layer 간에 데이터 이동으로 발생하는 context switching overhead를 줄일 수 있음.  
    - sequential하게 처리되던 연산을 병렬도 처리할 수 있다는 장점도 있음.  
    ![](https://i.imgur.com/ofQZIMC.png)  
    - [Conv, Relu], [Conv, BatchNorm], [Conv, BatchNorm, Relu], [Linear, Relu] 등과 같은 fusion이 있음.
    
- 정확도 손실을 최소화하기 위해 calibration으로 미세조정
    
    - calibration으로 range 설정을 조절할 수 있음.
    - histogram에따른 calibration 등
    - 대표 데이터셋을 통해 calibration 조정
- 연산속도 향상
    
- tflite는 CPU, GPU 환경에서 추론 가능 / pytorch는 CPU만 가능
    
- Pytorch 예시
    
    ```null
    import torch
    
    # define a floating point model where some layers could be statically quantized
    class M(torch.nn.Module):
        def __init__(self):
            super(M, self).__init__()
            # QuantStub converts tensors from floating point to quantized
            self.quant = torch.quantization.QuantStub()
            self.conv = torch.nn.Conv2d(1, 1, 1)
            self.relu = torch.nn.ReLU()
            # DeQuantStub converts tensors from quantized to floating point
            self.dequant = torch.quantization.DeQuantStub()
    
        def forward(self, x):
            # manually specify where tensors will be converted from floating
            # point to quantized in the quantized model
            x = self.quant(x)
            x = self.conv(x)
            x = self.relu(x)
            # manually specify where tensors will be converted from quantized
            # to floating point in the quantized model
            x = self.dequant(x)
            return x
    
    # create a model instance
    model_fp32 = M()
    
    # model must be set to eval mode for static quantization logic to work
    model_fp32.eval()
    
    # attach a global qconfig, which contains information about what kind
    # of observers to attach. Use 'fbgemm' for server inference and
    # 'qnnpack' for mobile inference. Other quantization configurations such
    # as selecting symmetric or assymetric quantization and MinMax or L2Norm
    # calibration techniques can be specified here.
    model_fp32.qconfig = torch.quantization.get_default_qconfig('fbgemm')
    
    # Fuse the activations to preceding layers, where applicable.
    # This needs to be done manually depending on the model architecture.
    # Common fusions include `conv + relu` and `conv + batchnorm + relu`
    model_fp32_fused = torch.quantization.fuse_modules(model_fp32, [['conv', 'relu']])
    
    # Prepare the model for static quantization. This inserts observers in
    # the model that will observe activation tensors during calibration.
    model_fp32_prepared = torch.quantization.prepare(model_fp32_fused)
    
    # calibrate the prepared model to determine quantization parameters for activations
    # in a real world setting, the calibration would be done with a representative dataset
    input_fp32 = torch.randn(4, 1, 4, 4)
    model_fp32_prepared(input_fp32)
    
    # Convert the observed model to a quantized model. This does several things:
    # quantizes the weights, computes and stores the scale and bias value to be
    # used with each activation tensor, and replaces key operators with quantized
    # implementations.
    model_int8 = torch.quantization.convert(model_fp32_prepared)
    
    # run the model, relevant calculations will happen in int8
    res = model_int8(input_fp32)
    ```
    
- Diagram
    
    ```null
    # original model
    # all tensors and computations are in floating point
    previous_layer_fp32 -- linear_fp32 -- activation_fp32 -- next_layer_fp32
                        /
        linear_weight_fp32
    
    # statically quantized model
    # weights and activations are in int8
    previous_layer_int8 -- linear_with_activation_int8 -- next_layer_int8
                        /
      linear_weight_int8
    ```
    
- 활성화가 inference에 영향이 큰 CNN 모델에 적합
    
- 참고자료
    
    - [https://pytorch.org/docs/stable/quantization.html](https://pytorch.org/docs/stable/quantization.html)
    - [https://docs.nvidia.com/deeplearning/tensorrt/pytorch-quantization-toolkit/docs/tutorials/quant_resnet50.html](https://docs.nvidia.com/deeplearning/tensorrt/pytorch-quantization-toolkit/docs/tutorials/quant_resnet50.html)
    - [https://talkingaboutme.tistory.com/entry/DL-Optimization-Techniques](https://talkingaboutme.tistory.com/entry/DL-Optimization-Techniques)

### 3. Quantization aware training

- 모델의 가중치와 활성화를 학습하면서 양자화
    
- fake-quantization modules/nodes를 양자화가 되는 부분에 위치시킴.  
    ![](https://i.imgur.com/81FVWWA.png)
    
- Fake-quantization은 clamping과 rounding을 수행
    
    - *clamping = 데이터 범위
- quantization-aware training 중에 활성화 함수(activation)의 실제 출력 범위(최대/최소) 확인도 진행
    
- quantization aware trainingdl 끝나면, fake-quantization modules에 저장된 정보를 이용하여, floating point 모델이 integer 모델로 변경할 수 있음.
    
    - Once the quantization aware training is finished, the floating point model could be converted to quantized integer model immediately using the information stored in the fake quantization modules.
- Dynamic, Static Quantization 보다 높은 accuracy 확보
    
- 학습은 CPU, GPU 환경에서 사용 가능 / 추론은 CPU에서만 가능
    
- dynamic, static quantization으로 성능이 나오지 않는 CNN 모델에서 활용
    
- pytorch 예시
    
    ```null
    import torch
    
    # define a floating point model where some layers could benefit from QAT
    class M(torch.nn.Module):
        def __init__(self):
            super(M, self).__init__()
            # QuantStub converts tensors from floating point to quantized
            self.quant = torch.quantization.QuantStub()
            self.conv = torch.nn.Conv2d(1, 1, 1)
            self.bn = torch.nn.BatchNorm2d(1)
            self.relu = torch.nn.ReLU()
            # DeQuantStub converts tensors from quantized to floating point
            self.dequant = torch.quantization.DeQuantStub()
    
        def forward(self, x):
            x = self.quant(x)
            x = self.conv(x)
            x = self.bn(x)
            x = self.relu(x)
            x = self.dequant(x)
            return x
    
    # create a model instance
    model_fp32 = M()
    
    # model must be set to train mode for QAT logic to work
    model_fp32.train()
    
    # attach a global qconfig, which contains information about what kind
    # of observers to attach. Use 'fbgemm' for server inference and
    # 'qnnpack' for mobile inference. Other quantization configurations such
    # as selecting symmetric or assymetric quantization and MinMax or L2Norm
    # calibration techniques can be specified here.
    model_fp32.qconfig = torch.quantization.get_default_qat_qconfig('fbgemm')
    
    # fuse the activations to preceding layers, where applicable
    # this needs to be done manually depending on the model architecture
    model_fp32_fused = torch.quantization.fuse_modules(model_fp32,
        [['conv', 'bn', 'relu']])
    
    # Prepare the model for QAT. This inserts observers and fake_quants in
    # the model that will observe weight and activation tensors during calibration.
    model_fp32_prepared = torch.quantization.prepare_qat(model_fp32_fused)
    
    # run the training loop (not shown)
    training_loop(model_fp32_prepared)
    
    # Convert the observed model to a quantized model. This does several things:
    # quantizes the weights, computes and stores the scale and bias value to be
    # used with each activation tensor, fuses modules where appropriate,
    # and replaces key operators with quantized implementations.
    model_fp32_prepared.eval()
    model_int8 = torch.quantization.convert(model_fp32_prepared)
    
    # run the model, relevant calculations will happen in int8
    res = model_int8(input_fp32)
    ```
    
- Diagram
    
    ```null
    # original model
    # all tensors and computations are in floating point
    previous_layer_fp32 -- linear_fp32 -- activation_fp32 -- next_layer_fp32
                          /
        linear_weight_fp32
    
    # model with fake_quants for modeling quantization numerics during training
    previous_layer_fp32 -- fq -- linear_fp32 -- activation_fp32 -- fq -- next_layer_fp32
                               /
       linear_weight_fp32 -- fq
    
    # quantized model
    # weights and activations are in int8
    previous_layer_int8 -- linear_with_activation_int8 -- next_layer_int8
                         /
       linear_weight_int8
    ```
    
- 참고자료
    
    - [https://wannabeaprogrammer.tistory.com/42](https://wannabeaprogrammer.tistory.com/42)
    - [https://leimao.github.io/blog/PyTorch-Quantization-Aware-Training/](https://leimao.github.io/blog/PyTorch-Quantization-Aware-Training/)
    - [https://m.blog.naver.com/PostView.naver?blogId=kangdonghyun&logNo=221316398483&proxyReferer=https:%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.naver?blogId=kangdonghyun&logNo=221316398483&proxyReferer=https:%2F%2Fwww.google.com%2F)


## Quantization 모델 실행 환경

Today, PyTorch supports the following backends for running quantized operators efficiently:

**x86 CPUs with AVX2 support or higher (without AVX2 some operations have inefficient implementations)**

**ARM CPUs (typically found in mobile/embedded devices)**

At the moment PyTorch doesn’t provide quantized operator implementations on CUDA - this is the direction for future work. Move the model to CPU in order to test the quantized functionality.

However, quantization aware training occurs in full floating point and can run on either GPU or CPU. Quantization aware training is typically only used in CNN models when post training static or dynamic quantization doesn’t yield sufficient accuracy. This can occur with models that are highly optimized to achieve small size (such as Mobilenet).

- float16 quantization은 GPU에서 inference 가능
- TPU에서는 integer quantization이 효율적
- 참고자료
    - [https://talkingaboutme.tistory.com/entry/Embedded-DL-Tensorflow-Lite-Quantization](https://talkingaboutme.tistory.com/entry/Embedded-DL-Tensorflow-Lite-Quantization)

## Quantization approach 선정

- 양자화 기법 선정 가이드라인  
    ![](https://i.imgur.com/9QWynOq.png)
- 모델별 양자화 기법 선정  
    ![](https://i.imgur.com/b2TtdO5.png)
- 성능 결과  
    ![](https://i.imgur.com/2E4gC2k.png)
- accuracy 결과  
    ![](https://i.imgur.com/zkg7nQM.png)
- 참고자료: [https://n-brogrammer.tistory.com/m/135](https://n-brogrammer.tistory.com/m/135)

## Quantization 지원 프레임워크

- tflite (tensorflow lite)
    
    ```null
    converter = tf.lite.TFLiteConverter.from_keras_model(keras_model)
    ```
    
- pytorch API
    - torch.quantization.* 라이브러리 활용
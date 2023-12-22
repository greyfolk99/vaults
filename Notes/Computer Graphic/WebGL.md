Web Graphics Library 

화려하고 인터랙션이 많은 그래픽을 처리할 때 사용 OpenGL ES의 API를 웹에서 사용할 수 있도록 개발됨 webGL 1 ver: OpenGL ES 2.0v WebGL 2 ver : OpenGL ES 3.0v GPU를 통해 렌더링 계산이 이뤄짐 GLSL 을 지원하는 네이티브 API 캔버스 내부에서 작동


WebGL 은 종종 3D API로 여겨진다.  
사실 **WebGL 은 [[Rasterization]] 엔진**에 불과하다.

**WebGL 은 컴퓨터에 있는 GPU에서 실행된다.**  
따라서 GPU에서 실행되는 코드를 제공해야 한다.  
해당 코드는 함수 쌍 형태로 제공해야 한다.

두 함수는 [[Vertex Shader]]와[[ Fragment Shader]] 라고 불리고 C/C++ 처럼 엄격한 타입을 가지는 [[GLSL (Graphics Library Shader Language)]] 작성**되어 있다.

이 **두 개를 합쳐 프로그램** 이라고 부른다.

**Vertex Shader (정점 셰이더) 의 역할은 정점 위치를 계산하는 것이다.**

WebGL은 함수가 출력하는 위치를 기반으로 점, 선, 삼각형 등의 다양한 종류의 Primitive를 래스터화 할 수 있다.

**- Primitive: 그래픽스의 기본적인 기하학 요소.** 점, 선, 삼각형, 사각형, 폴리곤

래스터화할 때 프리미티브는 Fragment Shader라고 불리는 두 번째 사용자 작성 함수를 호출한다.

**Fragment Shader의 역할은 현재 그려지는 프리미티브들의 각 픽셀에 대한 색상을 계산하는 것이다.**

대부분의 WebGL API 는 이러한 함수 쌍을 실행하기 위한 상태 설정에 관한 것이다.  
원하는 것을 그리기 위해서는 여러 상태를 설정 후, GPU 에서 Shader를 실행하는 `gl.drawArrays` 나 `gl.drawElements` 를 호출해서 함수 쌍을 실행한다.

이러한 함수가 접근하는 모든 데이터는 GPU 에 제공되어야 한다.  
Shader가 데이터를 받을 수 있는 방법에는 네 가지가 있다.
https://blog.wishket.com/api%EB%9E%80-%EC%89%BD%EA%B2%8C-%EC%84%A4%EB%AA%85-%EA%B7%B8%EB%A6%B0%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8/

#### API란 무엇인지, 쉽게 알아보자!

![API란 무엇인가](https://blog.wishket.com/wp-content/uploads/2019/10/API-%EC%89%BD%EA%B2%8C-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0.png)  

API를 본격적으로 알아보기 전에, 비유를 들어 쉽게 설명을 도와드리겠습니다. 여러분이 멋진 레스토랑에 있다고 가정해봅시다. 점원이 가져다준 메뉴판을 보면서 먹음직스러운 스테이크를 고르면, 점원이 주문을 받아 요리사에 요청을 할 텐데요. 그러면 요리사는 정성껏 스테이크를 만들어 점원에게 주고, 여러분은 점원이 가져다준 맛있는 음식을 먹을 수 있게 됩니다.  
여기서 점원의 역할을 한 번 살펴보겠습니다. 점원은 손님에게 메뉴를 알려주고, 주방에 주문받은 요리를 요청합니다. 그다음 주방에서 완성된 요리를 손님께 다시 전달하지요. API는 점원과 같은 역할을 합니다.  
API는 손님(프로그램)이 주문할 수 있게 메뉴(명령 목록)를 정리하고, 주문(명령)을 받으면 요리사(응용프로그램)와 상호작용하여 요청된 메뉴(명령에 대한 값)를 전달합니다.  
쉽게 말해, **API는 프로그램들이 서로 상호작용하는 것을 도와주는 매개체**로 볼 수 있습니다.

#### **API의 역할은?** 

**1. API는 서버와 데이터베이스에 대한 출입구 역할을 한다.**  
: 데이터베이스에는 소중한 정보들이 저장되는데요. 모든 사람들이 이 데이터베이스에 접근할 수 있으면 안 되겠지요. API는 이를 방지하기 위해 여러분이 가진 서버와 데이터베이스에 대한 출입구 역할을 하며, 허용된 사람들에게만 접근성을 부여해줍니다.

**2. API는 애플리케이션과 기기가 원활하게 통신할 수 있도록 한다.**  
: 여기서 애플리케이션이란 우리가 흔히 알고 있는 스마트폰 어플이나 프로그램을 말합니다. API는 애플리케이션과 기기가 데이터를 원활히 주고받을 수 있도록 돕는 역할을 합니다.

**3. API는 모든 접속을 표준화한다.**  
API는 모든 접속을 표준화하기 때문에 기계/ 운영체제 등과 상관없이 누구나 동일한 액세스를 얻을 수 있습니다. 쉽게 말해, API는 범용 플러그처럼 작동한다고 볼 수 있습니다.

#### API유형은 어떤게 있을까?

**1) private API**  
: private API는 내부 API로, 회사 개발자가 자체 제품과 서비스를 개선하기 위해 내부적으로 발행합니다. 따라서 제 3자에게 노출되지 않습니다.

**2) public API**  
: public API는 개방형 API로, 모두에게 공개됩니다. 누구나 제한 없이 API를 사용할 수 있는 게 특징입니다.

**3) partner API**  
:partner API는 기업이 데이터 공유에 동의하는 특정인들만 사용할 수 있습니다. 비즈니스 관계에서 사용되는 편이며, 종종 파트너 회사 간에 소프트웨어를 통합하기 위해 사용됩니다.

#### API 사용하면 뭐가 좋을까?

API를 사용하면 많은 이점들이 있는데요. Private API를 이용할 경우, 개발자들이 애플리케이션 코드를 작성하는 방법을 표준화함으로써, 간소화되고 빠른 프로세스 처리를 가능하게 합니다. 또한, 소프트 웨어를 통합하고자 할 때는 개발자들 간의 협업을 용이하게 만들어줄 수 있죠.  
public API와 partner API 를 사용하면, 기업은 타사 데이터를 활용하여 브랜드 인지도를 높일 수 있습니다. 뿐만 아니라 고객 데이터베이스를 확장하여 전환율까지 높일 수 있지요.


#### [[REST API]] VS [[SOAP API]]

##### ‘SOAP REST 차이’ : 핵심적인 차이들

!['SOAP REST 차이': 핵심적인 차이들](https://blog.wishket.com/wp-content/uploads/2020/02/unnamed-file-4.png)  SOAP는 프로토콜이고, REST는 아키텍처 스타일입니다. API는 애플리케이션이 서버에 접속할 수 있도록 설계된 일종의 도구이며, SOAP는 서비스 인터페이스를 이용해서 서버에 접근하고, REST는 URI를 이용해서 접근합니다.

API라는 것은 결국은 앱의 페이로드를 처리하기 위해서 만들어진 것인데, ‘SOAP REST 차이’는 페이로드를 처리하는 방식에 있습니다. 페이로드는 인터넷을 통해서 전송되는 데이터입니다. 그렇기 때문에 페이로드가 ‘무거운’경우에는 더 많은 리소스가 필요합니다. **REST는 HTTP와 JSON을 사용하기 때문에 페이로드의 무게를 가볍게 할 수 있습니다. 하지만 SOAP에서는 XML에만 의존합니다.**

‘SOAP REST 차이’는 보안을 다루는 방식에서도 볼 수 있습니다. **SOAP는 WS-Security를 지원하는데, WS-Security는 전송 레벨에서 아주 뛰어나며 SSL보다도 조금 더 복잡하기 때문에 기업용 보안 도구에 통합하는데 보다 이상적입니다.** 둘 다 양단간(end-to-end) 보안성을 위해서 SSL을 지원하며, REST에서는 HTTP의 보안 버전인 HTTPS 프로토콜을 이용할 수 있습니다.

SOAP는 서버와 긴밀하게 연결하며, REST는 보다 느슨한 형태로 연결됩니다. 프로그래밍을 할 때는 일반적으로 **추상화 계층이 더 많을수록 양쪽의 상호작용에서 통제할 수 있는 부분이 줄어들기는 하지만 오히려 구현하기는 쉬우며, 나중에 그 방식을 전부 고치지 않고도 업데이트를 쉽게 할 수 있는 편입니다.** API를 이용해서 서버와 상호작용할 때에도 마찬가지입니다. 그리고 바로 이 부분에서 ‘SOAP REST 차이’를 보이고 있습니다. **SOAP는 서버와 매우 긴밀하게 연결되기 때문에 그 통신 방식이 매우 엄격하며, 그래서 수정이나 업데이트를 하는 것이 보다 더 어렵습니다. REST 방식의 API에서는 클라이언트에서 해당 API가 필요하지 않지만, SOAP 방식의 API에서는 상호작용을 시작할 때조차도 클라이언트에서 API에 관한 모든 정보들을 필요로 합니다.**
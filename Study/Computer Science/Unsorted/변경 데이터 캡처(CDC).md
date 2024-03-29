#cdc 

https://www.redhat.com/ko/topics/integration/what-is-change-data-capture

## CDC란 무엇일까요?

변경 데이터 캡처는 데이터가 변경되는 시점과 해당 항목을 추적하고 이러한 변경에 대응해야 하는 다른 시스템 및 서비스에 알림을 전송하는 검증된 [데이터 통합](https://www.redhat.com/ko/topics/integration) 패턴입니다. 변경 데이터 캡처를 통해 데이터를 사용하는 모든 시스템에서 일관성과 기능을 유지할 수 있습니다.

데이터는 모든 비즈니스에 꼭 필요합니다. 그러나 문제는 데이터가 지속적으로 업데이트되고 변경된다는 것입니다. 그리고 기업은 그에 맞게 변화해야 합니다. 트랜잭션, 주문, 인벤토리 또는 고객 등에 관한 실시간 최신 데이터를 보유하는 것은 비즈니스 운영에서 매우 중요합니다. 구매 주문서가 업데이트되거나, 새로운 고객이 생기거나, 대금을 수령할 때는 이러한 중요 비즈니스 프로세스를 완료하기 위해 전사적으로 애플리케이션에 정보를 제공해야 합니다.

## CDC 및 Apache Kafka를 통한 실시간 변경

CDC는 데이터베이스 변경 사항을 캡처하지만, 해당 시스템과 애플리케이션에 변경 알림을 전달하려면 메시징 서비스가 필요합니다. 이를 가장 효율적으로 실행하는 방법은 [이벤트 기반 아키텍처(EDA)](https://www.redhat.com/ko/topics/integration/what-is-event-driven-architecture)에서와 같이 변경 사항을 이벤트로 처리하고 비동기식으로 전송하는 것입니다.

[Apache Kafka](https://www.redhat.com/ko/topics/integration/what-is-apache-kafka)는 대량의 재생 가능한 사용 패턴을 필요로 하는 사용자와 데이터베이스간 비동기 통신을 제공하기에 아주 좋은 방법입니다. Kafka는 실시간으로 이벤트 스트림을 게시, 구독, 저장 및 처리할 수 있는 분산형 스트리밍 플랫폼으로서, 여러 소스의 데이터 스트림을 처리하고 높은 처리량과 확장성으로 여러 대상에 데이터를 제공하도록 설계되었습니다.

변경 데이터 캡처는 Kafka에서 전송한 이벤트가 소스 시스템 또는 데이터베이스의 변경 사항과 일치하도록 합니다. Kafka 메시징은 비동기식이기 때문에 이벤트가 사용자와 분리되어 모든 변경 사항을 보다 안정적으로 제공할 수 있습니다.

## 변경 데이터 캡처를 사용하는 이유는 무엇인가요?

[Debezium](https://debezium.io/)과 같은 변경 데이터 캡처 플랫폼은 변경 사항이 커밋될 때 트랜잭션 로그를 모니터링함으로써 데이터베이스 변경 사항을 추적합니다. 이 방식 대신 간단한 폴링 기반 프로세스 또는 쿼리 기반 프로세스를 사용할 수도 있습니다. 

트랜잭션 로그에 기반을 둔 CDC는 이러한 옵션에 비해 다음과 같은 장점이 있습니다.

- **모든 변경 사항 캡처:** CDC는 데이터베이스 변경 사항을 모두 캡처하도록 설계되었습니다. CDC가 없으면 폴 루프가 2회 실행되는 사이에 업데이트, 삭제 등 중간 변경 사항 및 새 데이터가 누락될 수 있습니다.
- **낮은 오버헤드:** CDC와 Kafka를 연계해 거의 실시간으로 데이터 변경 사항을 전달합니다. 때문에 잦은 폴링으로 인한 CPU 부하 증가를 방지할 수 있습니다.
- **데이터 모델 영향 없음:** CDC를 사용하는 타임스탬프 열은 더 이상 마지막 데이터 업데이트를 확정하지 않아도 됩니다.

## 활용 사례

다음 예시는 변경 데이터 캡처를 위한 다양한 활용 사례를 보여줍니다.

### 마이크로서비스 통합

CDC를 사용하면 마이크로서비스를 기존의 모놀리식 애플리케이션과 동기화하여 데이터 변경 사항을 레거시 시스템에서 마이크로서비스 기반 애플리케이션으로 원활하게 전송할 수 있습니다.

### 데이터 복제

CDC를 사용하면 여러 데이터베이스, 데이터 레이크 또는 데이터 웨어하우스에 데이터를 복제하여 각 리소스가 최신 버전 데이터를 보유하도록 할 수 있습니다. 이러한 방식으로 CDC는 분산되어 있거나 심지어 사일로화된 여러 팀이 동일한 최신 데이터에 액세스하도록 지원할 수 있습니다. 

### 분석 대시보드

CDC는 비즈니스 인텔리전스와 같은 목적으로 데이터 변경 사항을 분석 대시보드에 제공하여 긴급한 의사결정을 지원할 수 있습니다.

### 감사 및 컴플라이언스

엄격한 데이터 컴플라이언스 요건을 준수하고 규정 미준수에 대한 엄격한 처벌을 피하려면 데이터 변경 이력을 저장하는 것이 중요합니다. CDC를 사용하면 감사 또는 보관 요건에 따라 데이터 변경 사항을 저장할 수 있습니다. 

### 캐시 무효화

CDC를 캐시 무효화를 통해 캐시에서 오래된 항목을 교체하거나 제거하여 최신 버전을 표시할 수 있습니다.

### CQRS 모델 업데이트

CDC를 사용하여 CQRS(Command Query Responsibility Separation) 읽기 모델을 기본 모델과 동기화할 수 있습니다.

### 전체 테스트 검색

CDC를 사용하면 전체 텍스트 검색 인덱스를 데이터베이스와 자동으로 동기화할 수 있습니다.

## CDC의 비즈니스 이점

변경 데이터 캡처를 사용하는 비즈니스는 더 신속한 데이터 기반 의사 결정을 통해 시간, 노력, 수익 면에서 낭비를 줄일 수 있습니다.

### 데이터 가치 극대화

CDC는 기업이 다양한 목적으로 정보를 활용해 데이터 가치를 극대화하도록 지원합니다. CDC는 다양한 사일로에서 동일 데이터를 지속적으로 업데이트하는 방법을 제공하므로 조직은 데이터 무결성을 유지하면서 데이터를 최대한 활용할 수 있습니다.

### 비즈니스를 최신 상태로 유지

CDC를 사용하면 여러 데이터베이스와 애플리케이션이 최신 데이터와 동기화되어 비즈니스 이해관계자에게 최신 정보를 제공할 수 있습니다.

### 의사 결정 수준과 속도 개선

CDC는 비즈니스 사용자가 최신 정보를 기반으로 더욱더 정확하고 신속하게 의사 결정을 내리도록 지원합니다. 의사 결정 데이터의 가치는 빠르게 하락할 때가 많으므로 CDC와 Kafka를 사용하여 모든 이해관계자에게 최대한 즉각적으로 관련 데이터를 제공해야 합니다. 정확한 실시간 분석 기능은 경쟁 우위를 구축하고 유지하는 데 매우 중요합니다.

### 지연 없는 지속적인 운영

여러 시스템의 데이터가 동기화되지 않으면 주문 조정, 트랜잭션 처리, 고객 서비스 제공, 보고서 생성, 프로덕션 일정 준수 관련 문제가 발생할 수 있습니다. 이러한 상황이 하나라도 발생하면 비즈니스가 지연될 수 있으며 이는 매출 손실로 이어집니다. CDC를 사용하는 조직은 대기 시간이 짧아 수많은 시스템의 데이터를 원활하게 동기화하고 지속적인 운영을 지원할 수 있습니다.
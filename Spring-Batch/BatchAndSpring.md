'The Definitive Guide to Spring Batch'를 공부하며 쓴 글입니다.

## 배치가 필요한 이유와 왜 스프링 배치인가
배치라면 그냥 각 비즈니스마다 일괄작업이 필요하고, 서비스가 이용되는 시간을 최대한 피해서 한다. 정도로만 알고 있었는데, 난 배치의 배자도 몰랐다는 사실을 알게 되었다.

### Chapter 1: Batch And Sping
1. 배치(Batch)란?
유한한 양의 데이터를 상호작용(일반적으로 사람-컴퓨터)없이 처리하는 프로세스를 의미한다. 여기서 중요한 것은 상호작용(interactionc)이다. 일반적인 컴퓨터 프로그램들은 사람과의 상호작용을 통해 처리가 이루어진다. 사람이 시키고 최종 결과를 확인하고 등등. 그런데 배치는 이러한 사람의 개입없이 프로세스가 실행된다. 제어하는 사람이 빠졌기 때문에 cron, Jenkins, Quarts와 같은 스케줄러가 필요하고, 예약된 작업이 주로 서비스 비이용시간이기 때문에 일괄처리를 하다보니 배치=일괄처리라는 개념이 굳어진 듯하다.

2. 배치가 필요한 이유
그럼 요즘은 하드웨어도 좋아지고, MSA 트렌드로 애플리케이션과 DB도 분리되어 있어서 실시간 처리도 가능할 것 같은데 왜 굳이 배치를 계속 이용해야할까 라는 의문이 든다. 왜일까?

첫째, 모든 프로세스가 실시간 처리될 필요는 없다. 관련된 여러 프로세스가 다 처리될 때까지 기다려야 하는 경우도 있다. 월별 정산 데이터를 봐야 하는 팀에 해당 자료를 보낼 때 실시간으로 발생하는 결제 데이터를 보내야할 필요가 있을까? 해당 작업일에 맞춰 한 달에 한번만 데이터를 생성하는 것이 유리하다.

둘째, 비즈니스적인 이유이다. 온라인 쇼핑몰을 예로 들자. 고객이 주문하면 주문 데이터가 업체에게 전송되고, 업체가 택배사(또는 직접)와 소통하여 상품을 출고한다. 택배 기사에게 물건이 전달되어 배송 중이기 때문에 취소가 불가능하다. 여기서 주문부터 출고까지 동시에 처리된다면? 논리적인 배송 프로세스 거리가 절약되긴하겠지만, 고객 변심으로 인한 취소가 불가능하다. 낙장불입이 된다. 이러한 고객 편의를 위한 텀을 두기 위해 주문데이터를 업체에 전송하는 프로세스를 배치를 통해 일정 주기로 전달한다.

셋째, 데이터 모델링적인 이유이다. 첫번째 프로세스에서 데이터 양이 많고, 다음 프로세스에서 그 결과 데이터를 사용해서 빠른처리가 필요할 때 이다. 비즈니스에서 쌓인 데이터가 항상 최종 데이터인 것은 아니다. 데이터를 정제하여 가볍게 최종 데이터만 봐야 하는 경우가 있다. 예를 들어 한 온라인 쇼핑몰에서 업체별 월 정산데이터를 확인해야 할 경우이다. 정산팀에서는 1월부터 9월까지 A 업체의 월별 정산데이터를 확인한다고 가정하자. 1월 정산 데이터를 조회할 때 모든 주문/결제 테이블을 다 뒤져 데이터를 만들면 애플리케이션이 힘들어하지 않을까?^^ 몇백만 몇천만 건은 될텐데.. 정산 관련 테이블을 두어서 A업체의 1월 매출은 얼마 비용은 얼마 이런식으로 저장만 해놓으면, row 1개씩 9개(1~9월)만 찾으면 된다. 이 정산 관련 테이블에 한 달에 한번씩 데이터 넣는 작업을 배치를 통해 할 수 있다.

3. 배치 설계 시 고려사항
첫째, 사용성(usability),유지보수성(maintainability),확장성(extensibility)이다.

둘째, 대용량성?(scalability)이다.

셋째, 가용성(availability)이다.

넷째, 보안(security)이다.

4. 자바 배치를 사용하는 이유, 스프링 배치를 사용하는 이유
첫째, maintainability  - 웹과 다르게 배치는 트렌디하지 않고 스태틱함. 스프링과 자바의 궁합이 좋아 스프링 장점(테스트, 추상화)도 취할 수 있다.

둘째, Flexibility - 다른언어는 JVM이 없으니까 운영체제에 독립적이지 않다. “Write once, run anywhere”

셋째, Scalability

넷째, Development resources

다섯째, Support - 자바 개발자들이 많으니까

여섯째, Cost - 하드웨어, 소프트웨어 라이센스, 급여, 컨설팅 비용 등

### Chapter 1: Spring Batch 101
1. Job : 배치 프로세스 단위

2. Step : Job 내의 처리 프로세스로, 각각 ItemReader/ItemProcessor/ItemWriter로 구성되어 있다.

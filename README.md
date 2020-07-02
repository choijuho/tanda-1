Tanda(택시예약 시스템)
======================
본 시스템은 MSA/DDD/Event Storming/EDA 를 포괄하는 분석/설계/구현/운영 전단계를 커버하도록 구성한 시스템입니다.
  
## Table of contents

 * 택시예약관리 시스템
  * 서비스 시나리오
  * 체크포인트
  * 분석/설계
  * 구현:
   * DDD 의 적용
   * 폴리글랏 퍼시스턴스
   * 폴리글랏 프로그래밍
   * 동기식 호출 과 Fallback 처리
   * 비동기식 호출 과 Eventual Consistency
 *운영
  * CI/CD 설정
  * 동기식 호출 / 서킷 브레이킹 / 장애격리
  * 오토스케일 아웃
  * 무정지 재배포
  
 ## 소스코드
 ---
 https://github.com/tanda-ms
 
 ## 서비스 시나리오
 
 ####기능적 요구사항
 
 - 고객이 택시를 요청한다
 - 기사가 배차를 승낙한다
 - 택시가 배차 된다
 - 배차가 되면 고객에게 도착 시간을 알린다
 - 가용한 차량이 없으면 고객의 요청이 취소된다
 - 차량에 탑승하면 운행중으로 상태가 바뀐다
 - 차량에서 하차하면 운행종료로 상태가 바뀐다
 - 하차시 요금이 자동 결제 된다
 - 고객은 배차 요청을 탑승전까지 취소할 수 있으며 택시가 배차상태라도 배차가 취소된다
 
#### 비기능 요구사항
 
  1. 트랜잭션
   고객 배차 취소 요청은 비동기 방식이나, Saga Pattern을 적용하여 최종 일관성을 유지한다.  
  2. 장애격리
   - 결제시스템(Sync연동)이 과중되면 Circuit breaker(FeignClient, Hystrix) 동작 및 운행종료(fallback)로 넘어가지 않는다.
   - 요금 결제가 되지 않으면 운행종료로 넘어가지 않는다 (sync호출)
  3. 성능 
   - 서포트 기능(CPQR) 이 수행되지 않더라도 차량 요청 및 배차는 365일 24시간 받을 수 있어야 한다      Async (event-driven), Eventual Consistency
   - 가용한 차량이 없으면 사용자를 잠시동안 받지 않고 잠시후에 호출하도록 유도 (circuit breaker)
  - 차량운행 상태의 변경시 알림을 준다 (Event driven)
    1 배차(비가용)
    2 운행시작(비가용)
    3 운행종료(가용)
  4. 기타
   - 결제(Sync) 외 비동기
   
 ####  KPI 
  1. 예약 서비스(book)
   - 타 시스템의 장애 여부와 상관없이 택시 요청에 대한 응답을 놓치지 않는다

 2. 차량 운행 서비스 (Taxideploy)
   - 모든 요청에 대해 1분이내 배차/재요청 응답한다.

 3. CORS
   - 예약 배차 관련 모든 상태 정보를 전달한다

   




 
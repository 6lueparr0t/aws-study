확장성(Scalability)과 고가용성(High Availability)
- 확장성 : 수직 확장성과 탄력성이라고 불리는 수평확장성
  - 수직 : t2.micro -> t2.large
    - 신입 개발자에서 시니어 개발자로 바꾸는 것
  - 수평 : t2.micro -> t2.micro + t2.micro
    - 개발자 한명에서 개발자 두명으로 늘리는 것
  - 스케일아웃 (scale out) : 인스턴스 수를 늘리는 것
  - 스케일인 (scale in) : 인스턴스 수를 줄이는 것
- 고가용성 : 콜센터 하나가 멈춰도 또 다른 콜센터가 동작하는 것
  - Auto Scaling Group multi AZ
  - Load Balancer multi AZ
  - 오토스케일링 그룹이나 로드밸런서에 적용 가능함

로드밸런싱
- 서버 혹은 서버셋으로 트래픽을 백엔드나 다운스트림 EC2 인스턴스 또는 서버들로 전달하는 역할을 함
- 부하를 다수의 다운스트림 인스턴스로 분산하기 위해서 사용
- 애플리케이션에 단일 액세스 지점(DNS)을 노출해서 다운스트림 인스턴스의 장애를 원할히 처리 가능함
  - 다운스트림 : Server to Local
  - 말이 어렵지만 아무튼 연결된 여러 인스턴스 혹은 서버로 분산시켜 처리하는 것

ELB (Elastic Load Balancer)
- 순서대로 CLB, ALB, NLB, GLB 가 있음
- CLB (Classic)
  - HTTP, HTTPS, TCP, SSL (secure TCP)
  - 2009년에 출시한 오래된 타입 (v1 - old generation)
- ALB (Application)HTTP 전용 로드 밸런서
  - 2016년에 출시 (v2 - new generation)
  - TCP, TLS (secure TCP), UDP
  - HTTP에서 HTTPS로 트래픽을 자동 리다이렉트하려는 경우 로드 밸런서 레벨에서 가능
  - path, hostname, query string 으로도 분기 가능하기에 MSA 에 적합함
  - 타겟그룹으로 EC2 instances, ECS tasks, Lambda functions, 외부 사설 IP 등에서 사용 가능
  - 고정된 hostname 을 사용하고, X-Forwarded-For / Port / Proto 를 사용해서 클라이언트의 정보를 얻는다.
- NLB (Network)
  - 2017년에 출시 (v2 - new generation)
  - TCP, TLS (secure TCP), UDP
  - ALB 보다 고성능이며 지연시간이 훨씬 짧음
  - 외부 가용 영역 당 1개의 고정 IP 를 노출함
- GWLB (Gateway)
  - 2020년에 출시
  - EC2, IP Address
  - Layer3 : IP Protocol
  - 모든 트래픽이 방화벽을 통과하게 하거나 침임 탐지 및 방지 시스템에 사용함
    - 요청 받은 모든 트래픽을 먼저 검사 & 분석하여 허용 가능한 트래픽만 인입시킴
    - 네트워크 트래픽을 분석한다는 것이 중요
  - 투명한 네트워크 게이트웨이 : 모든 트래픽이 하나의 입구/출구를 통함
  - 로드 밸런싱 : 대상 그룹의 가상 어플라이언스 집합에 트래픽을 분산함
  - GENEVE (6081번 포트) 프로토콜을 사용해야함

고정 세션 & 세션 밀접성 (Sticky Sessions & Session Affinity)
- Application-based & Duration-based
- 이 기능을 활성화하면 접속자가 특정 인스턴스에만 접근하도록 설정할 수 있음
- 쿠키 이름으로 AWSALB, AWSALBAPP, AWSALBTG 를 예약어로 사용
- 동일한 클라이언트에 대한 트래픽이 항상 동일한 대상(예: EC2 인스턴스)으로 리디렉션되도록 하고, 클라이언트가 세션 데이터를 잃지 않도록 도와줌

교차 영역 로드 밸런싱 (Cross-Zone Load Balancing)
- 2개의 가용 영역에서 한 곳은 인스턴스가 2개, 다른 한 곳은 8개가 있을 때 가용 영역당 50% 씩 트래픽을 보내지만 각 인스턴스는 10%의 트래픽만을 부담함
- 즉, 가용 영역이 달라도 트래픽이 고르게 분배되도록 하는 기능
- ALB 는 늘 활성화 되어 있으며 해제할 수 없음 (비용 없음)
- NLB 는 비활성화 되어 있으며, 설정 시 비용 부담
- CLB 는 비활성화 되어 있지만, 설정해도 비용 없음

SNI (Server Name Indicator)
- 한 웹 서버에 다수의 SSL 인증서를 발급해 단일 웹 서버가 여러 개의 SSL 을 가질 수 있음
- 대상 그룹(Target Group)에 SSL 인증서를 적용 시키고, 해당 도메인으로 요청이 오면 대상 그룹으로 전달
- CLB는 SSL 하나만 사용 가능하고, ALB & NLB 는 SNI 및 다중 SSL 인증서를 지원

연결 드레이닝 (Connection Draining)
- 연결 드레이닝 Connection Draining (CLB)
- 등록 취소 지연 Deregistraion Delay (ALB & NLB)
- 1 to 3600 초를 대기하며 기본은 300초, 5분
- 0 이면 지연(드레이닝) 없이 바로 비활성화

오토스케일링 그룹
- 스케일링 저책에 따라 인스턴스를 자동으로 생성해주는 서비스
- CloudWatch 알람을 기반으로 오토스케일링 그룹을 스케일링 하는 것이 가능함
- CPU 사용량, 인스턴스 개수, 평균 네트워크 인/아웃, 커스텀 메트릭 (연결된 유저 숫자) 등
- 인스턴스를 생성하기 위한 템플릿이 필요함
- 오토 스케일링 그룹에 IAM 을 지정하면 생성되는 인스턴스에 자동으로 지정 됨

오토스케일링 그룹 - 동적 스케일링 정책
- Traget Tracking 스케일링 : 그룹 평균 CPU 사용률을 추적하는 등 기준선을 세움
- Simple / Step 스케일링 : 복잡한 설정, 특정 기준 시 인스턴스를 추가하거나 삭제하는 등
- Scheduled Actions : 오후 5시마다 자동으로 10까지 늘려라 같은 스케줄링 작업
- 좋은 지표 :
  - CPU 사용률, 타겟 요청 수, 평균 네트워크 인아웃(업&다운로드), 커스텀메트릭(CloudWatch)
- 스케일링 쿨다운 : 스케일링 이후 300초(5분)의 휴지 기간을 갖고, 휴지 기간에는 인스턴스를 실행 또는 종료할 수 없음
  - AMI 를 이용하여 인스턴스 구성 시간을 단축하는 것을 추천함


스트레스 테스트
```bash
sudo amazon-linux-extras install epel -y
sudo yum install stress -y
stress -c 4
```

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
- CLB : TCP (Layer 4), HTTP & HTTPS (Layer 7)
- ALB : HTTP 전용 로드 밸런서
  - HTTP에서 HTTPS로 트래픽을 자동 리다이렉트하려는 경우 로드 밸런서 레벨에서 가능
  - path, hostname, query string 으로도 분기 가능하기에 MSA 에 적합함
  - 타겟그룹으로 EC2 instances, ECS tasks, Lambda functions, 외부 사설 IP 등에서 사용 가능
  - 고정된 hostname 을 사용하고, X-Forwarded-For / Port / Proto 를 사용해서 클라이언트의 정보를 얻는다.

- NLB : 
- GLB : 
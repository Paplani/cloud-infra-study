# cloud-infra-study

클라우드 엔지니어 / 네트워크 엔지니어를 목표로  
AWS, Linux 기반 인프라를 직접 구축하고 운영하며 학습하는 저장소입니다.  

이 저장소는 단순 이론 정리가 아니라,  
**직접 구축 → 문제 발생 → 분석 → 해결 → 문서화** 과정을 기록하는 실습형 포트폴리오입니다.

---

## 1. 목표

- Linux 서버 운영 기본기 이해
- AWS 인프라 구성 경험 축적
- 네트워크 흐름 및 보안 구조 이해
- Load Balancing / Auto Scaling 기반 고가용성 구조 이해
- 장애 발생 시 점검 및 복구 흐름 익히기
- 장기적으로 DevOps / SRE 방향으로 확장

---

## 2. 현재까지 진행한 실습

### Linux / EC2 기초
- EC2 Ubuntu 인스턴스 생성
- SSH 접속 및 서버 접근
- nginx 설치 및 실행
- Linux 기본 명령어 실습 (ps, df, free 등)
- 웹 서버 기본 페이지 수정

---

### 운영 / 보안
- Security Group 인바운드 규칙 설정
- SSH / HTTP 접근 제어
- 포트 기반 접근 구조 이해

---

### 네트워크
- VPC / Subnet 구성
- Public / Private 네트워크 구조 이해
- NAT Gateway 개념 및 구성
- 인터넷 접근 흐름 이해

---

### 가용성 / 확장성
- Application Load Balancer(ALB) 구성
- Target Group 생성 및 Health Check 확인
- EC2 2대 트래픽 분산 확인
- nginx 중지 시 unhealthy 처리 및 자동 제외 확인

- Auto Scaling Group 구성
- Launch Template + User Data 기반 자동 서버 구성
- CPU 기반 Scale Out / Scale In 확인
- 장애 발생 시 자동 복구 (Failover) 확인

---

### Monitoring
- CloudWatch를 통한 CPU 사용률 확인
- Auto Scaling Metrics 활성화
- 지표 기반 동작 구조 이해
- Metrics 지연 및 No data 문제 경험

---

## 3. 실습 문서

### Linux / EC2
- [EC2 + nginx](docs/01-ec2-nginx.md)
- [File Permission](docs/02-file-permission.md)
- [EC2 Rebuild](docs/03-ec2-rebuild.md)

---

### Auto Scaling
- [Auto Scaling Setup](docs/auto-scaling/auto-scaling-setup.md)
- [Scale Test](docs/auto-scaling/scale-test.md)
- [Failover Test](docs/auto-scaling/failover-test.md)
- [Monitoring](docs/auto-scaling/monitoring.md)

---

## 4. 실습 환경

- Cloud: AWS  
- OS: Ubuntu  
- Web Server: nginx  
- Monitoring: CloudWatch  
- Load Balancing: Application Load Balancer  
- Scaling: Auto Scaling Group  
- Version Control: Git / GitHub  

---

## 5. 학습 방식

이 저장소는 이론을 먼저 정리하기보다  
실습을 통해 문제를 경험하고 해결하는 방식으로 진행합니다.

정리 기준:

- 무엇을 구축했는가  
- 어떻게 구성했는가  
- 어떤 문제가 발생했는가  
- 어떻게 점검하고 해결했는가  
- 운영 관점에서 왜 중요한가  

---

## 6. 이번 단계에서 구성한 아키텍처

- EC2 기반 nginx 웹 서버  
- Application Load Balancer  
- Target Group + Health Check  
- Auto Scaling Group  
- Launch Template + User Data  
- CloudWatch Monitoring  

---

## 7. 이번 실습에서 배운 핵심

- Auto Scaling은 개별 인스턴스가 아닌 그룹 기준으로 동작한다.
- Scale Out은 즉시 발생하지 않고 CloudWatch 지표를 기반으로 지연 후 발생한다.
- ALB는 단순한 분산기가 아니라 상태 기반으로 트래픽을 제어한다.
- Health Check 실패는 서버 상태, 서비스, 네트워크 문제 모두와 연결된다.
- CloudWatch는 실시간이 아닌 지표 기반 모니터링 시스템이다.
- 장애 발생 시 기존 인스턴스를 복구하는 것이 아니라 새로운 인스턴스를 생성한다.

---

## 8. 다음 확장 방향

- HTTPS + ACM 적용  
- Private Subnet + Bastion 구조  
- 3-Tier Architecture 구성 (Web / App / DB 분리)  
- RDS 도입  
- CI/CD 환경 구성  
- Terraform 기반 인프라 관리  

---

## 9. Repository 목적

이 저장소의 목적은 단순한 학습 기록이 아니라,  

클라우드 인프라를 직접 구축하고 운영할 수 있는 능력을  
실습 기반으로 증명하기 위한 포트폴리오를 만드는 것입니다.

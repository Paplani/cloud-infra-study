# Week 01 Study Log

## 목표

- GitHub 학습 레포 구축
- Linux 기본 명령어 학습
- AWS 콘솔 구조 이해

---

## 2026-03-18

### 진행

- GitHub 레포 생성
- README 확인
- .gitignore 추가
- docs / theory / practice / troubleshooting / log 구조 만들기

### 배운 점

GitHub에서 파일 경로를 활용하면 폴더와 파일을 함께 만들 수 있다.

---

## 2026-03-19

### 진행

- EC2 인스턴스 생성
- SSH 접속 성공
- nginx 설치 및 실행
- 웹 브라우저 접속 성공

### 배운 점

서버를 생성하고 외부에서 접속 가능한 웹 서버를 직접 구축할 수 있었다.

---

## 2026-03-24

### Level 2 ~ Level 3

- Linux 파일 권한 (chmod) 실습 완료  
  → 권한 구조 (r, w, x / 644, 755) 이해  
  → nginx 파일 권한 확인

- EC2 인스턴스 재생성 반복  
  → EC2 생성 → SSH → nginx 설치 흐름 익숙해짐

- Security Group 실습 진행  
  → HTTP(80) 차단 시 웹 접속 불가 확인  
  → HTTP 복구 시 정상 동작 확인  
  → SSH(22) 차단 시 접속 불가 확인  

### 느낀 점

- 서버가 정상이어도 포트가 막히면 접속이 안 된다는 것을 직접 확인함  
- 접속 문제는 서버 문제인지 네트워크 문제인지 구분해야 한다고 느꼈음  
- Security Group이 실제 서비스 가용성에 큰 영향을 준다는 것을 이해함

---

## 2026-03-25

### Level 3

- curl을 이용한 요청 테스트 진행  
  → 내부 요청과 외부 요청의 차이 이해  

- Security Group 동작 방식 정리  
  → 외부 요청만 영향을 받는다는 점 확인  

- HTTP / HTTPS 개념 학습  
  → HTTPS는 인증서와 설정이 필요하다는 점 이해  

- self-signed 인증서로 HTTPS 적용  
  → nginx 443 설정 및 접속 확인  

- 403 Forbidden 문제 해결  
  → index 설정 문제 원인 파악  

- HTTP → HTTPS 리다이렉트 설정  
  → 실제 운영 구조 이해  

### 느낀 점

- 요청이 서버까지 도달했는지 확인하는 것이 중요하다  
- 접속 문제는 서버와 네트워크를 구분해서 봐야 한다  
- 문제를 순서대로 확인하면서 해결하는 방식이 중요하다고 느꼈다

---

## 2026-03-26

### Level 5

- VPC 내부에 Private Subnet 생성  
- NAT Gateway를 통해 Private Subnet의 인터넷 연결 구성  
- Route Table 설정을 통해 Private → NAT → Internet 흐름 구성  

- Private EC2 생성 (Public IP 없음)  
  → 외부에서 직접 접속 불가 확인  

- Bastion Host 구조 구현  
  → Public EC2를 통해 Private EC2 접속 성공  

- SSH 키 전달 과정 이해 (scp 사용)  
- EC2 내부 vs 로컬 환경 차이 이해  

### 느낀 점

- AWS에서는 서버보다 네트워크 구조가 더 중요하다고 느꼈다  
- Private / Public 분리를 통해 보안 구조를 직접 이해할 수 있었다  
- 접속 문제는 네트워크, 인증, 실행 위치를 나눠서 봐야 한다  

---

## 2026-03-29

### Level 6

- Application Load Balancer(ALB) 구조 구성
- 서로 다른 AZ에 Public Subnet 2개 생성 및 구성
- EC2 2대를 Target Group으로 묶어 트래픽 분산 구조 구현

- ALB 생성 후 DNS를 통해 외부 접속 확인
- 새로고침 시 server-1 / server-2가 번갈아 응답되는 것 확인

- 서버 1대의 nginx를 중지하여 장애 상황 테스트
  → Target Group에서 unhealthy 상태 확인
  → ALB가 정상 서버(server-2)로만 요청 전달하는 것 확인
  
## 2026-03-31

### Level 7 ~ 8

- Launch Template을 생성하여 EC2 인스턴스 자동 생성 기준 구성
- Auto Scaling Group을 생성하고 Min / Desired / Max 값을 설정하여 기본 인스턴스 수 유지 구조 구성
- ALB의 Target Group과 Auto Scaling Group을 연결하여 트래픽 분산 구조 완성

- CPU 부하를 발생시키기 위해 stress 명령어 실행
  → 인스턴스 수가 증가하는 Scale Out 동작 확인
- 부하 종료 후 인스턴스 수가 감소하는 Scale In 동작 확인

- EC2 인스턴스를 강제로 종료하여 장애 상황 테스트
  → Auto Scaling Group이 새로운 인스턴스를 자동으로 생성하는 것 확인
  → Target Group에 자동 등록 및 healthy 상태로 전환 확인

- CloudWatch를 통해 EC2 CPU 사용률 확인
- Auto Scaling Metrics를 활성화하여 인스턴스 수 변화 지표 확인
- Metrics 활성화 이전 데이터는 표시되지 않는 것을 확인
- CloudWatch 지표는 실시간이 아닌 일정 시간 지연 후 반영되는 것 확인

### 느낀 점

- Auto Scaling은 단순히 인스턴스를 늘리는 기능이 아니라 장애 복구까지 포함된 구조라는 것을 이해했다
- Scale Out은 개별 인스턴스가 아닌 전체 평균 CPU를 기준으로 동작한다는 점이 인상적이었다
- CloudWatch는 단순 모니터링 도구가 아니라 Auto Scaling의 동작 기준이 되는 핵심 요소라는 것을 이해했다
- 실제로 부하를 발생시키고 장애를 만들어보면서 인프라 구조를 더 깊게 이해할 수 있었다

- 서버 복구 후 다시 healthy 상태로 전환되는 것 확인

### 느낀 점

- ALB는 단순한 트래픽 분산이 아니라 장애 대응까지 포함된 구조라는 것을 이해했다
- 서버 여러 대를 운영할 때는 네트워크 구조와 보안 설정이 매우 중요하다는 것을 체감했다
- 고가용성 구조는 실제 운영에서 필수적인 개념이라는 것을 확인했다

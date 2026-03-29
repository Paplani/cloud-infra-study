# Application Load Balancer (ALB) 실습

## 1. 실습 목적

Application Load Balancer(ALB)를 이용하여  
여러 EC2 인스턴스에 트래픽을 분산하는 구조를 구성했다.

또한 서버 하나가 중지되었을 때  
ALB가 정상 서버로만 요청을 전달하는 동작을 확인했다.

이번 실습의 목적은  
**고가용성을 고려한 서비스 구조와 장애 대응 흐름을 이해하는 것**이다.

---

## 2. 실습 환경

- Cloud: AWS
- Network:
  - VPC
  - Public Subnet (서로 다른 AZ 2개)
  - Internet Gateway
- Compute:
  - EC2 2대 (nginx)
- Load Balancing:
  - Application Load Balancer
  - Target Group
- Access:
  - HTTP

---

## 3. 실습 목표

- ALB 구조 이해
- Target Group의 역할 이해
- 트래픽 분산 동작 확인
- Health Check 기반 장애 대응 이해
- 단일 서버 구조와 다중 서버 구조의 차이 이해

---

## 4. 핵심 개념

### Application Load Balancer (ALB)

ALB는 외부 요청을 받아 여러 서버에 분산하는 역할을 한다.  
HTTP/HTTPS 계층에서 동작하며, 요청을 Target Group에 등록된 서버로 전달한다.

즉, 사용자는 하나의 진입점(ALB)만 보지만,  
실제로는 여러 서버가 뒤에서 요청을 나누어 처리한다.

### Target Group

Target Group은 트래픽을 전달받는 서버 목록이다.  
ALB는 EC2 인스턴스를 직접 대상으로 삼는 것이 아니라,  
Target Group을 기준으로 요청을 전달한다.

또한 서버 상태를 Health Check로 확인하고  
정상 상태인 서버만 요청 처리에 참여시킨다.

### Health Check

ALB는 주기적으로 각 서버에 요청을 보내 상태를 확인한다.

- 정상 응답 → healthy
- 응답 실패 → unhealthy

이를 통해 장애가 발생한 서버를 자동으로 제외하고,  
정상 서버에만 트래픽을 전달할 수 있다.

### 고가용성 (High Availability)

서버를 한 대만 운영하면  
그 서버가 중지되는 순간 서비스 전체가 중단된다.

반면 여러 서버를 두고 ALB를 사용하면  
한 서버에 문제가 생겨도 다른 서버가 계속 요청을 처리할 수 있다.

---

## 5. 전체 구조

```text
[ User (Browser) ]
        ↓
[ ALB (Application Load Balancer) ]
        ↓
[ Target Group ]
    ├── EC2 (web-server-1, AZ-a)
    │      └── nginx (server-1)
    │
    └── EC2 (web-server-2, AZ-b)
           └── nginx (server-2)
```

네트워크 관점에서는 아래와 같은 구조로 구성했다.

```text
Internet
  ↓
Internet Gateway
  ↓
Public Subnet (AZ-a) ── EC2 (server-1)
Public Subnet (AZ-b) ── EC2 (server-2)
  ↓
ALB (두 subnet에 연결)
```

즉,  
서로 다른 AZ에 서버를 배치하고  
그 앞단에 ALB를 두어 요청을 분산하는 구조이다.

---

## 6. 작업 과정

### 6-1. EC2 2대 생성 및 nginx 설정

→ 트래픽을 분산할 대상 서버 준비

각 서버에서 실행:

```bash
sudo apt update
sudo apt install nginx -y
```

서버 구분을 위해 index 설정:

server-1:
```bash
echo "server-1" | sudo tee /var/www/html/index.html
```

server-2:
```bash
echo "server-2" | sudo tee /var/www/html/index.html
```

이 과정에서  
단순히 서버를 만드는 것이 아니라,  
**각 서버의 응답을 구분하여 로드밸런싱 동작을 직접 확인할 수 있도록 준비했다.**

### 6-2. Target Group 생성

→ EC2 인스턴스를 하나의 그룹으로 묶는 단계

설정한 주요 항목:

- Target type: Instances
- Protocol: HTTP
- Port: 80
- VPC: my-vpc
- Health Check Path: `/`

이후 두 인스턴스를 Target Group에 등록했다.

이 과정에서  
ALB가 EC2를 직접 관리하는 것이 아니라,  
**Target Group 단위로 서버를 묶고 상태를 관리한다는 구조를 이해했다.**

### 6-3. ALB 생성

→ 외부 요청을 받아 Target Group으로 전달하는 진입 지점 생성

설정한 주요 항목:

- Type: Application Load Balancer
- Scheme: Internet-facing
- VPC: my-vpc
- Subnet: 서로 다른 AZ의 public subnet 2개

Listener 설정:

```text
HTTP : 80 → my-target-group
```

이 과정에서  
ALB는 단순히 요청을 받는 장치가 아니라,  
**어떤 Target Group으로 요청을 전달할지 결정하는 중심 지점**이라는 점을 이해했다.

### 6-4. Target Group 상태 확인

→ ALB와 서버 간 연결 상태 확인

- server-1 → healthy
- server-2 → healthy

이 단계에서  
**ALB가 각 서버에 Health Check를 보내고 정상 여부를 판단한다는 점**을 확인했다.

### 6-5. 브라우저 테스트

→ 트래픽 분산 동작 확인

```text
http://<ALB-DNS>
```

새로고침 시:

- server-1
- server-2

번갈아 응답 확인

이 과정에서  
사용자는 하나의 주소만 접속하지만,  
**실제 요청은 여러 서버로 분산된다는 점**을 직접 확인했다.

### 6-6. 서버 장애 상황 테스트

→ 장애 발생 시 ALB 동작 확인

server-1에서 실행:

```bash
sudo systemctl stop nginx
```

이 과정은  
단순 테스트가 아니라  
**실제 운영에서 특정 서버가 응답하지 않는 상황을 가정한 실습**이다.

### 6-7. 상태 변화 확인

→ Target Group 상태 확인

- server-1 → unhealthy
- server-2 → healthy

이 단계에서  
**ALB가 자동으로 장애 서버를 감지하고 요청 대상에서 제외한다는 점**을 확인했다.

### 6-8. 장애 상황에서 트래픽 확인

→ 브라우저 테스트

결과:

- server-2만 응답

이 과정을 통해  
**ALB는 무조건 분산만 하는 것이 아니라, 정상 서버에만 트래픽을 전달한다는 점**을 확인했다.

### 6-9. 서버 복구

```bash
sudo systemctl start nginx
```

→ 다시 healthy 상태로 복구

이 과정에서  
서버가 복구되면 다시 로드밸런싱 대상에 포함된다는 점을 확인했다.

---

## 7. 확인 결과

- ALB를 통해 요청이 여러 서버로 분산됨
- Target Group에서 서버 상태를 자동으로 확인함
- 서버 하나가 중지되면 해당 서버는 자동으로 제외됨
- 정상 서버만 계속 요청을 처리함
- 서버 복구 후 다시 로드밸런싱 대상에 포함됨

즉,  
**단순 분산뿐 아니라 장애 상황에서도 서비스가 계속 유지되는 구조를 확인했다.**

---

## 8. 운영 관점에서 중요한 이유

클라우드 운영 / 시스템 엔지니어 관점에서  
단일 서버 구조는 서버 장애 발생 시 서비스가 즉시 중단될 수 있다는 한계가 있다.

ALB를 사용하는 구조는 아래 측면에서 중요하다.

- 여러 서버에 요청을 분산할 수 있다
- 특정 서버 장애가 전체 서비스 중단으로 이어지는 것을 줄일 수 있다
- 비정상 서버를 자동으로 트래픽 대상에서 제외할 수 있다
- 복구된 서버를 다시 자동으로 서비스 대상에 포함할 수 있다
- 이후 Auto Scaling과 결합해 확장 가능한 구조로 발전시킬 수 있다

즉,  
이번 실습은 단순 웹 서버 운영에서 한 단계 나아가  
**가용성을 고려한 운영 구조를 직접 만들어본 경험**이라고 볼 수 있다.

---

## 9. 발생할 수 있는 문제

### 9-1. ALB DNS로 접속이 안 되는 경우

가능한 원인:

- ALB Security Group에서 HTTP(80) 미허용
- ALB 상태가 아직 active가 아님
- HTTP 대신 HTTPS로 접속 시도

---

### 9-2. Target Group 상태가 Unhealthy인 경우

가능한 원인:

- nginx 미실행
- EC2 Security Group에서 HTTP(80) 미허용
- Health Check 요청에 정상 응답하지 못함

---

### 9-3. Target Group에 인스턴스가 보이지 않는 경우

가능한 원인:

- Target Group과 EC2의 VPC 불일치
- EC2를 잘못된 subnet/VPC에 생성함
  
---

## 10. 트러블슈팅 (실습 중 발생 문제)

### 10-1. Target Group 상태가 Unused로 표시됨

문제:

- Target Group에 인스턴스를 등록했지만 상태가 `Unused`로 표시됨

원인:

- ALB와 Target Group이 아직 연결되지 않은 상태

해결:

- ALB 생성 시 Listener 설정에서 Target Group을 연결

```text
HTTP : 80 → my-target-group
```

---

### 10-2. ALB DNS로 접속했을 때 페이지가 열리지 않음

문제:

- ALB DNS 주소로 접속해도 페이지가 표시되지 않음

원인:

- ALB Security Group에서 HTTP(80) 포트가 허용되지 않음

해결:

- ALB Security Group 인바운드 규칙에 HTTP(80) 추가

```text
Type: HTTP
Port: 80
Source: Anywhere-IPv4
```

---

### 10-3. Target Group 생성 시 인스턴스가 보이지 않음

문제:

- Register targets 단계에서 EC2 인스턴스가 목록에 나타나지 않음

원인:

- EC2와 Target Group의 VPC가 서로 다름
- EC2를 생성할 때 VPC를 지정하지 않음

해결:

- 동일한 VPC(my-vpc)에 EC2를 새로 생성
- 이후 Target Group에 다시 등록

---

### 10-4. ALB 생성 시 subnet을 2개 선택할 수 없음

문제:

- ALB Network 설정에서 subnet을 하나만 선택 가능

원인:

- 동일한 AZ에 있는 subnet만 존재
- ALB는 서로 다른 AZ의 subnet을 요구

해결:

- 다른 AZ에 public subnet 추가 생성
- 해당 subnet에 EC2 생성 후 Target Group에 등록

---

## 11. 배운 점

- ALB는 단순히 요청을 나누는 장치가 아니라, 서버 상태를 판단하고 정상 서버에만 요청을 전달하는 장치라는 점을 이해했다.
- Target Group이 ALB와 EC2 사이의 핵심 연결 지점이라는 것을 알게 되었다.
- 로드밸런싱은 단순히 서버를 여러 대 두는 것에서 끝나는 것이 아니라, Health Check와 함께 동작해야 실제 운영 의미가 있다는 점을 이해했다.
- 서버 한 대 구조는 단순하지만 장애에 매우 약하고, ALB 구조는 고가용성을 위한 기본이라는 점을 체감했다.
- Security Group, VPC, subnet, AZ 같은 네트워크 설정이 ALB 동작에 직접적인 영향을 준다는 점을 다시 확인했다.
- 장애 상황에서 unhealthy 상태를 직접 확인하고, ALB가 정상 서버로만 요청을 전달하는 과정을 보면서 “운영 관점의 구조”를 더 선명하게 이해할 수 있었다.

---

## 12. 다음 단계

- Auto Scaling Group
- Launch Template
- 서버 자동 확장 구조

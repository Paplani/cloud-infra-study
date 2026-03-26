# VPC / Subnet / EC2 네트워크 구성 실습

## 1. 실습 목적

AWS에서 VPC와 Subnet을 직접 구성하고,  
그 위에 EC2 인스턴스를 생성하여 인터넷 연결이 가능한 구조를 만들었다.

이번 실습의 목적은  
단순히 서버를 생성하는 것이 아니라,  
**서버가 동작하는 네트워크 구조를 직접 설계하고 이해하는 것**이다.

특히 아래 내용을 중점적으로 확인했다.

- VPC의 역할과 필요성 이해
- Subnet이 실제 서버가 배치되는 위치라는 점
- Internet Gateway와 Route Table이 인터넷 연결에 어떤 역할을 하는지
- EC2가 네트워크 구성 위에서 동작한다는 점

---

## 2. 실습 환경

- Cloud: AWS
- Network:
  - VPC
  - Subnet
  - Internet Gateway
  - Route Table
- Compute: EC2 (Ubuntu)
- Access:
  - SSH
  - 인터넷 요청 (ping, curl)

---

## 3. 실습 목표

이번 실습에서 확인한 목표는 다음과 같다.

- VPC를 직접 생성하고 IP 범위 설정
- Subnet 생성 및 VPC 내부 구조 이해
- Internet Gateway 연결을 통한 외부 네트워크 연결
- Route Table 설정을 통한 트래픽 흐름 이해
- EC2를 Subnet에 배치하고 실제 인터넷 연결 확인

---

## 4. 핵심 개념

### VPC (Virtual Private Cloud)

VPC는 AWS에서 제공하는 **가상 네트워크 공간**이다.

사용자가 직접 IP 범위를 설정하고,  
서버를 어떤 네트워크 구조 위에서 운영할지 설계할 수 있다.

즉,  
**클라우드에서의 “내 전용 네트워크”라고 볼 수 있다.**

---

### Subnet

Subnet은 VPC 내부에서 IP를 나눈 영역이다.

EC2는 반드시 특정 Subnet 안에 생성되며,  
이 Subnet이 인터넷과 연결되어 있는지에 따라  
외부 접근 가능 여부가 결정된다.

---

### Internet Gateway (IGW)

Internet Gateway는 VPC를 외부 인터넷과 연결해주는 구성 요소이다.

IGW가 없다면  
VPC 내부 리소스는 외부 인터넷과 통신할 수 없다.

---

### Route Table

Route Table은 네트워크 트래픽의 경로를 결정하는 규칙이다.

예:

```text
0.0.0.0/0 → IGW
```

의미:

→ 모든 외부 요청을 Internet Gateway로 전달

즉,  
**인터넷으로 나가는 길을 만들어주는 역할**이다.

---

## 5. 전체 구조

이번 실습에서 구성한 구조는 다음과 같다.

```text
인터넷
  ↑
Internet Gateway
  ↑
Route Table (0.0.0.0/0)
  ↑
Public Subnet
  ↑
EC2
```

---

## 6. 작업 과정

### 6-1. VPC 생성

- Name: my-vpc
- CIDR: 10.0.0.0/16

→ 전체 네트워크 범위 설정

---

### 6-2. Subnet 생성

- Name: public-subnet
- CIDR: 10.0.1.0/24
- VPC: my-vpc

→ VPC 내부에서 사용할 IP 범위 분리

---

### 6-3. Internet Gateway 생성 및 연결

- Name: my-igw
- VPC에 Attach

→ 외부 인터넷과 연결되는 “출구” 생성

---

### 6-4. Route Table 설정

- Destination: 0.0.0.0/0
- Target: Internet Gateway

→ 모든 외부 트래픽을 IGW로 전달

또한 Subnet을 이 Route Table에 연결

---

### 6-5. EC2 생성 (중요)

- VPC: my-vpc
- Subnet: public-subnet
- Public IP: Enable
- Security Group:
  - SSH (22)
  - HTTP (80)

→ 네트워크 위에 실제 서버 배치

---

### 6-6. SSH 접속 확인

```bash
ssh -i my-key.pem ubuntu@<public-ip>
```

---

### 6-7. 인터넷 연결 테스트

```bash
ping google.com
```

또는

```bash
curl google.com
```

---

## 7. 확인 결과

- EC2 생성 및 SSH 접속 성공
- EC2에서 외부 인터넷 접속 성공
- 구성한 네트워크 구조가 정상적으로 동작함을 확인

---

## 8. 핵심 이해

이번 실습에서 가장 중요하게 이해한 내용은 다음과 같다.

- EC2는 단순히 생성하는 것이 아니라 네트워크 위에 배치된다
- Subnet이 인터넷과 연결되어 있어야 외부 접근이 가능하다
- Internet Gateway는 외부로 나가는 통로 역할을 한다
- Route Table은 트래픽이 어디로 갈지 결정한다

즉,

**서버가 먼저가 아니라 네트워크 구조가 먼저 설계되어야 한다**

---

## 9. 운영 관점에서 중요한 이유

클라우드 환경에서 서버는 단독으로 존재하지 않는다.

모든 서버는 네트워크 위에서 동작하며,  
접속 문제의 원인은 다음과 같이 다양하게 나뉠 수 있다.

- 서버 문제 (서비스 중지)
- 보안 문제 (Security Group)
- 네트워크 문제 (Route, IGW, Subnet)

이번 실습은 이 중에서  
**네트워크 구조 자체가 서비스 가능 여부를 결정한다는 점을 보여준다.**

---

## 10. 발생할 수 있는 문제

### EC2 접속이 안 되는 경우

가능한 원인:

- Public IP 없음
- SSH 포트 미허용
- 잘못된 Subnet

---

### 인터넷 연결이 안 되는 경우

가능한 원인:

- Internet Gateway 미연결
- Route Table 설정 없음
- Subnet이 Route Table에 연결 안 됨

---

## 11. 트러블슈팅

### 인터넷 연결 안 될 때

확인 순서:

1. Internet Gateway가 VPC에 연결되어 있는지 확인
2. Route Table에 `0.0.0.0/0 → IGW` 설정 확인
3. Subnet이 해당 Route Table에 연결되어 있는지 확인
4. EC2에 Public IP가 할당되어 있는지 확인

---

### SSH 접속 안 될 때

확인 순서:

1. EC2 상태 (Running) 확인
2. Public IP 확인
3. Security Group (22 포트) 확인
4. 키 파일 권한 확인

---

## 12. 배운 점

- AWS에서 서버보다 네트워크 구성이 더 중요하다
- 인터넷 연결은 여러 구성 요소가 함께 동작해야 가능하다
- 구조를 이해하면 문제 원인을 빠르게 찾을 수 있다

---

## 13. 다음 단계

- Private Subnet 구성
- NAT Gateway 이해
- Public / Private 구조 분리

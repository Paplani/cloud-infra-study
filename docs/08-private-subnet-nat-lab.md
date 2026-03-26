# Private Subnet / NAT Gateway / Bastion Host 실습

## 1. 실습 목적

Private Subnet을 구성하고 NAT Gateway를 통해  
외부 인터넷과 통신할 수 있는 구조를 만들었다.

또한 Public EC2를 통해 Private EC2에 접속하는  
Bastion Host 구조를 구현했다.

이번 실습의 목적은  
**보안이 적용된 네트워크 구조에서 서버를 어떻게 운영하는지 이해하는 것**이다.

---

## 2. 실습 환경

- Cloud: AWS
- Network:
  - VPC
  - Public Subnet
  - Private Subnet
  - Internet Gateway
  - NAT Gateway
  - Route Table
- Compute:
  - Public EC2 (Bastion)
  - Private EC2
- Access:
  - SSH
  - 내부 네트워크 통신

---

## 3. 실습 목표

- Private Subnet 개념 이해
- NAT Gateway를 통한 인터넷 연결 구조 이해
- Private EC2 생성 및 외부 접근 제한 확인
- Bastion Host를 통한 내부 서버 접근 구현
- 실습 기반 트러블슈팅 경험

---

## 4. 핵심 개념

### Private Subnet

Private Subnet은 외부 인터넷에서 직접 접근할 수 없는 네트워크 영역이다.

- Public IP 없음
- 외부에서 직접 SSH 불가

하지만 NAT Gateway를 통해  
외부 인터넷으로 나가는 것은 가능하다.

### NAT Gateway

NAT Gateway는 Private Subnet의 인스턴스가  
외부 인터넷으로 나갈 수 있도록 도와주는 구성 요소이다.

- 내부 → 외부 ⭕
- 외부 → 내부 ❌

즉,  
**보안을 유지하면서 필요한 통신만 허용하는 구조**이다.

### Bastion Host

Bastion Host는 Public Subnet에 위치한 서버로,  
Private Subnet에 접근하기 위한 중간 서버 역할을 한다.

접속 흐름:

```
내 PC → Public EC2 → Private EC2
```

---

## 5. 전체 구조

```
인터넷
  ↓
Internet Gateway
  ↓
Public Subnet
  └── Public EC2 (Bastion)
        ↓
     Private Subnet
       └── Private EC2
            ↓
        NAT Gateway
            ↓
        인터넷
```

---

## 6. 작업 과정

### 6-1. Private Subnet 생성

→ 외부와 분리된 네트워크 영역 생성

- Name: private-subnet
- CIDR: 10.0.2.0/24

### 6-2. NAT Gateway 생성

→ Private Subnet이 외부 인터넷으로 나갈 수 있는 경로 생성

- Subnet: public-subnet
- Availability mode: Zonal
- Elastic IP 할당

### 6-3. Private Route Table 설정

→ Private Subnet 트래픽을 NAT Gateway로 전달

- Name: private-rt
- Route:
  - 0.0.0.0/0 → NAT Gateway

→ private-subnet 연결

### 6-4. Private EC2 생성

→ 외부에서 직접 접근할 수 없는 내부 서버 생성

- VPC: my-vpc
- Subnet: private-subnet
- Public IP: Disable

### 6-5. Public EC2 접속 (내 PC → Bastion)

(내 PC PowerShell에서 실행)

```powershell
ssh -i my-key.pem ubuntu@<public-ip>
```

### 6-6. pem 파일 업로드 (내 PC → Public EC2)

(내 PC PowerShell에서 실행)

```powershell
scp -i my-key.pem my-key.pem ubuntu@<public-ip>:~
```

### 6-7. Public EC2에서 Private EC2 접속 준비

(Public EC2 내부에서 실행)

```bash
chmod 400 my-key.pem
```

### 6-8. Private EC2 접속 (Bastion → Private)

(Public EC2 내부에서 실행)

```bash
ssh -i my-key.pem ubuntu@10.0.2.204
```

---

## 7. 확인 결과

- Private EC2는 외부에서 직접 접속 불가
- Public EC2를 통해서만 접속 가능
- Private EC2에서 외부 인터넷 접근 가능 (NAT 구조 정상 동작)

---

## 8. 트러블슈팅

### 8-1. Private EC2 SSH 접속 실패

에러:

```
Permission denied (publickey)
```

원인:

- 키 파일 없이 접속 시도

잘못된 명령:

```bash
ssh ubuntu@10.0.2.204
```

해결:

```bash
ssh -i my-key.pem ubuntu@10.0.2.204
```

### 8-2. scp 실행 위치 오류

에러:

```
No such file or directory
```

원인:

- EC2 내부에서 scp 실행

해결:

```powershell
scp -i my-key.pem my-key.pem ubuntu@<public-ip>:~
```

### 8-3. NAT 설정 시 Subnet 선택 문제

원인:

- UI 구조 이해 부족

해결:

- Availability mode → Zonal → AZ → Subnet 선택

### 8-4. Private EC2 직접 접속 시도

문제:

- Public IP 없음

결론:

- 외부 접속 불가 (정상)

해결:

- Bastion Host 통해 접속

---

## 9. 운영 관점

- Public Subnet → 외부 노출 서버
- Private Subnet → 내부 서버

→ 보안과 접근 제어 분리 가능

---

## 10. 배운 점

- 네트워크 구조가 서버보다 중요하다
- Public / Private 분리가 핵심이다
- 실행 위치(내 PC vs EC2)를 구분하는 것이 중요하다
- 문제를 단계적으로 나눠서 해결해야 한다

---

## 11. 다음 단계

- ALB (Load Balancer) 구성
- 다중 서버 구조
- Auto Scaling

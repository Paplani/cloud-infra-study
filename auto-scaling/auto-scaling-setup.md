# Auto Scaling Setup

---

## 1. 실습 목적  

Auto Scaling Group을 구성하여 트래픽 증가 및 장애 상황에 자동으로 대응할 수 있는 인프라를 구축한다.  

단일 서버 환경에서는 트래픽 증가나 장애 발생 시 서비스가 중단될 수 있기 때문에,  
이를 해결하기 위해 다중 인스턴스 기반의 자동 확장 구조를 구성하는 것을 목표로 한다.

---

## 2. 실습 환경  

- AWS EC2  
- Auto Scaling Group  
- Launch Template  
- Application Load Balancer (ALB)  
- Target Group  
- Ubuntu 22.04  
- nginx  

---

## 3. 실습 목표  

- Launch Template 생성  
- Auto Scaling Group 생성  
- ALB 및 Target Group 연동  
- 최소/최대 인스턴스 수 설정  
- 기본적인 고가용성 구조 구성  

---

## 4. 핵심 개념  

- **Launch Template**  
  EC2 인스턴스를 생성하기 위한 기준 템플릿으로, AMI, 인스턴스 타입, 보안 설정 등을 포함한다.  

- **Auto Scaling Group (ASG)**  
  설정된 조건에 따라 EC2 인스턴스를 자동으로 생성/삭제하여 개수를 유지하는 서비스이다.  

- **Desired Capacity**  
  Auto Scaling Group이 유지하려는 인스턴스 개수이다.  

- **Health Check**  
  인스턴스가 정상적으로 동작하는지 판단하는 기준이며, 비정상 상태일 경우 자동으로 교체된다.  

---

## 5. 전체 구조 (계층 구조)  

```
Internet
   ↓
ALB (Load Balancer)
   ↓
Target Group
   ↓
Auto Scaling Group
   ↓
EC2 Instances (nginx)
```

---

## 6. 작업 과정  

### 6-1. Launch Template 생성  

Auto Scaling에서 사용할 EC2 생성 기준을 정의하기 위해 Launch Template을 생성하였다.  

설정한 주요 항목:  
- AMI: Ubuntu 22.04  
- Instance type: t3.micro  
- Security Group: HTTP(80), SSH(22) 허용  

User Data:

```bash
#!/bin/bash
apt update -y
apt install nginx -y
systemctl enable nginx
systemctl start nginx
```

이 과정에서  
Auto Scaling은 기존 인스턴스를 복제하는 것이 아니라  
Launch Template을 기반으로 새로운 인스턴스를 생성한다는 점을 이해했다.  

### 📸 Launch Template 생성 화면  

![launch-template](./images/launch-template.png)

---

### 6-2. Auto Scaling Group 생성  

Launch Template을 기반으로 Auto Scaling Group을 생성하였다.  

설정한 주요 항목:  
- Min: 2  
- Desired: 2  
- Max: 4  
- Subnet: Public Subnet 2개 선택  
- Target Group: 기존 ALB Target Group 연결  

이 과정에서  
ALB는 EC2 인스턴스를 직접 관리하지 않고  
Target Group 단위로 트래픽을 전달한다는 점을 이해했다.  

### 📸 Subnet 및 Target Group 연결 화면  

![asg-subnet](./images/asg-subnet.png)

### 📸 Min / Desired / Max 설정 화면  

![asg-capacity](./images/asg-capacity.png)

---

### 6-3. Auto Scaling 동작 확인  

Auto Scaling Group 생성 후 EC2 인스턴스가 자동으로 생성되는 것을 확인하였다.  

- EC2 → Instances에서 인스턴스 생성 확인  
- Target Group → Targets에서 healthy 상태 확인  

이 과정에서  
Auto Scaling Group은 인스턴스를 생성할 뿐만 아니라  
Target Group에 자동으로 등록된다는 점을 확인하였다.  

### 📸 EC2 인스턴스 생성 화면  

![ec2-created](./images/ec2-created.png)

### 📸 Target Group healthy 상태  

![target-group](./images/target-group.png)

---

## 7. 확인 결과  

- EC2 인스턴스 2개 자동 생성  
- Target Group에서 모든 인스턴스 healthy 상태 확인  
- ALB를 통해 정상적인 웹 페이지 접근 가능  

---

## 8. 운영 관점  

Auto Scaling은 단순한 확장 기능이 아니라  
서비스의 가용성을 유지하기 위한 핵심 구성 요소이다.  

최소 인스턴스 수를 유지함으로써  
하나의 서버가 장애가 발생하더라도 서비스가 중단되지 않는다.  

---

## 9. 발생 가능한 문제  

1. Security Group이 다른 VPC에 속해 Auto Scaling 생성 실패  
2. Max 값이 Min보다 작아서 생성 오류 발생  
3. Target Group 미연결로 트래픽 전달 실패  

---

## 10. 트러블슈팅  

- Security Group VPC 불일치 문제 발생  
→ Launch Template의 Security Group을 동일 VPC로 수정  

- Max < Min 오류 발생  
→ Max 값을 Min보다 크게 수정  

---

## 11. 배운 점  

Auto Scaling은 특정 인스턴스를 유지하는 것이 아니라  
설정된 Desired Capacity를 기준으로 전체 인스턴스를 관리한다는 점을 이해했다.  

또한 ALB와 Target Group을 통해  
트래픽이 자동으로 분산되는 구조를 명확하게 이해할 수 있었다.  

---

## 12. 다음 단계  

- CPU 부하를 통한 Scale Out / Scale In 테스트 진행  
- 장애 발생 시 자동 복구 동작 확인  

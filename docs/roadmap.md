# AWS Infrastructure Study Roadmap

## Level 1: 서버 생성 및 기본 구성

- AWS 계정 생성
- EC2 인스턴스 생성
- SSH 접속
- nginx 설치 및 실행
- 웹 브라우저 접속 확인

---

## Level 2: 서버 운영 및 Linux

- nginx 서비스 제어 (start / stop / restart)
- 웹 서버 로그 확인
- Linux 기본 명령어
- 파일 및 디렉토리 관리
- 파일 권한 (chmod)

---

## Level 3: 네트워크 이해

- IP / Port 개념
- HTTP / HTTPS 이해
- Security Group 설정 및 점검
- 외부 접속 흐름 이해

---

## Level 4: AWS 네트워크 구조

- VPC 개념 이해
- Subnet 구성
- Public / Private 구분
- Internet Gateway / NAT Gateway

---

## Level 5: 모니터링 및 운영 이해

- CloudWatch 기본 이해
- CPU / 네트워크 모니터링
- 서버 상태 확인
- 로그 기반 문제 분석

---

## Level 6: 서비스 구조 확장 (Load Balancing)

- EC2 다중 서버 구성
- ALB (Application Load Balancer) 적용
- Target Group 이해
- 트래픽 분산 구조 확인
- Health Check 기반 장애 대응

---

## Level 7: Auto Scaling

- Launch Template 생성
- Auto Scaling Group 구성
- 최소/최대 인스턴스 개념
- ALB와 Auto Scaling 연동
- 서버 자동 생성 / 삭제 흐름 이해

---

## Level 8: 모니터링 기반 자동화

- CloudWatch Alarm 설정
- CPU 기반 Auto Scaling 정책 구성
- 트래픽 증가/감소에 따른 자동 확장 실습
- 운영 관점에서의 확장 전략 이해

---

## Level 9: 아키텍처 설계 (3-Tier 준비)

- Web / App / DB 구조 이해
- Public / Private Layer 설계
- 보안 그룹 계층 구조 설계
- 실제 서비스 아키텍처 흐름 이해

---

## Level 10: DevOps 기초

- GitHub 기반 인프라 기록 관리
- 실습 문서화 및 구조 정리
- 간단한 배포 흐름 이해
- 이후 Terraform / IaC 확장 준비

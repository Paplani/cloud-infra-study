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


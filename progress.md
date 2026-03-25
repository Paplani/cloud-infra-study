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

## 2026-03-25

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

## Day 3

### 진행

### 배운 점


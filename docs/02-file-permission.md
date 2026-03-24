# File Permission (chmod)

## 1. 실습 목적

Linux 파일 권한 구조를 이해하고  
chmod 명령어를 통해 권한을 변경한다.

또한 nginx가 파일을 읽기 위해 필요한 권한을 확인한다.

---

## 2. 실습 환경

- Cloud: AWS EC2
- OS: Ubuntu
- Access: SSH
- Web Server: nginx

---

## 3. 구성 내용

- test 파일 생성
- chmod를 이용한 권한 변경
- nginx 기본 파일 권한 확인

---

## 4. 작업 과정

### 4-1. 파일 생성 및 권한 확인

```bash
touch test.txt
ls -l
```

- 파일 생성 후 권한 확인

---

### 4-2. 권한 변경

```bash
chmod 777 test.txt
chmod 644 test.txt
chmod 755 test.txt
```

- 777: 모든 사용자 읽기/쓰기/실행
- 644: owner만 쓰기 가능
- 755: 실행 권한 포함

---

### 4-3. nginx 파일 권한 확인

```bash
cd /var/www/html
ls -l
```

- 기본 파일: index.nginx-debian.html

```bash
sudo chmod 644 index.nginx-debian.html
```

- nginx가 파일을 읽을 수 있도록 권한 설정

---

## 5. 확인 결과

- chmod 적용 후 파일 권한이 변경됨
- nginx가 파일을 정상적으로 읽을 수 있는 상태 확인

---

## 6. 운영 관점에서 중요한 이유

- 웹 서버는 파일을 읽을 수 있어야 정상적으로 동작한다
- 잘못된 권한 설정은 서비스 장애나 보안 문제로 이어질 수 있다
- Linux 권한 관리는 서버 운영의 기본 요소이다

---

## 7. 문제 / 트러블슈팅

### 기본 파일 이름 혼동

- 문제: index.html이 아닌 index.nginx-debian.html 존재
- 원인: Ubuntu nginx 기본 파일 이름
- 해결: 해당 파일 기준으로 권한 적용

---

## 8. 배운 점

- Linux 권한은 user / group / others로 구성된다
- chmod를 통해 권한을 제어할 수 있다
- 권한 설정은 기능이 아니라 보안과 직접 연결된다

---

## 9. 다음 실습 계획

- EC2 인스턴스를 다시 생성하여 전체 흐름 복습

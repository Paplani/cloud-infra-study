# HTTPS 적용 및 리다이렉트 실습

## 1. 실습 목적

nginx에 HTTPS를 적용하고,  
HTTP 요청을 HTTPS로 자동 리다이렉트하는 설정을 구성했다.

이번 실습의 목적은  
단순히 HTTPS를 사용하는 것이 아니라,  
**웹 서버에서 보안 연결이 어떻게 구성되고 동작하는지 이해하는 것**이다.

또한 설정 과정에서 발생한 오류를 통해  
문제를 해결하는 흐름도 함께 확인했다.

---

## 2. 실습 환경

- Cloud: AWS EC2
- OS: Ubuntu
- Web Server: nginx
- Access:
  - SSH
  - HTTP / HTTPS (브라우저)

---

## 3. 핵심 개념

### HTTP vs HTTPS

- HTTP (80): 암호화 없음 (평문 통신)
- HTTPS (443): SSL/TLS 기반 암호화

HTTP는 단순하지만 보안에 취약하고,  
HTTPS는 암호화를 통해 데이터를 보호한다.

---

### HTTPS 동작 조건

HTTPS는 단순히 포트를 여는 것으로 동작하지 않는다.

필요 요소:

- SSL 인증서
- nginx 설정
- 443 포트 오픈

즉,  
**포트 + 인증서 + 서버 설정이 모두 있어야 HTTPS가 동작한다.**

---

## 4. 작업 과정

### 4-1. self-signed 인증서 생성

```bash
sudo openssl req -x509 -nodes -days 365 \
-newkey rsa:2048 \
-keyout /etc/ssl/private/nginx-selfsigned.key \
-out /etc/ssl/certs/nginx-selfsigned.crt
```

---

### 4-2. nginx HTTPS 설정

```bash
sudo nano /etc/nginx/sites-available/default
```

아래 server 블록 추가:

```nginx
server {
    listen 443 ssl;
    server_name _;

    ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
    ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;

    root /var/www/html;
    index index.html index.nginx-debian.html;
}
```

---

### 4-3. nginx 재시작

```bash
sudo nginx -t
sudo systemctl restart nginx
```

---

### 4-4. HTTPS 접속 확인

```text
https://<public-ip>
```

결과:

- 브라우저 경고 발생 (self-signed 인증서)
- 페이지 정상 출력

---

## 5. 문제 발생 및 해결

### 5-1. 403 Forbidden 발생

HTTPS 접속 시 아래 오류 발생:

```
403 Forbidden
nginx/1.18.0 (Ubuntu)
```

---

### 원인

- HTTPS 연결 자체는 성공
- nginx 실행도 정상
- 하지만 index 파일 처리 문제

즉,  
**네트워크나 HTTPS 문제가 아니라 nginx 설정 문제였다.**

---

### 해결

nginx 설정 수정:

```nginx
index index.html index.nginx-debian.html;
```

→ 기본 파일까지 포함하도록 설정

---

### 결과

- HTTPS 정상 출력 확인

---

## 6. HTTP → HTTPS 리다이렉트 설정

### 6-1. 기존 80 포트 설정

기존에는 HTTP로도 직접 웹 서비스를 제공하는 설정이 있었다.

---

### 6-2. 리다이렉트 설정 적용

```nginx
server {
    listen 80;
    server_name _;

    return 301 https://$host$request_uri;
}
```

---

### 6-3. 적용

```bash
sudo nginx -t
sudo systemctl restart nginx
```

---

### 6-4. 확인

```text
http://<public-ip>
```

결과:

→ 자동으로 HTTPS로 이동

---

## 7. 설정 판단 과정 (중요)

처음에는 기존 80 포트 설정을 지워도 되는지 고민이 있었다.

기존 설정:

- HTTP로 직접 서비스 제공

변경 후:

- HTTP → HTTPS 리다이렉트만 수행

---

### 판단 기준

- 이미 443(HTTPS) 서버가 정상 동작 중이라면  
  → 80은 리다이렉트만 담당하도록 변경 가능

즉, 구조는 다음과 같다.

```
80 → HTTPS로 리다이렉트
443 → 실제 서비스 제공
```

이 방식이 실제 운영에서도 일반적으로 사용하는 구조이다.

---

## 8. 확인 결과

- HTTPS 정상 동작
- self-signed 인증서 기반 연결 확인
- 403 오류 해결 완료
- HTTP → HTTPS 리다이렉트 정상 동작

---

## 9. 운영 관점에서 중요한 점

- HTTP는 보안에 취약하므로 HTTPS 사용이 기본이다
- HTTPS는 단순 포트 문제가 아니라 인증서와 설정이 필요하다
- 리다이렉트를 통해 모든 트래픽을 HTTPS로 유도할 수 있다

또한 설정 과정에서:

- 연결 문제인지
- 서버 설정 문제인지

구분하는 것이 중요하다는 점을 확인했다.

---

## 10. 트러블슈팅

### HTTPS 접속 시 오류 발생

확인 순서:

1. 443 포트 열려 있는지 확인
2. nginx 설정 확인
3. 인증서 경로 확인
4. index 설정 확인

---

### 403 Forbidden 발생

원인:

- index 파일 설정 문제

해결:

```nginx
index index.html index.nginx-debian.html;
```

---

### 리다이렉트 안 될 때

확인:

- 80 포트 설정이 return 301인지 확인
- nginx 재시작 여부 확인

---

## 11. 배운 점

- HTTPS는 단순 포트가 아니라 인증서 + 설정이 필요하다
- HTTP와 HTTPS는 구조적으로 다르다
- nginx 설정 하나로 동작이 크게 바뀔 수 있다
- 문제 발생 시 원인을 단계적으로 구분하는 것이 중요하다

---

## 12. 다음 단계

- Let's Encrypt 인증서 적용
- HTTPS 자동 갱신 설정
- 도메인 기반 HTTPS 구성

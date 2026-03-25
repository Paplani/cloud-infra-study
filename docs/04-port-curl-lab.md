# Port & 요청 흐름 실습 (curl)

## 1. 실습 목적

curl을 이용해서 서버에 직접 요청을 보내보고,  
내부 요청과 외부 요청의 차이를 확인했다.

이번 실습의 핵심은  
**요청이 서버까지 도달하는 과정과, 어디에서 차단될 수 있는지 이해하는 것**이다.

---

## 2. 실습 환경

- Cloud: AWS EC2
- OS: Ubuntu
- Web Server: nginx
- Access:
  - SSH
  - curl (터미널)
  - Browser

---

## 3. 핵심 개념

### curl이란?

curl은 터미널에서 HTTP 요청을 보낼 수 있는 도구다.

브라우저 대신 직접 요청을 보내서  
서버가 어떤 응답을 주는지 확인할 수 있다.

---

### 내부 요청 vs 외부 요청

#### 내부 요청

```bash
curl http://localhost
```

- 서버 내부에서 nginx로 요청을 보냄
- Security Group 영향 없음

---

#### 외부 요청

```bash
curl http://<public-ip>
```

또는 브라우저 접속:

```text
http://<public-ip>
```

- 인터넷을 통해 AWS로 요청이 들어옴
- Security Group 영향을 받음

---

## 4. 작업 과정

### 4-1. nginx 상태 확인

```bash
sudo systemctl status nginx
```

→ active (running) 확인

---

### 4-2. 내부 요청 테스트

```bash
curl http://localhost
```

결과:

- HTML 코드 출력

👉 nginx가 정상적으로 응답하고 있는 상태

---

### 4-3. 외부 요청 테스트

브라우저:

```text
http://<public-ip>
```

결과:

- nginx 페이지 정상 출력

---

### 4-4. HTTP(80) 차단

Security Group에서 HTTP(80) 포트를 제거

---

### 4-5. 외부 요청 다시 테스트

```text
http://<public-ip>
```

결과:

- 접속 실패 (timeout)

---

### 4-6. 내부 요청 다시 테스트

```bash
curl http://localhost
```

결과:

- 정상 응답

👉 nginx는 정상인데 외부 접속만 안 됨

---

## 5. 확인 결과

- 내부 요청 → 항상 정상 (nginx 살아있으면)
- 외부 요청 → Security Group 영향 받음
- HTTP 포트 차단 시 외부 요청 실패

---

## 6. 요청 흐름 정리

### 내부 요청

```text
EC2 내부 → nginx → 응답
```

---

### 외부 요청

```text
내 PC → 인터넷 → AWS → Security Group → EC2 → nginx
```

---

## 7. 핵심 이해

이번 실습에서 가장 중요하게 이해한 내용은 다음과 같다.

내부 요청은 서버 내부에서 nginx에 직접 요청을 보내고 응답을 받는 구조이고,  
외부 요청은 인터넷을 통해 AWS로 들어온 요청이 Security Group을 통과해야 nginx까지 도달할 수 있다.  

그래서 HTTP 포트가 막혀 있으면  
요청 자체가 서버에 도달하지 못한다.

---

## 8. 운영 관점에서 느낀 점

- 접속이 안 될 때는 “요청이 서버까지 도달했는지”가 중요하다
- 내부 요청이 되면 nginx는 정상일 가능성이 높다
- 외부 요청이 안 되면 네트워크 문제일 가능성이 높다

즉,  
**서버 문제인지 네트워크 문제인지 구분하는 기준이 생겼다.**

---

## 9. 트러블슈팅

### 외부 접속 안 될 때

가능한 원인:

- HTTP(80) 포트 차단
- 퍼블릭 IP 오류
- 네트워크 설정 문제

확인 순서:

1. Security Group에서 80 포트 확인
2. 퍼블릭 IP 확인
3. 내부 요청 테스트

```bash
curl http://localhost
```

👉 내부는 되고 외부만 안 되면  
→ 네트워크 문제

---

### 내부 요청 안 될 때

가능한 원인:

- nginx 서비스 중지

확인:

```bash
sudo systemctl status nginx
```

해결:

```bash
sudo systemctl restart nginx
```

---

## 10. 배운 점

- curl을 통해 직접 요청을 보내고 확인할 수 있다
- 내부 요청과 외부 요청은 완전히 다른 경로로 동작한다
- Security Group은 외부 요청에만 영향을 준다
- 요청이 어디까지 도달했는지 확인하는 것이 중요하다

---

## 11. 다음 단계

- HTTP vs HTTPS 이해
- 80 / 443 포트 차이 이해
- HTTPS 적용 구조 확인

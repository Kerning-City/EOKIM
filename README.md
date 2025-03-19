
# 🚀 Docker 기반 Java 애플리케이션 배포 및 사용자 실행 테스트 절차

---

## ✅ 1. 목표  
- `step01_basic_t-0.0.1-SNAPSHOT.jar`를 Docker 컨테이너 이미지로 빌드  
- Docker Hub에 업로드  
- 다른 사용자가 Docker Hub에서 이미지를 다운로드(pull)하고 실행 및 테스트까지 완료  

---

## ✅ 2. Dockerfile 작성 (최적화, 경량화)
```dockerfile
FROM openjdk:17-slim

WORKDIR /app

COPY step01_basic_t-0.0.1-SNAPSHOT.jar /app/step01_basic_t-0.0.1-SNAPSHOT.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "step01_basic_t-0.0.1-SNAPSHOT.jar"]
```
> 👉 `openjdk:17-slim` 사용: 가벼운 실행용 이미지  

---

## ✅ 3. Docker 이미지 빌드
```bash
docker build -t javaapp:1.0 .
```
> `javaapp:1.0` 이름으로 로컬 이미지 생성

---

## ✅ 4. DockerHub 업로드 절차

### 4-1) Docker Hub 로그인
```bash
docker login
```
> Docker Hub ID와 비밀번호 입력  
> 로그인 성공 시 `Login Succeeded` 메시지 출력

### 4-2) Docker 이미지에 태그 추가
```bash
docker tag javaapp:1.0 eoteagyu/javaapp:1.0
```
> `eoteagyu` 부분은 본인의 Docker Hub 계정으로 변경

### 4-3) Docker Hub로 Push
```bash
docker push eoteagyu/javaapp:1.0
```
> 업로드가 완료되면 Docker Hub 리포지토리에서 확인 가능  
> 확인 주소: [https://hub.docker.com/](https://hub.docker.com/)

---

## ✅ 5. 사용자가 이미지 다운로드(pull) 및 실행 테스트

### 5-1) 사용자가 Docker Hub에서 이미지 가져오기
```bash
docker pull eoteagyu/javaapp:1.0
```
> 다운로드 완료 시 로컬 이미지 목록에서 확인 가능:
```bash
docker images
```

### 5-2) 사용자가 컨테이너 실행
```bash
docker run -d -p 8080:8080 --name javaapp eoteagyu/javaapp:1.0
```
- `-d` : 백그라운드 실행
- `-p 8080:8080` : 호스트 포트 8080 → 컨테이너 포트 8080 매핑
- `--name javaapp` : 컨테이너 이름 지정

### 5-3) 사용자가 컨테이너 실행 상태 확인
```bash
docker ps
```
> 상태가 `Up` 인지 확인

---

## ✅ 6. 실행 테스트 (사용자 측)
### 6-1) 웹 브라우저에서 접속
- 주소창에 입력:
```
http://localhost:8080
```
> 정상적으로 Java 애플리케이션 실행 화면 또는 API 응답이 나오면 성공!

### 6-2) 또는 curl로 테스트
```bash
curl http://localhost:8080
```
> 응답 메시지가 잘 출력되면 테스트 완료!

---

## ✅ 7. 발생할 수 있는 오류와 해결 방법

| 문제                                                      | 원인                                                   | 해결 방법                                                   |
|---------------------------------------------------------|------------------------------------------------------|----------------------------------------------------------|
| `openjdk:17-jre-slim` 이미지 오류                      | 해당 태그가 Docker Hub에 없음                         | `openjdk:17-slim` 이미지로 변경                           |
| `failed to do request: i/o timeout`                    | Docker Hub 연결 시 네트워크 문제                      | `/etc/docker/daemon.json`에 DNS(`8.8.8.8`) 설정 후 Docker 재시작 |
| `denied: requested access to the resource is denied`   | Docker Hub 로그인 안 했거나 태그 오류                | `docker login` 후 올바르게 태그 및 푸시 후 재시도            |

---

## ✅ 최종 요약
| 단계             | 명령어 / 내용                                                               |
|----------------|------------------------------------------------------------------------|
| Dockerfile 작성  | `FROM openjdk:17-slim` 으로 경량화 이미지 사용                                    |
| 이미지 빌드      | `docker build -t javaapp:1.0 .`                                        |
| DockerHub 로그인 | `docker login`                                                         |
| 태그 변경        | `docker tag javaapp:1.0 eoteagyu/javaapp:1.0`                          |
| DockerHub Push | `docker push eoteagyu/javaapp:1.0`                                     |
| 사용자가 Pull   | `docker pull eoteagyu/javaapp:1.0`                                     |
| 사용자가 실행   | `docker run -d -p 8080:8080 eoteagyu/javaapp:1.0`                      |
| 테스트 확인      | 브라우저 또는 `curl http://localhost:8080` 로 확인                           |

---

## 🎉 이제 누구나 Docker Hub에서 가져와 실행할 수 있습니다!  
> **한 줄 테스트 요약:**
```bash
docker pull eoteagyu/javaapp:1.0 && docker run -d -p 8080:8080 eoteagyu/javaapp:1.0
```
```
테스트 성공 시: 브라우저나 curl에서 정상 응답 확인!
```

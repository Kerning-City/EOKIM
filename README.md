# 🚀 Docker 기반 Spring Boot 애플리케이션 경량화 이미지 빌드 및 배포 가이드


## ✅ 1. 목표  
- **Spring Boot 애플리케이션(`springbootapp.jar`)을 경량화된 Docker 이미지로 빌드 및 배포**  
- **왜 Docker 이미지 경량화가 중요한가? (개발자 관점)**  
  - 빠른 빌드, 배포 및 다운로드 속도  
  - 보안 취약점 및 의존성 최소화  
  - 클라우드 및 CI/CD 환경에서 리소스 절약  
  - 개발 및 테스트 시 더 빠른 실행과 유지보수 용이  

- 최적화된 Docker 이미지를 Docker Hub에 업로드  
- 다른 개발자가 Docker Hub에서 이미지를 pull 후 실행 및 테스트 가능하도록 제공  

---

## ✅ 2. Dockerfile 작성 (경량화 및 최적화)
```dockerfile
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

COPY springbootapp.jar /app/springbootapp.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "springbootapp.jar"]
```
> ✅ `eclipse-temurin:17-jre-alpine` 또는 `openjdk:17-slim` 이미지를 사용해 불필요한 요소를 제거한 경량 이미지 사용  
> ✅ JAR 파일 이름은 `springbootapp.jar`로 통일  

---

## ✅ 3. Docker 이미지 빌드
```bash
docker build -t springboot-app:1.0 .
```
> `springboot-app:1.0` 이름으로 로컬 이미지 생성  

---

## ✅ 4. Docker Hub 업로드 절차

### 4-1) Docker Hub 로그인
```bash
docker login
```
> Docker Hub 계정 정보 입력 후 `Login Succeeded` 확인  

### 4-2) Docker 이미지 태그 지정
```bash
docker tag springboot-app:1.0 <your-dockerhub-username>/springboot-app:1.0
```
> ✅ `<your-dockerhub-username>` 부분을 본인 Docker Hub 계정명으로 변경  

### 4-3) Docker Hub Push
```bash
docker push <your-dockerhub-username>/springboot-app:1.0
```
> 업로드 후 Docker Hub 리포지토리에서 확인 가능  
> 링크: [https://hub.docker.com/](https://hub.docker.com/)  

---

## ✅ 5. 사용자 실행 테스트 절차

### 5-1) Docker 이미지 다운로드(pull)
```bash
docker pull <your-dockerhub-username>/springboot-app:1.0
```
> 다운로드 완료 후 이미지 확인:
```bash
docker images
```

### 5-2) 컨테이너 실행
```bash
docker run -d -p 8080:8080 --name springboot-app <your-dockerhub-username>/springboot-app:1.0
```
- `-d` : 백그라운드 실행  
- `-p 8080:8080` : 호스트 포트 8080 → 컨테이너 포트 8080 매핑  
- `--name` : 실행 중인 컨테이너 이름 지정  

### 5-3) 컨테이너 실행 상태 확인
```bash
docker ps
```
> `STATUS`가 `Up` 상태인지 확인  

---

## ✅ 6. 애플리케이션 실행 확인
### 6-1) 브라우저 테스트
```
http://localhost:8080
```
> Spring Boot 앱 화면 또는 API 응답 정상 확인 시 성공  

### 6-2) curl 테스트
```bash
curl http://localhost:8080
```
> 정상적인 응답이 출력되면 완료!  

---

## ✅ 7. 발생 가능한 오류 및 해결 방법

| 문제                                                      | 원인                                                      | 해결 방법                                                   |
|---------------------------------------------------------|---------------------------------------------------------|----------------------------------------------------------|
| `openjdk:17-jre-slim` 이미지 오류                      | 해당 태그 이미지가 존재하지 않음                         | `openjdk:17-slim` 또는 `eclipse-temurin:17-jre-alpine`로 변경 |
| `failed to do request: i/o timeout`                    | Docker Hub 연결 시 네트워크 문제                          | `/etc/docker/daemon.json`에 DNS(`8.8.8.8`) 추가 후 Docker 재시작 |
| `denied: requested access to the resource is denied`   | Docker Hub 로그인 안 되었거나 태그 지정 오류              | `docker login` 실행 후 태그 확인 및 재시도                     |

---

## ✅ 최종 요약
| 단계               | 명령어 / 내용                                                                 |
|------------------|----------------------------------------------------------------------------|
| Dockerfile 작성   | 경량화 이미지(`eclipse-temurin:17-jre-alpine`) 기반 작성 및 `springbootapp.jar` 복사                      |
| 이미지 빌드        | `docker build -t springboot-app:1.0 .`                                      |
| DockerHub 로그인  | `docker login`                                                             |
| 이미지 태그 지정   | `docker tag springboot-app:1.0 <your-dockerhub-username>/springboot-app:1.0`  |
| DockerHub 업로드 | `docker push <your-dockerhub-username>/springboot-app:1.0`                  |
| 사용자 Pull      | `docker pull <your-dockerhub-username>/springboot-app:1.0`                  |
| 컨테이너 실행     | `docker run -d -p 8080:8080 <your-dockerhub-username>/springboot-app:1.0`  |
| 실행 확인        | 브라우저 접속 또는 `curl http://localhost:8080` 실행으로 정상 응답 확인                        |

---

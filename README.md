

# 🚀 Docker 기반 Java 애플리케이션 배포 절차

## ✅ 1. 목표  
- `step01_basic_t-0.0.1-SNAPSHOT.jar`를 Docker로 컨테이너화하여 실행  
- Docker Hub에 업로드하여 다른 환경에서도 실행 가능하도록 설정  

---

## ✅ 2. Dockerfile 작성 (최적화 버전)
```dockerfile
FROM openjdk:17-slim

WORKDIR /app

COPY step01_basic_t-0.0.1-SNAPSHOT.jar /app/step01_basic_t-0.0.1-SNAPSHOT.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "step01_basic_t-0.0.1-SNAPSHOT.jar"]
```
> `openjdk:17-slim` 사용: 가벼운 JRE 포함된 이미지  

---

## ✅ 3. Docker 이미지 빌드
```bash
docker build -t javaapp:1.0 .
```

---

## ✅ 4. DockerHub 업로드 준비
### 1) Docker Hub 로그인
```bash
docker login
```
> Docker Hub 계정 입력 필요

### 2) 태그 변경
```bash
docker tag javaapp:1.0 eoteagyu/javaapp:1.0
```
> `eoteagyu`를 본인의 Docker Hub 계정 ID로 변경  

---

## ✅ 5. Docker Hub로 Push
```bash
docker push eoteagyu/javaapp:1.0
```
> **푸시 완료 후, Docker Hub에서 확인!**  
> [Docker Hub 링크](https://hub.docker.com/)

---

## ✅ 6. Docker 컨테이너 실행
```bash
docker run -d -p 8080:8080 --name javaapp eoteagyu/javaapp:1.0
```
> `-d` : 백그라운드 실행  
> `-p 8080:8080` : 로컬 포트 매핑  

---

## ✅ 7. 발생한 오류 & 해결 과정

| 문제                                                      | 원인                                                   | 해결 방법                                                   |
|---------------------------------------------------------|------------------------------------------------------|----------------------------------------------------------|
| `openjdk:17-jre-slim` 이미지가 안됨                     | Docker Hub에 해당 태그 없음                            | `openjdk:17-slim` 이미지로 변경                           |
| `failed to do request: i/o timeout`                    | Docker Hub 접속 시 DNS 또는 네트워크 문제 발생        | `/etc/docker/daemon.json`에 DNS(`8.8.8.8`) 설정 및 Docker 재시작 |
| `denied: requested access to the resource is denied`   | Docker Hub에 로그인 안 했거나 태그가 잘못됨           | `docker login` 후, `docker tag` 올바르게 하고 `docker push` 다시 시도 |

---

## ✅ 8. 최종 요약
| 단계             | 명령어 / 내용                                                               |
|----------------|------------------------------------------------------------------------|
| Dockerfile 작성  | `FROM openjdk:17-slim` 으로 경량화 이미지 사용                                    |
| 이미지 빌드      | `docker build -t javaapp:1.0 .`                                        |
| DockerHub 로그인 | `docker login`                                                         |
| 태그 변경        | `docker tag javaapp:1.0 eoteagyu/javaapp:1.0`                          |
| DockerHub Push | `docker push eoteagyu/javaapp:1.0`                                     |
| Docker Hub 확인 | 웹사이트에서 리포지토리 업로드 여부 확인                                      |

---

### 🎯 **이제 배포된 이미지를 어디서든 사용할 수 있음!**  
```bash
docker pull eoteagyu/javaapp:1.0
docker run -d -p 8080:8080 --name javaapp eoteagyu/javaapp:1.0
```

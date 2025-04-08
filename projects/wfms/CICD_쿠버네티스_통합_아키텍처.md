---
title: CICD + 쿠버네티스 통합 아키텍처
layout: default
---
# [개념명] CI/CD + 쿠버네티스 통합 아키텍처

## 1. 개요
GitLab + Jenkins + Nexus + Kubernetes를 활용한  
**도커 이미지 기반 자동 빌드 및 배포 시스템**

---

## 2. 주요 기능

- Git push → 자동 빌드 및 도커 이미지 생성
- Nexus를 통한 의존성 및 이미지 저장 관리
- Jenkins에서 쿠버네티스 배포까지 자동화
- Kubernetes가 원격 노드에서 이미지 pull 후 서비스 실행

---

## 3. 기술 스택

| 영역 | 기술 |
|------|------|
| 소스 저장소 | GitLab |
| CI/CD | Jenkins |
| 빌드 도구 | Maven / Gradle |
| 이미지 저장소 | Nexus (Docker Registry + Maven Repo) |
| 컨테이너 런타임 | Docker / containerd |
| 컨테이너 오케스트레이션 | Kubernetes |
| 인증 | imagePullSecrets (for Nexus private access) |

---

## 4. 전체 흐름

### 🧑‍💻 개발자
- GitLab에 소스코드 `git push`

### 🦊 GitLab
- Webhook을 통해 Jenkins에 빌드 이벤트 전송

### 🧩 Jenkins
- GitLab에서 코드 clone
- Maven/Gradle로 빌드 (Nexus Maven Repo 참조)
- `.jar` 또는 `.war` 생성
- Docker 이미지 생성
- Nexus Docker Registry에 이미지 push
- `kubectl apply` 또는 Helm으로 쿠버네티스에 배포

### 🚢 Kubernetes 마스터
- 배포 요청 수신 (`kubectl`/Helm)
- 워커 노드에 스케줄링 및 실행 지시

### ⚙️ Kubernetes 워커 노드
- Nexus에서 도커 이미지 pull
- 컨테이너 실행 및 서비스 운영

---

## 5. 다이어그램 요약

```plaintext
개발자
  ↓ git push
GitLab
  ↓ Webhook
Jenkins
  ├─ Maven/Gradle 빌드
  ├─ Nexus에서 의존성 fetch
  ├─ JAR/WAR 생성
  ├─ Docker 이미지 생성
  ├─ 이미지 → Nexus Registry에 push
  └─ kubectl apply → 쿠버네티스 배포
K8s 마스터
  └─ 워커 노드에 실행 명령
워커 노드
  └─ Nexus에서 이미지 pull → 컨테이너 실행
```

---

## 6. 주요 설정 예시

### Dockerfile (Spring Boot JAR)

```Dockerfile
FROM openjdk:17
COPY build/libs/myapp.jar /app/myapp.jar
CMD ["java", "-jar", "/app/myapp.jar"]
```

---

### Kubernetes Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: nexus.example.com/myapp:1.0.0
          ports:
            - containerPort: 8080
      imagePullSecrets:
        - name: nexus-secret
```

---

### imagePullSecret 생성

```bash
kubectl create secret docker-registry nexus-secret \
  --docker-server=nexus.example.com \
  --docker-username=admin \
  --docker-password=비밀번호 \
  --docker-email=dev@example.com
```

---

## 7. 활용 시나리오

1. 개발자가 GitLab에 코드를 push
2. Jenkins가 자동 빌드 및 Docker 이미지 생성
3. Nexus에 이미지 저장
4. Jenkins가 Kubernetes에 배포 명령
5. 워커 노드가 이미지를 pull하여 서비스 실행

---

## 8. 결론

이 구조를 통해  
- **CI/CD 자동화**
- **도커 이미지 기반 배포 표준화**
- **멀티 노드 쿠버네티스 배포 확장성 확보**

까지 모두 달성할 수 있다.  
엔터프라이즈급 배포 자동화 구조로 추천됨 ✅

---

## 9. Jenkins의 Git 연동 방식 (clone vs pull)

Jenkins가 GitLab의 Webhook 이벤트를 받아 빌드를 수행할 때,  
Git 저장소를 **`pull`하는 게 아니라 보통 `clone`을 한다.**

### ✅ 기본 동작

| 명령어 | 용도 | Jenkins에서 쓰이는 시점 |
|--------|------|-------------------------|
| `git clone` | 저장소 전체를 새로 가져옴 | **일반적인 Jenkins 빌드에서 사용됨** |
| `git pull` | 기존 디렉토리에서 최신 변경만 병합 | 캐시를 재사용하는 일부 특수한 경우 |

### 🔧 Jenkins 빌드 흐름 예시

```bash
Cloning repository https://gitlab.com/your-repo.git
Fetching upstream changes
Checking out Revision abc123...
```

### ❗ 예외적으로 pull이 쓰일 수 있는 상황

- Jenkins 워크스페이스 캐시를 유지하는 설정이 있을 경우
- `sparse checkout`, `shallow clone`, `git fetch` 전략을 사용하는 경우
- Git SCM 플러그인 설정에서 “workspace 보존”을 활성화한 경우

### ✅ 결론

> 일반적인 CI/CD 파이프라인에서는  
> **Jenkins는 GitLab에서 코드를 `pull`하는 것이 아니라 `clone`하여 사용한다.**



굿잡
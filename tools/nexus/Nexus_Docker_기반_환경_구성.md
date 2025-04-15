# [개념명] Nexus Docker 기반 환경 구성 - 컨테이너로 아티팩트 저장소 운영하기

## 1. 개요
Nexus Repository Manager는 Java 기반의 아티팩트 저장소 관리 툴로, Maven, npm, Docker, PyPI 등 다양한 패키지 저장소 역할을 수행한다.  
일반적으로 운영 환경에서는 **Docker 컨테이너로 Nexus를 구성**하는 것이 표준처럼 자리잡고 있으며, 설치와 유지보수가 간편하고 이식성이 높다는 장점이 있다.

---

## 2. 왜 Docker로 Nexus를 구성할까?

| 이유 | 설명 |
|------|------|
| ✅ **설치 간편** | `docker run` 명령으로 수초 내에 실행 가능 (별도 JDK 설치, 포트 설정 불필요) |
| ✅ **환경 일관성** | 운영, 테스트, 로컬 환경 모두 동일 이미지로 구성 가능 |
| ✅ **업데이트 쉬움** | 새로운 버전으로 이미지 교체만 하면 업그레이드 가능 |
| ✅ **데이터 분리** | `/nexus-data` 경로만 외부 마운트하면 설정 및 데이터 유지 가능 |
| ✅ **서비스 격리성** | 다른 툴(Jenkins, GitLab 등)과 독립된 환경에서 실행 가능 |
| ✅ **Kubernetes 연계 쉬움** | Docker 환경을 그대로 K8s로 이전 가능 (StatefulSet 등 활용) |

---

## 3. Docker 기반 Nexus 환경 구성 절차

### 🔧 [1] 이미지 다운로드

```bash
docker pull sonatype/nexus3
```

- `sonatype/nexus3`: Nexus 공식 Docker 이미지

---

### 📦 [2] 데이터 저장 경로 준비

```bash
mkdir -p /home/nexus/nexus-data
chown -R 200 /home/nexus/nexus-data
```

- Nexus는 내부적으로 UID `200`번을 사용하므로 권한 설정 필요

---

### 🚀 [3] 컨테이너 실행

```bash
docker run -d \
  --name nexus \
  -p 8081:8081 \
  -v /home/nexus/nexus-data:/nexus-data \
  sonatype/nexus3
```

| 옵션 | 설명 |
|------|------|
| `--name nexus` | 컨테이너 이름 |
| `-p 8081:8081` | 호스트 포트 ↔ 컨테이너 포트 매핑 |
| `-v ...` | 데이터 디렉토리 마운트 (볼륨 유지) |

---

### 🔑 [4] 초기 관리자 비밀번호 확인

```bash
cat /home/nexus/nexus-data/admin.password
```

- 최초 접속 시 http://localhost:8081 로 접속
- 위 경로에 생성되는 비밀번호로 로그인

---

### ⚙️ [5] 필수 초기 설정

1. 관리자 비밀번호 변경
2. 기본 저장소(Maven Central 등) 구성
3. 사내 호스팅 저장소 생성 (Hosted Repository)
4. 접근 권한/사용자 그룹 설정

---

## 4. 추가 구성 예시

### ✅ 환경변수 사용 (예: Java 옵션)

```bash
docker run -d \
  -e INSTALL4J_ADD_VM_PARAMS="-Xms2g -Xmx2g -XX:MaxDirectMemorySize=2g" \
  -v /home/nexus/nexus-data:/nexus-data \
  -p 8081:8081 \
  sonatype/nexus3
```

---

### ✅ `docker-compose` 예시

```yaml
version: '3'
services:
  nexus:
    image: sonatype/nexus3
    container_name: nexus
    ports:
      - "8081:8081"
    volumes:
      - ./nexus-data:/nexus-data
    environment:
      - INSTALL4J_ADD_VM_PARAMS=-Xms2g -Xmx2g -XX:MaxDirectMemorySize=2g
```

---

## 5. 한줄 요약

> Nexus는 Docker 기반으로 설치하는 것이 현대적인 표준 방식이며, 설치가 간단하고 환경 간 이식성과 유지보수가 뛰어나다.  
> `/nexus-data`만 잘 마운트하면 손쉬운 백업과 복구도 가능하다.

---

## 6. 참고 링크

- [Nexus Docker 공식 이미지](https://hub.docker.com/r/sonatype/nexus3)
- [Nexus 설정 가이드 (공식문서)](https://help.sonatype.com/repomanager3)

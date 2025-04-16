# [개념명] Docker와 유저랜드 개념 정리 - 커널 공유 기반의 컨테이너 실행 환경 구조

## 1. 개요
Docker는 호스트 운영체제의 커널을 공유하면서, 각 컨테이너가 독립된 유저랜드(Userland) 환경을 갖도록 설계된 가상화 기술이다.  
Docker를 통해 Nexus, Jenkins, Tomcat 등의 서비스를 컨테이너 이미지로 실행할 수 있으며, 이들은 각기 다른 유저랜드를 갖는다.

---

## 2. 핵심 개념

### ✅ Docker의 구조
| 구성 요소 | 설명 |
|-----------|------|
| **커널(Kernel)** | 호스트 운영체제의 커널을 모든 컨테이너가 공유 |
| **유저랜드(Userland)** | 컨테이너 내부의 OS 유사 환경 (예: Ubuntu, Alpine, CentOS 기반 등) |
| **컨테이너(Container)** | 애플리케이션 + 라이브러리 + 유저랜드 환경의 독립 실행 단위 |

### ✅ 유저랜드란?
호스트의 커널 위에서 동작하며, 사용자와 시스템이 직접 상호작용하는 계층으로 다음과 같은 요소를 포함한다.

| 범주 | 예시 |
|------|------|
| 쉘 | bash, sh, zsh |
| 유틸리티 도구 | ls, cp, cat, grep |
| 시스템 라이브러리 | glibc, musl |
| 패키지 매니저 | apt (Debian), yum (CentOS), apk (Alpine) |
| 설정 파일 | /etc/passwd, /etc/hosts, /etc/resolv.conf |
| 디렉토리 구조 | /bin, /etc, /lib, /usr, /var |
| 런타임 | Java, Python, Node.js 등 |

---

## 3. Docker 이미지와 유저랜드의 관계

| 이미지 | 유저랜드 기반 | 설명 |
|--------|----------------|------|
| sonatype/nexus3 | CentOS | Nexus Repository OSS 공식 이미지 |
| jenkins/jenkins | Ubuntu | Jenkins 공식 이미지 |
| tomcat | Debian | Apache Tomcat 공식 이미지 |
| nginx:alpine | Alpine | 경량 컨테이너용 이미지 |

- 각 이미지마다 포함된 유저랜드 환경이 다르며, 내부의 `/etc`, `/lib`, `/usr` 구조도 이미지에 따라 달라진다.
- 호스트의 `/etc`와는 완전히 분리된, **자기만의 루트 파일 시스템을 갖는다.**

---

## 4. 설정 및 환경변수 주입 방식

- 환경변수: `docker run -e VAR=value` 또는 `docker-compose.yml`의 `environment` 항목
- 설정파일: 각 이미지의 유저랜드 기준 경로 사용 (예: `/nexus-data/nexus.properties`)
- 공유 필요시: `-v` 옵션으로 호스트 디렉토리를 이미지 내부 디렉토리에 마운트

```bash
docker run -d \
  -e JAVA_OPTS="-Xmx2g" \
  -v /home/user/nexus-data:/nexus-data \
  -p 8081:8081 \
  sonatype/nexus3
```

---

## 5. 한줄 요약

> Docker는 호스트 커널을 공유하고, 각 컨테이너는 자신만의 유저랜드(OS처럼 보이는 환경)를 통해 독립적으로 애플리케이션을 실행한다.  
> Docker 이미지마다 유저랜드는 달라질 수 있으며, 이 환경에 맞게 설정과 관리가 필요하다.

---

## 6. 활용 예시

- Ubuntu 기반 Jenkins에서 `apt install`
- Alpine 기반 Nginx에서 `apk add`
- CentOS 기반 Nexus에서 `/etc/nexus-default.properties` 설정 변경

---

## 7. 참고 링크

- Docker Hub: [https://hub.docker.com](https://hub.docker.com)
- Nexus 이미지: [https://hub.docker.com/r/sonatype/nexus3](https://hub.docker.com/r/sonatype/nexus3)
- Dockerfile 예시: [https://github.com/sonatype/docker-nexus3](https://github.com/sonatype/docker-nexus3)

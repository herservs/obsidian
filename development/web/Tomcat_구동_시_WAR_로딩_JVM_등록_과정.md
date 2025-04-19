# [개념명] Tomcat 구동 시 WAR 파일 로딩 및 JVM 등록 과정 - 실행 주체 중심 정리

## 1. 개요
JDK를 기반으로 Tomcat을 실행하고, `webapps` 디렉토리의 WAR 파일을 스캔/전개하여 클래스들을 JVM에 로딩하는 전체 과정을 **실행 주체(누가 하는지)**를 중심으로 설명함.

---

## 2. 전체 실행 흐름 요약

```plaintext
[개발자] ▶ [JDK] ▶ [Tomcat] ▶ [Catalina] ▶ [WebappClassLoader] ▶ [Servlet/Jersey/Spring 등 프레임워크]
```

---

## 3. 단계별 흐름 + 실행 주체

| 단계 | 설명 | 주체 |
|------|------|------|
| 1. JDK 실행 | `java` 명령 또는 `startup.sh` 실행 | 개발자 |
| 2. JVM 생성 | `java`가 JVM을 실행하고 main class (Bootstrap 등) 로딩 | JDK |
| 3. Tomcat 부트스트랩 | `org.apache.catalina.startup.Bootstrap` 실행, Catalina 구성 | Tomcat |
| 4. server.xml 로드 | Host/Engine/Service 구조 구성 | Tomcat |
| 5. `webapps/` 디렉토리 스캔 | `.war` 파일이나 폴더 탐색 | Tomcat |
| 6. WAR 파일 전개 | `.war` 압축 해제 → 디렉토리로 확장 | Tomcat |
| 7. web.xml 파싱 | Servlet, Filter 등 설정 확인 | Tomcat (Catalina) |
| 8. ClassLoader 생성 | 각 웹앱마다 **WebappClassLoader** 생성 | Tomcat |
| 9. 클래스 로딩 | `WEB-INF/classes`와 `lib` 폴더 내 `.class`, `.jar` 로딩 | WebappClassLoader |
| 10. 서블릿 초기화 | ServletContainer (예: Jersey, Spring 등) init 호출 | Tomcat + 서블릿 구현체 |
| 11. 어노테이션 스캔 | `@Path`, `@Controller` 등 프레임워크가 자체적으로 처리 | Jersey / Spring 등 |
| 12. 요청 대기 | HTTP 요청을 받을 준비 완료 | Tomcat + 각 프레임워크 |

---

## 4. 예시 흐름: Jersey 기반 edoc.war 실행 흐름

```plaintext
개발자
 └── startup.sh 실행
      └── JDK(java) → Tomcat(Bootstrap)
           └── Catalina 구성
                └── webapps/edoc.war 전개 ← [Tomcat이 직접 함]
                     └── web.xml 분석 → ServletContainer(Jersey) 등록
                          └── WebappClassLoader가 클래스 로딩
                               └── Jersey가 @Path, @POST 메서드 스캔
                                    └── REST API 준비 완료
```

---

## 5. 핵심 질문 답변

### ❓ Q1. WAR 전개는 누가 하나?
✅ **Tomcat이 직접 전개함.**
- `.war` 파일을 발견하면, 내부적으로 `org.apache.catalina.webresources.StandardRoot` 등을 통해 압축을 풀고 폴더로 확장.

---

### ❓ Q2. 클래스들을 JVM 메모리에 올리는 주체는?
✅ **WebappClassLoader**라는 **Tomcat이 만든 클래스로더 인스턴스가 담당.**
- 각 웹 애플리케이션마다 **분리된 ClassLoader**가 있어서 독립적인 클래스 로딩 가능.

---

### ❓ Q3. @Path, @POST 같은 어노테이션은 누가 인식?
✅ **Jersey (ServletContainer)**가 `init()` 과정에서 지정된 패키지를 스캔하며 처리
- `@Path`, `@GET`, `@POST` → `Application` 또는 `ResourceConfig` 클래스에서 탐색

---

## 6. 요약: 실행 주체 정리표

| 작업 내용 | 실행 주체 |
|-----------|-----------|
| JVM 실행 | `java` 명령 (개발자) |
| Tomcat 부팅 | `Bootstrap`, `Catalina` (Tomcat) |
| WAR 스캔 및 전개 | Tomcat |
| web.xml 파싱 | Tomcat |
| 클래스 로딩 | WebappClassLoader (Tomcat 내부) |
| 서블릿 등록 및 init | Tomcat + 서블릿 구현체 (Jersey 등) |
| REST 어노테이션 처리 | Jersey 프레임워크 |
| 요청 처리 | Tomcat + 서블릿 (Jersey/Spring 등) |

---

## 7. 참고 개념

- Tomcat은 Java Servlet Container (웹 애플리케이션 실행기)
- WAR 파일은 Java EE 표준의 배포 단위
- 클래스 로더는 JVM 영역에 `.class` 및 `.jar` 파일을 로딩하는 역할
- 각 WAR 파일마다 ClassLoader가 분리되어 클래스 충돌을 방지함


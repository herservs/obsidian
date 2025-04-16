# [개념명] e-Doc 시스템 개발 및 배포환경 구성

## 1. 배포환경

### 가. 아키텍처
- 하나의 Tomcat 인스턴스에서 **JSP 기반 프론트 프로젝트**와 **Jersey 기반 REST API 백엔드 프로젝트**를 함께 배포
- 두 프로젝트는 각각 `war` 파일로 패키징되어 `webapps` 디렉토리에 배치됨
- 프론트는 백엔드 REST API를 호출하여 동작

### 나. 배포 방식
- **백엔드**: Maven으로 빌드하여 `war` 파일 생성
- **프론트엔드**: IDE의 Export 기능으로 `war` 파일 생성  
  ※ 프로젝트 초기 코드 구조가 잘못되어 있어, JSP 등의 리소스를 `/src/main/webapp/`로 이동하여 해결함

---

## 2. 개발환경

### 가. 구성 개요
- 개발자는 각각의 PC에서 프론트와 백엔드를 **개별 톰캣 인스턴스**로 실행
- **포트 충돌 방지를 위해 다른 포트**(예: 8080, 8081)로 구동

### 나. 백엔드 설정
- **CORS 문제 해결**
- **Tomcat의 Last-Modified 헤더로 인한 한글 인코딩 문제 해결**

```java
// [1] CORSFilter.java

@Provider
public class CORSFilter implements ContainerResponseFilter {
    @Override
    public void filter(ContainerRequestContext requestContext,
                       ContainerResponseContext responseContext) throws IOException {

        // CORS 설정
        responseContext.getHeaders().add("Access-Control-Allow-Origin", "*");
        responseContext.getHeaders().add("Access-Control-Allow-Headers", "origin, content-type, accept, authorization");
        responseContext.getHeaders().add("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS, HEAD");
        responseContext.getHeaders().add("Access-Control-Allow-Credentials", "true");

        // Last-Modified 헤더 제거 (한글 요일 인코딩 문제 방지)
        responseContext.getHeaders().remove("Last-Modified");
    }
}
```

### 다. 프론트 설정
- **API 호출 URL 변경**:
  - 기존: `/glovis-portal-s4-rest`
  - 변경: `http://localhost:8081/glovis-portal-s4-rest`

```xml
<!-- [2] web.xml (프론트 프로젝트용) -->

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         version="3.1"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee 
         http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd">

    <display-name>edoc-web</display-name>

    <welcome-file-list>
        <welcome-file>default.jsp</welcome-file>
    </welcome-file-list>

</web-app>
```

---

## ✅ 정리 요약

| 구분       | 항목                            | 설명 |
|------------|---------------------------------|------|
| 배포 방식   | Tomcat 단일 인스턴스 / WAR 2개 | 프론트 + 백엔드 WAR 동시 배포 |
| 프론트 WAR | IDE Export 방식                 | `/src/main/webapp`로 이동 필요 |
| 백엔드 WAR | Maven Build                    | REST API |
| 포트       | 개발 시 분리 필요               | 예: 8080(프론트), 8081(백엔드) |
| 필터       | CORS + Last-Modified 제거       | 개발환경 인코딩 문제 대응 |


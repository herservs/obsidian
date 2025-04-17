# [개념명] Java 웹 기술의 계층 구조와 구성요소 관계 - Servlet, JSP, Spring MVC, Jersey

## 1. 개요
Java 웹 기술 스택에서 Servlet, JSP, DispatcherServlet, Jersey, Servlet Container(Tomcat 등)의 역할과 관계를  
**MVC 여부**, **계층 구조**, **실행 흐름**, **등록 및 로딩 원리** 관점에서 정리함.

---

## 2. 핵심 구성 요소 요약

| 구성요소 | 분류 | 설명 | MVC 구조 여부 |
|----------|------|------|---------------|
| **Servlet** | 저수준 기술 | HTTP 요청/응답을 처리하는 자바 클래스 | ❌ |
| **JSP** | 뷰 기술 | HTML에 Java 코드 삽입, 실행 시 Servlet으로 변환됨 | ✅ (뷰 역할) |
| **DispatcherServlet** | 프론트 컨트롤러 | Spring MVC의 중앙 분배 서블릿 | ✅ |
| **Jersey** | REST API 프레임워크 | JAX-RS 구현체, 경량 REST 서버 구성 | ❌ |
| **Servlet Container** | 실행 환경 | Servlet, JSP를 실행시키는 서버 (Tomcat, Jetty 등) | N/A |

---

## 3. 기술 계층 구조 (상하위 관계)

```
[클라이언트 브라우저]
         │
         ▼
[웹 서버 (예: Apache, Nginx)]  ← 필요시 프록시 서버 역할
         │
         ▼
[Servlet Container (Tomcat, Jetty 등)]
         │
 ┌─────────────┬──────────────┬────────────────────┐
 │             │              │                    │
 ▼             ▼              ▼                    ▼
[Servlet]   [JSP → Servlet] [DispatcherServlet]  [Jersey 서블릿]
 (직접 구현)    (뷰 템플릿)    (Spring MVC 프론트 컨트롤러) (REST 처리기)
```

> 모든 구성요소는 결국 **서블릿 컨테이너에 등록되는 서블릿**으로 동작함

---

## 4. MVC 구조 여부 및 용도 정리

| 기술 | MVC 여부 | View 포함 여부 | 주요 용도 |
|------|----------|----------------|-----------|
| Servlet | ❌ | ❌ | 직접 HTTP 요청 처리 |
| JSP | ✅ (뷰 역할) | ✅ | 화면 구성용 (HTML 응답) |
| DispatcherServlet(Spring MVC) | ✅ | ✅ | 전체 웹 MVC 아키텍처 |
| Jersey | ❌ | ❌ (JSON 주로 사용) | REST API 서버 구현 |

---

## 5. JAX 계열 API 정리

| 명칭 | 전체 이름 | 설명 | 사용 목적 | 대표 구현체 |
|------|-----------|------|------------|--------------|
| **JAX-RS** | Java API for RESTful Web Services | REST API 구현 | REST API | Jersey, RESTEasy |
| **JAX-WS** | Java API for XML Web Services | SOAP 방식 웹서비스 | SOAP API | Metro |
| **JAX-RPC** | Java API for XML-based RPC | 오래된 웹서비스 방식 | 폐기됨 | XFire |
| **JAXP** | Java API for XML Processing | XML 파싱 DOM/SAX 등 | XML 처리 | Xerces |
| **JAXR** | Java API for XML Registries | UDDI 레지스트리 상호작용 | 거의 미사용 | 없음 |

---

## 6. 요청 흐름 비교

### ✅ Spring MVC

```
[브라우저 요청]
     ↓
[Tomcat (서블릿 컨테이너)]
     ↓
[DispatcherServlet]
     ↓
[Controller (@RequestMapping)]
     ↓
[ViewResolver → JSP/Thymeleaf]
     ↓
[응답 반환]
```

### ✅ Jersey

```
[브라우저 요청]
     ↓
[Tomcat (서블릿 컨테이너)]
     ↓
[Jersey 서블릿 (ServletContainer 클래스)]
     ↓
[@Path, @GET 등으로 정의된 Resource 클래스]
     ↓
[JSON 응답 반환]
```

---

## 7. 서블릿이 Servlet Container에 등록된다는 개념

### 7.1 한 줄 요약
> 서블릿이 등록된다는 것은, **Tomcat이 웹 애플리케이션 로딩 시 해당 서블릿 클래스를 찾아 로딩하고, 요청 처리 준비를 완료하는 과정**을 의미함.

### 7.2 실제 내부 흐름

```
[TOMCAT 실행]
    ↓
[webapps 디렉토리 스캔]
    ↓
[web.xml 또는 @WebServlet 메타데이터 확인]
    ↓
[클래스 로더(WebAppClassLoader)로 서블릿 클래스 로딩]
    ↓
[Servlet 객체 생성 및 init() 호출]
    ↓
[요청 시 service() → doGet()/doPost()]
```

### 7.3 등록 방식 예시

#### ⬛ web.xml 기반
```xml
<servlet>
  <servlet-name>hello</servlet-name>
  <servlet-class>com.example.HelloServlet</servlet-class>
</servlet>
<servlet-mapping>
  <servlet-name>hello</servlet-name>
  <url-pattern>/hello</url-pattern>
</servlet-mapping>
```

#### ⬛ 어노테이션 기반 (`Servlet 3.0 이상`)
```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) {
        res.getWriter().write("Hello!");
    }
}
```

### 7.4 DispatcherServlet도 동일 구조
```xml
<servlet>
  <servlet-name>spring</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
<servlet-mapping>
  <servlet-name>spring</servlet-name>
  <url-pattern>/</url-pattern>
</servlet-mapping>
```

> Spring Boot에서는 코드 기반으로 자동 등록됨 (ServletRegistrationBean 등)

---

## 8. 용어 바로잡기

- ❌ Jersey는 **서블릿 컨테이너가 아니다**
- ✅ Jersey는 **서블릿 컨테이너(Tomcat 등)에 등록되어 실행되는 서블릿**
- ✅ DispatcherServlet도 **Tomcat에서 실행되는 서블릿 중 하나**
- ✅ ServletContainer는 **실행 환경**이며, 요청 분배와 클래스 로딩을 담당

---

## 9. 마무리 한 줄 요약

> **Servlet Container는 모든 Java 웹 기술의 실행 기반**이며,  
> JSP, Jersey, DispatcherServlet(Spring MVC) 등은  
> **클래스로 작성되어 서블릿으로 등록되고**, 요청이 올 때 각각의 역할(뷰, API, 분기처리)을 수행한다.

# [개념명] JSP vs Thymeleaf - Java 웹 뷰 템플릿 기술 비교 및 프론트/백엔드 분리 구조 대응 전략

## 1. 개요

Java 웹 애플리케이션에서 사용되는 대표적인 서버사이드 뷰 기술인  
**JSP(JavaServer Pages)**와 **Thymeleaf**의 개념과 차이를 정리하고,  
**프론트-백엔드 분리 구조(SPA 등)**에서의 사용 가능성 및 전략을 함께 설명한다.

- **JSP**: HTML 안에 Java 코드를 삽입하여 서버에서 동적으로 HTML 생성  
- **Thymeleaf**: HTML5 문법을 유지한 채, 서버에서 동적 데이터를 속성 기반으로 표현  
- **SPA**: 싱글 페이지 어플리케이션, 동적 라우팅과 클라이언트 렌더링 중심의 프론트 구조  
- **프론트/백 분리 구조**: 클라이언트(프론트)와 서버(백엔드)를 독립적으로 운영하는 구조

---

## 1-1. 추가 개념: SPA vs 프론트/백 분리 구조

| 용어 | 정의 | 설명 |
|------|------|------|
| **SPA (Single Page Application)** | 단일 HTML에서 모든 라우팅 처리 | React, Vue, Angular 기반. CSR 중심 |
| **MPA (Multi Page Application)** | 페이지마다 별도 HTML 요청 | JSP/Thymeleaf 기반, SSR 중심 |
| **프론트/백 분리 구조** | 프론트와 백엔드가 서로 독립된 앱으로 통신 (API) | REST, GraphQL, WebSocket 등 사용 |
| **CSR (Client Side Rendering)** | 클라이언트에서 렌더링 처리 | SPA와 동일 개념으로 쓰임 |
| **SSR (Server Side Rendering)** | 서버에서 HTML 생성 후 응답 | 전통적인 방식 (JSP, Thymeleaf 등) |
| **SSG (Static Site Generation)** | 빌드 타임에 HTML 생성 | 정적 사이트 생성기 (예: Next.js SSG, Hugo) |

> ✅ 대부분의 **SPA는 프론트/백 분리 구조**로 되어 있지만,  
> ❌ **프론트/백 분리 구조라고 해서 반드시 SPA인 것은 아님**

**예시:**

- ✅ SPA + 분리: React + Spring Boot API
- ❌ MPA + 분리: 정적 HTML + REST API
- ❌ SSR 통합형: Spring MVC + JSP/Thymeleaf

---

## 2. JSP 개념

### ✅ 정의

**JSP (JavaServer Pages)**는 HTML 안에 Java 코드를 삽입해서  
**동적 웹 페이지를 생성할 수 있도록 해주는 서버 사이드 기술**이다.

> 브라우저 요청 → JSP 실행 → HTML 생성해서 응답

### ✅ 동작 구조

```
[JSP 파일 (.jsp)]
   ↓   (컴파일 시)
[Servlet(.java)으로 변환됨]
   ↓   (컴파일 시)
[바이트코드(.class)로 컴파일됨]
   ↓
[서블릿 컨테이너(Tomcat 등)에서 실행]
```

→ JSP는 **결국 서블릿으로 변환되어 실행**되는 구조임.

### ✅ 구성 요소

| 구성 요소 | 설명 | 예시 |
|------------|------|------|
| **HTML** | 정적 UI | `<h1>Hello</h1>` |
| **스크립트릿** | Java 코드 삽입 | `<% int i = 1; %>` |
| **표현식** | 값 출력 | `<%= i %>` |
| **지시자** | 페이지 설정 | `<%@ page contentType="text/html" %>` |
| **JSP 태그** | JSTL/커스텀 태그 | `<c:forEach>` 등 |

### ✅ 예시 코드

```jsp
<%@ page contentType="text/html; charset=UTF-8" %>
<html>
<head><title>Hello JSP</title></head>
<body>
<%
  String name = "JSP";
%>
<h1>Hello, <%= name %></h1>
</body>
</html>
```

→ 출력: `Hello, JSP`

### ✅ 특징

| 항목 | 설명 |
|------|------|
| 실행 방식 | HTML 안에 Java 코드 삽입 → 서블릿으로 변환됨 |
| 뷰 역할 | MVC에서 View(화면 생성) 역할 |
| 기술 의존 | Java EE (서블릿 기반) |
| 단점 | 로직과 뷰가 섞여 유지보수 어려움 |
| 대안 | Thymeleaf, React, Vue, 프론트엔드 프레임워크 등 |

---

## 3. Thymeleaf 개념

### ✅ 정의

**Thymeleaf**는 Java 웹에서 사용되는 **HTML 기반의 템플릿 엔진**으로,  
HTML 문서를 그대로 유지하면서 서버에서 **동적으로 데이터를 표현**할 수 있는 뷰 기술이다.

> 브라우저에서 정적 HTML처럼 볼 수 있고, 서버에서는 동적 HTML로 처리됨

### ✅ 특징

| 항목 | 설명 |
|------|------|
| HTML5 친화 | HTML 구조 유지됨 (정적 HTML로도 열림) |
| 속성 기반 표현 | `th:text`, `th:if`, `th:each` 등 |
| XSS 방지 | 자동으로 HTML Escape 처리 |
| Spring Boot 기본 뷰 | 기본 템플릿 엔진으로 채택됨 |
| 유지보수성 | 매우 뛰어남 (Java 코드 없음, 구조 분리 우수) |

### ✅ 예시 코드

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head><title>Hello</title></head>
<body>
  <h1 th:text="'Hello, ' + ${name}">Hello, Friend</h1>
</body>
</html>
```

→ `${name}`이 `Thymeleaf`이면 출력 결과는 `Hello, Thymeleaf`

---

## 4. JSP vs Thymeleaf 비교

| 항목 | **JSP** | **Thymeleaf** |
|------|---------|----------------|
| 기반 | Java EE (Servlet) | HTML5 + Spring 기반 |
| 문법 | Java 코드 직접 삽입 (`<% %>`) | HTML 속성 방식 (`th:*`) |
| HTML 유지 | ❌ 깨짐 (스크립트 때문) | ✅ 완전 유지 가능 |
| 미리보기 | ❌ (서버 필요) | ✅ (정적 HTML처럼 열림) |
| 로직 삽입 | 가능 (뷰 로직 포함) | 불가능 (표현 전용) |
| 보안 | 수동 XSS 처리 필요 | 자동 HTML Escape |
| Spring Boot 기본 지원 | ❌ | ✅ |
| 유지보수성 | 낮음 | 높음 |
| 현대 사용성 | 레거시 중심 | Spring 기반 표준 |

---

## 5. 공통점

- 둘 다 **Java 웹에서 View 역할** 수행
- **Controller → Model → View** 흐름을 따름
- 서버에서 동적 데이터를 받아 HTML을 생성하는 목적

---

## 6. MVC 구조에서의 위치

```
[브라우저 요청]
    ↓
[DispatcherServlet or Servlet (Controller)]
    ↓
[Model 데이터 생성]
    ↓
[JSP or Thymeleaf (View)]
    ↓
[HTML 응답 반환]
```

---

## 7. 프론트/백 분리 구조에서의 사용 가능성

| 구조 유형 | JSP 사용 | Thymeleaf 사용 | 설명 |
|-----------|----------|----------------|------|
| Spring MVC 모놀리식 구조 | ✅ 가능 | ✅ 가능 | Controller → View 구조 |
| 프론트/백 WAR 분리 (SPA 형태) | ❌ 부적합 | ❌ 부적합 | HTML은 정적, 서버는 API 제공 |
| frontend.war이 Spring 기반 | ✅ 가능 | ✅ 가능 | JSP/Thymeleaf로 서버사이드 렌더링 가능 |
| backend.war에서 뷰 생성 | ✅ 가능 | ✅ 가능 | API 외에 HTML을 직접 생성하는 경우 |

> SPA에서는 클라이언트가 HTML 렌더링을 담당하므로 서버 뷰 기술은 사용되지 않음

---

## 8. 대안: SPA 구조에서는?

| 역할 | 기술 스택 |
|------|-----------|
| 프론트 | React / Vue / Angular (정적 리소스) |
| 백엔드 | Spring Boot + REST API + JSON 응답 |
| 통신 방식 | Axios / Fetch / Ajax 등 |

→ 이 구조에서는 템플릿 엔진이 아니라 **API 응답(JSON)** 중심으로 동작

---

## 9. 부록: JSP → Thymeleaf 마이그레이션 전략

| JSP 문법 | Thymeleaf 문법 |
|----------|----------------|
| `<%= name %>` | `th:text=\"${name}\"` |
| `<c:if test=\"\">` | `th:if` |
| `<c:forEach>` | `th:each` |

```html
<!-- Thymeleaf 예시 -->
<h1 th:text="'Hello, ' + ${name}">Hello</h1>
```

---

## 10. 한 줄 요약

> **JSP는 Java 코드 기반의 전통적인 서버사이드 뷰 기술이고**,  
> **Thymeleaf는 HTML 친화적인 현대적 템플릿 엔진이다.**  
> **둘 다 서버에서 HTML을 생성하지만, 프론트-백 분리 SPA 구조에서는 적합하지 않다.**

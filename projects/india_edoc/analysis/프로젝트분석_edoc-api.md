# [개념명] e-Doc 전자결재 시스템 아키텍처 분석 - 확정 및 추가 확인 반영판

---

## ✅ 확실한 내용 (Confirmed)

### 📌 아키텍처 및 기술 스택
- **Maven 기반 WAR 프로젝트**
- **DBMS: MariaDB (MySQL 드라이버 사용)**
- **WAS: Tomcat9**
- **OS: Linux (Red Hat)**
- **ORM: MyBatis (Mapper Interface + XML 매핑)**
- **뷰: JSP + Velocity 병행**
- **프론트엔드: JSP + jQuery + Kendo UI**
- **REST API: Jersey 사용 확정 (`@Path`, `@POST`, `@Consumes` 등 실제 구현 확인됨)**
- **JSON 처리: Jackson + Gson 병행**
- **PDF 출력: Apache PDFBox**
- **로그 처리: Log4j2 + log4jdbc**
- **SAP 연동: SAP JCO (`sapjco3.jar`) 기반 RFC 연동 구조**

### 📌 시스템 기능 및 연계
- **SAP ↔ e-Doc 인터페이스 존재**
  - 5분 주기 자동 연동
  - 전표 Park → Approval → Post 처리 흐름
- **Staging DB 계층 존재 (SAP 중계 역할)**
- **전자증빙 문서 관리 포함 (스캔본 업로드)**
- **인사 마스터 기반의 결재선, 사용자, 부서 권한 관리 포함**

### 📌 계층 구조 흐름도

```
[Client - JSP + Kendo UI]

       ↓ (HTTP)

[Web Layer]
  → Jersey REST Controller
  → 필터 or 인터셉터

       ↓

[Service Layer]
  → Business Logic

       ↓

[Persistence Layer]
  → MyBatis Mapper Interface
  → XML Mapper

       ↓

[DB]
  → MariaDB (MySQL 드라이버)

[외부 연동]
  → SAP (sapjco3 사용)
```

---

## 🔄 추가 확인 내용 정리 결과

| 항목 | 확인 결과 | 세부 내용 |
|------|-----------|-----------|
| **Spring MVC 사용 여부** | ❌ 사용하지 않음 | `web.xml` 확인 결과 Spring DispatcherServlet 없음. @Controller 등 전혀 없음&#8203;:contentReference[oaicite:0]{index=0} |
| **Jersey 단독 사용 여부** | ✅ 단독 사용 확정 | `@Path`, `@POST` 등으로 구성된 REST API 구현 파일 확인&#8203;:contentReference[oaicite:1]{index=1} |
| **Velocity 사용 위치** | ✅ 이메일 템플릿 전용 | `SendEmail.java`에서만 Velocity 사용. View 렌더링 목적이 아니라 메일용 템플릿 렌더링&#8203;:contentReference[oaicite:2]{index=2} |
| **DI 방식 (의존성 주입)** | ❌ 수동 객체 생성 방식 | Impl 클래스 직접 생성 후 호출 (`new Impl()`) 방식 사용&#8203;:contentReference[oaicite:3]{index=3} |
| **인터셉터/필터 구조** | ❌ 없음 | `web.xml` 내 filter 정의 없음, `ContainerRequestFilter` 등도 없음&#8203;:contentReference[oaicite:4]{index=4} |
| **SAP 연동 상세 방식** | ✅ RFC 연동 확정 | `SAPDestManager`, `SAPUtil`, `SAPMassUtil` 분석 결과 SAP JCo로 Destination 설정 후 `JCoFunction.execute()` 방식 사용&#8203;:contentReference[oaicite:5]{index=5} |

---

## 🧠 종합 결론

- Jersey 기반의 경량 REST + MyBatis + JSP 구조로 운영되는 전자결재 시스템
- Spring 프레임워크 미사용, 직접 객체 생성 방식의 구조
- SAP 연동은 JCO 기반으로 안정적 처리 (RFC 함수 호출, 테이블 매핑 포함)
- PDF 출력, 전자증빙 파일 업로드, 승인 흐름 등 실무 전자결재 기능 전반 구현
- Velocity는 이메일 템플릿 렌더링 용도로만 활용됨
- 인터셉터/필터 없음 → 보안·인증 처리는 Jersey 단 또는 프런트에서 처리 가능성 있음


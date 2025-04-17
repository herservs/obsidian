# [개념명] SAP RFC & Function Module 개념 정리

---

## 1. RFC란?

### ✅ 정의
**RFC (Remote Function Call)**  
SAP 시스템에서 정의된 **Function Module(함수)**을  
**외부 시스템(Java 등)에서 원격으로 호출**할 수 있게 해주는 SAP 통신 방식이야.

---

## 2. Function Module이란?

### ✅ 정의
**Function Module (FM)**은  
SAP 안에 정의된 **일종의 재사용 가능한 함수 또는 프로시저**로,  
전표 생성, 데이터 조회, 반려 등 다양한 SAP 내부 로직이 여기에 구현돼.

📌 예를 들어 SAP에서 이런 이름의 FM이 정의되어 있어:
```text
Z_POST_DOC      → 전표를 Post 처리
Z_GET_PARKED    → Parked 전표 조회
Z_REVERSE_DOC   → 전표 반려/삭제
```

> `Z_`로 시작하면 **사용자 정의 커스텀 함수**라는 뜻이야.

---

## 3. RFC와 Function Module의 관계

| 구성 요소 | 설명 |
|-----------|------|
| Function Module | SAP 내부에 정의된 재사용 함수 |
| RFC         | Function Module을 외부에서 **네트워크 호출** 가능하게 한 인터페이스 |
| RFC-enabled FM | `Remote-Enabled`로 설정된 Function Module만 RFC로 호출 가능 |

---

## 4. RFC의 유형 (SAP 기준)

| 종류 | 설명 |
|------|------|
| **sRFC (Synchronous)** | 요청-응답 방식. 일반적인 Java ↔ SAP 통신 구조 |
| **tRFC (Transactional)** | 전송 성공 여부를 보장하는 트랜잭션 기반 RFC |
| **qRFC (Queued)** | 순서 보장 + 트랜잭션 처리. 고도화된 인터페이스 |

> 우리 전자결재 시스템에서는 보통 **sRFC (동기식)** 방식만 사용함.

---

## 5. 개발자 입장에서 SAP RFC 호출 흐름

```java
JCoDestination dest = JCoDestinationManager.getDestination("SAP_CONN");
JCoFunction func = dest.getRepository().getFunction("Z_POST_DOC");
func.getImportParameterList().setValue("DOC_NUM", "100001");
func.execute(dest);
```

🔧 위 코드는 Java에서 SAP의 `Z_POST_DOC` 함수(RFC)를 호출하여 전표를 생성하는 예시야.

---

## 6. 우리 시스템에서의 활용

| 위치 | 역할 |
|------|------|
| e-Doc 결재 승인 시 | SAP Function Module 호출 (RFC 방식) |
| e-Doc → SAP | `Z_POST_DOC` 등 RFC 호출하여 SAP에 전표 전송 |
| SAP → e-Doc | SAP의 RFC 호출 결과를 기반으로 전표 처리 상태 업데이트 |

📁 관련 클래스:
- `SAPUtil.java`, `SAPMassUtil.java`, `SAPDestManager.java` 등  
  → :contentReference[oaicite:0]{index=0}&#8203;:contentReference[oaicite:1]{index=1}&#8203;:contentReference[oaicite:2]{index=2}

---

## 7. 정리

```text
Function Module: SAP 내부 함수
RFC: 외부에서 이 함수를 네트워크로 호출할 수 있게 하는 방식
→ SAP ↔ Java 연동 핵심 메커니즘!
```

🧩 **전자결재 시스템이 SAP와 연동하려면 → 반드시 RFC 호출을 통해 Function Module을 실행해야 함.**


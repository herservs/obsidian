# [개념명] SAP 연동 흐름 설명 - 전자결재 시스템에서 SAP 전표 처리까지

---

## 1. 전체 연동 흐름 개요

```text
전자결재 사용자 승인
   ↓
SAP Function Module 호출 준비 (RFC)
   ↓
SAP에 전표 전송 또는 조회 (예: Z_POST_DOC)
   ↓
SAP 결과 수신 → DB 반영 or 오류 처리
```

---

## 2. 연동 주요 클래스 구조

| 클래스명 | 역할 |
|----------|------|
| `SAPDestManager` | SAP 접속 정보 설정 및 연결 객체(Destination) 생성 |
| `SAPUtil` / `SAPMassUtil` | SAP Function Module(RFC) 호출 유틸리티 (단건/대량 처리) |
| `JCoFunction` | SAP의 실제 함수 호출 객체 (Z_POST_DOC 등) |

---

## 3. 실제 코드 기반 연동 흐름 설명

---

### ✅ STEP 1: SAP 연결 객체 생성 (SAPDestManager)

```java
JCoDestination dest = SAPDestManager.getInstance().getDestination();
```

🔹 내부에서 하는 일:
- `glovis.portal.properties` 파일 로드
- JCoDestination 객체 생성 및 캐싱
- 연결 실패 시 예외 throw

> 📁 출처: `SAPDestManager.java` :contentReference[oaicite:0]{index=0}

---

### ✅ STEP 2: SAP Function Module 호출 준비

```java
JCoFunction func = dest.getRepository().getFunction("Z_POST_DOC");
```

- SAP에서 정의된 커스텀 RFC (`Z_POST_DOC`)를 호출하기 위한 함수 객체 생성
- JCoFunction은 Import/Export/Table 구조로 파라미터를 주고받음

---

### ✅ STEP 3: 파라미터 세팅 (`SAPUtil.setInputParam`)

```java
setInputParam(func, paramMap, "DOC_NUM, G_LACCT, AMOUNT, ...");
```

- 전자결재 시스템에서 받은 데이터를 SAP 입력 파라미터로 매핑
- 실제로는 `"IN.DOC_NUM"` 같은 키를 `glovis.sap.properties` 에서 변환해서 매핑

```java
iParam.setValue("DOC_NUM", "100001");
```

> 📁 출처: `SAPUtil.java` :contentReference[oaicite:1]{index=1}

---

### ✅ STEP 4: 테이블 파라미터 세팅 (`setInputTable`)

```java
setInputTable(func, paramMap, itemList, "IT_DOC", "TABLE");
```

- 전표 라인아이템 등 복수 데이터를 SAP 테이블 파라미터로 세팅
- `JCoTable.appendRow()` → `setValue(필드, 값)`

---

### ✅ STEP 5: SAP Function 실행

```java
func.execute(dest);
```

- RFC 호출 → SAP 서버에서 실제 전표 생성 수행

---

### ✅ STEP 6: 결과값 수신

```java
String eType = func.getExportParameterList().getString("E_TYPE"); // "S", "E"
String eMsg  = func.getExportParameterList().getString("E_MSG");
```

- SAP 함수의 결과 상태 ("S": 성공, "E": 에러)와 메시지를 읽어옴

---

### ✅ STEP 7: 결과 처리 및 리턴

```java
List<HashMap<String,Object>> resultList = getOutputTable(func, "T_DOC");
```

- SAP에서 리턴된 전표 번호, 상태 등을 resultList로 반환
- 이후 전자결재 시스템 DB에 상태 반영

---

## 4. 주요 포인트 요약

| 포인트 | 설명 |
|--------|------|
| SAP 연결 정보 | `glovis.portal.properties` 내에 설정됨 (JCO_ASHOST 등) |
| Function Module | `Z_`로 시작하는 SAP 커스터마이징 함수 |
| 파라미터 구조 | ImportParameter, ExportParameter, TableParameter |
| 실패 처리 | `E_TYPE = E`일 경우 SAP 오류 발생, 메시지 로깅 필요 |
| 보안 | SAP 접속 계정이 시스템 계정으로 고정되어 있음 (관리 주의) |

---

## 5. 결론

전자결재 시스템에서 SAP 연동은 다음 구조로 동작함:

```java
1. SAPDestManager → Destination 객체 생성
2. SAPUtil → Function Module 호출 준비
3. setInputParam / setInputTable → 파라미터 전달
4. func.execute → SAP에 전송
5. getExportParameterList / getOutputTable → 결과 수신 및 처리
```

SAP 전표 생성, 조회, 반려, 취소 등은 전부 이 구조로 공통화되어 있고,  
**SAP JCo + Function Module 구조**를 통해 안정적인 연동이 이루어지고 있어.


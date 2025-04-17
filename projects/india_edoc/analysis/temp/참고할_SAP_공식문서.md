# [개념명] 통합전자결재 시스템 개발 시 꼭 참고할 SAP 공식 문서 목록

---

## ✅ 1. SAP S/4HANA for Accounting Integration

- **왜 필요한가?**  
  전자결재 시스템에서 SAP로 **전표를 생성(Post)** 하거나  
  SAP로부터 **임시 전표(Parked)** 를 수신하려면 이 문서가 핵심 기준이 됨

- **포함 내용**:
  - 외부 시스템과 SAP 간 회계 문서 인터페이스 구조
  - 메시지 처리 방식 및 오류 처리 규칙
  - 저널 항목(JSON 구조 등) 연계 스펙

- **링크**:  
  🔗 [Accounting Integration](https://help.sap.com/docs/SAP_S4HANA_FOR_ACCOUNTING_INTEGRATION/0ed530f774df4567bc706a78dd5818a0/dd2c759295884833be3bd85f31021c9f.html?utm_source=chatgpt.com)

---

## ✅ 2. General Ledger Accounting (FI-GL)

- **왜 필요한가?**  
  SAP에 전표를 생성하거나 승인된 전표를 조회할 때는  
  반드시 **G/L 회계 구조**를 이해하고 있어야 함

- **포함 내용**:
  - SAP 전표 필드 구조 (Document Header, Line Item 등)
  - G/L 계정 처리 로직
  - 승인 후 SAP에 반영되는 구조 설명

- **링크**:  
  🔗 [FI-GL](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/651d8af3ea974ad1a4d74449122c620e/fbf38d5377a0ec23e10000000a174cb4.html?utm_source=chatgpt.com)

---

## ✅ 3. Financial Accounting – General Topics

- **왜 필요한가?**  
  SAP FI 모듈과 다른 SAP 모듈 (MM, SD 등) 간의 통합 개념 이해  
  연동 중 발생할 수 있는 트랜잭션 충돌이나 모듈 간 의존성 파악 가능

- **포함 내용**:
  - 회계 트랜잭션 흐름
  - 다양한 SAP 모듈과의 통합 개념
  - 전표 생성 시 내부 프로세스 설명

- **링크**:  
  🔗 [General Topics](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/8fbeed5f2046489696a50ac7fd76f9c6/68e4cf536db84408e10000000a174cb4.html?utm_source=chatgpt.com)

---

## ✅ 4. SAP S/4HANA Finance Learning Journey

- **왜 필요한가?**  
  SAP 회계 기능을 처음 연동하거나 시스템 구조를 이해하고자 할 때  
  전체 개념을 빠르게 학습할 수 있는 입문용 공식 학습 자료

- **포함 내용**:
  - SAP FI 모듈 이해
  - 회계 연동 기본기
  - 사용자 정의 및 확장 방법

- **링크**:  
  🔗 [Finance Learning Journey](https://help.sap.com/learning-journeys/5002d32d7a261014ad3081b2533c7414?utm_source=chatgpt.com)

---

## ✅ 5. 보조 참고: BAPI & RFC Function Module 목록 (필요 시)

- SAP와 연동 시 사용하는 주요 함수들 (`BAPI_ACC_DOCUMENT_POST`, `Z_POST_DOC` 등)의 구조
- SAP GUI에서 SE37 / SE80에서 직접 조회 가능
- 전자결재 시스템에서 실제 RFC 호출할 때 이 목록이 필요함

---

## ✅ 요약

| 문서명 | 설명 | 필수 여부 |
|--------|------|-----------|
| Accounting Integration | SAP 전표 연동 구조 핵심 | ✅ 필수 |
| FI-GL | SAP 전표 필드 및 회계 구조 | ✅ 필수 |
| FI General Topics | 모듈 간 트랜잭션 및 흐름 이해 | ⚠ 추천 |
| Finance Learning Journey | SAP FI 전반 학습 | 🔰 입문자 필수 |
| RFC/BAPI 목록 | 실제 호출 함수 참조용 | ⚙ 구현 시 필수 |

---

## 🧠 개발 흐름 중 문서 매핑 예시

| 개발 단계 | 참고 문서 |
|-----------|-----------|
| SAP 전표 생성 API 설계 | Accounting Integration, FI-GL |
| 전표 승인 시 SAP 연동 로직 구현 | FI-GL, RFC 함수 목록 |
| SAP 오류 메시지 해석 및 대응 | FI General Topics |
| 신규 개발자 학습용 교육 자료 | Finance Learning Journey |


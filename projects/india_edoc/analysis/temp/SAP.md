# [개념명] SAP의 위상 및 회계 연동 구조 - ERP 업계 표준 도구이자 통합 전자결재 연계 대상 시스템

---

## 1. SAP는 어떤 소프트웨어인가?

| 항목    | 설명                                                            |
| ----- | ------------------------------------------------------------- |
| 분류    | ERP (Enterprise Resource Planning) 시스템                        |
| 제공사   | SAP SE (독일 다국적 소프트웨어 회사)                                      |
| 주요 기능 | 회계(FI), 인사(HR), 구매(MM), 생산(PP), 물류(SD), 프로젝트관리(PS) 등 전사 통합 업무 |
| 사용 방식 | 기업 내부 시스템 구축형 / 클라우드형(SAP S/4 HANA Cloud) 모두 지원               |
| 주요 기술 | ABAP (내부 개발 언어), HANA (DB), RFC/BAPI (연동), Fiori (UI) 등       |

---

## 2. SAP는 업계 표준인가?

✅ 예.  
SAP는 ERP 분야에서 **글로벌/국내 모두 압도적인 점유율을 가진 사실상의 업계 표준 ERP 시스템**이다.

---

## 3. 타 기술과의 비유

| 비교 대상 | 역할 | SAP와의 관계 |
|-----------|------|---------------|
| 엑셀 | 문서, 계산의 기본 도구 | SAP는 ERP 업무 처리의 기본 도구 |
| 오라클/MySQL | DBMS (데이터 저장) | SAP HANA는 SAP 전용 DB. 전자결재 등은 별도 RDBMS 사용 |
| Spring | Java 웹 프레임워크 | SAP ABAP은 SAP 전용 개발 프레임워크 |
| Salesforce | CRM 분야 강자 | SAP는 ERP 분야의 대표 강자 |
| 더존 ERP | 국내 중소기업 ERP | SAP는 주로 대기업, 다국적 기업 중심 ERP |

---

## 4. 글로벌/국내 사용 현황

| 구분 | 내용 |
|------|------|
| 🌍 글로벌 | 190개국, 40만개 이상 기업 사용 |
| 🏢 국내 대기업 | 삼성, LG, 현대, SK, 포스코, 롯데, 네이버, 카카오 등 대부분 사용 |
| 🏭 산업군 | 제조, 물류, 유통, 금융, 공공, IT, 건설 등 전 산업군 |
| 🧩 점유율 | ERP 시장 점유율 약 35~40%로 1위 |

---

## 5. 왜 SAP가 업계 표준이 되었는가?

| 이유           | 설명                                         |
| ------------ | ------------------------------------------ |
| 기능 통합        | 회계, 구매, 생산, 물류, 품질 등 전사 업무 일괄 통합           |
| 글로벌 회계 기준 지원 | IFRS, K-GAAP 등 국제/국내 회계 기준 지원              |
| 보안 및 내부 통제   | 감사 대응 및 접근 권한 관리 체계 탑재                     |
| 확장성 및 유연성    | 커스터마이징 가능 (Z Function Module, User Exit 등) |
| 방대한 생태계      | 컨설팅/개발 파트너, 문서, 커뮤니티, 연동 기술 축적             |

---

## 6. SAP는 프레임워크인가? 솔루션인가?

| 관점 | 설명 |
|------|------|
| ✔ ERP 솔루션 | SAP S/4 HANA, SAP ECC 등 패키지형 ERP 제품 |
| ✔ ERP 플랫폼 | SAP NetWeaver, SAP BTP(Cloud), SAP GUI 등 |
| ✔ ERP 프레임워크 | SAP 내부 업무는 ABAP + RFC + BAPI + Fiori 구조로 프레임워크처럼 구성됨 |

---

## 7. 전자결재 시스템과의 연동 배경

- SAP는 ERP 표준이므로 대기업 전자결재 시스템은 반드시 SAP 회계 모듈(FI)과 연동 필요
- 회계 전표의 승인, 반려, 생성, 삭제 등 처리는 SAP Function Module을 RFC로 호출해 수행
- SAP에서는 공식 문서를 통해 외부 시스템과의 회계 연동 가이드를 제공

---

## 8. SAP 회계(Financial Accounting) 연동 관련 공식 문서 목록

### ✅ SAP S/4HANA for Accounting Integration

- **설명**: SAP S/4HANA와 외부 시스템 간의 회계 문서 통합을 위한 프레임워크 제공
- **주요 기능**:
  - 외부 전표 인터페이스 처리
  - 통합 메시지 모니터링
  - 저널 항목 송수신 처리
- **링크**:  
  🔗 [SAP S/4HANA for Accounting Integration](https://help.sap.com/docs/SAP_S4HANA_FOR_ACCOUNTING_INTEGRATION/0ed530f774df4567bc706a78dd5818a0/dd2c759295884833be3bd85f31021c9f.html?utm_source=chatgpt.com)

---

### ✅ Financial Accounting – General Topics

- **설명**: SAP S/4HANA 온프레미스 환경에서 재무 회계 일반 주제 설명
- **주요 기능**:
  - 회계 트랜잭션 처리
  - FI 모듈과 기타 모듈 간 통합
- **링크**:  
  🔗 [Financial Accounting – General Topics](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/8fbeed5f2046489696a50ac7fd76f9c6/68e4cf536db84408e10000000a174cb4.html?utm_source=chatgpt.com)

---

### ✅ General Ledger Accounting (FI-GL)

- **설명**: SAP 일반 원장 회계 기능 문서. 외부 회계 처리 핵심
- **주요 기능**:
  - 회계 문서 생성
  - G/L 계정 처리
  - Ledger 업데이트
- **링크**:  
  🔗 [General Ledger Accounting (FI-GL)](https://help.sap.com/docs/SAP_S4HANA_ON-PREMISE/651d8af3ea974ad1a4d74449122c620e/fbf38d5377a0ec23e10000000a174cb4.html?utm_source=chatgpt.com)

---

### ✅ SAP S/4HANA Finance Learning Journey

- **설명**: SAP Finance 기능을 구현/사용자정의하기 위한 개발자 학습 로드맵
- **주요 기능**:
  - SAP 회계 시스템 구성 이해
  - 사용자 정의 및 연동 구현 방법 학습
- **링크**:  
  🔗 [SAP S/4HANA Finance Learning Journey](https://help.sap.com/learning-journeys/5002d32d7a261014ad3081b2533c7414?utm_source=chatgpt.com)

---

## 9. 결론

> SAP는 단순한 ERP 솔루션이 아니라,  
> **글로벌 기업들이 기본처럼 사용하는 ERP 플랫폼이자 산업 표준 도구**다.  
> 전자결재 시스템에서 SAP 연동은 선택이 아닌 **당연한 전제 조건**이며,  
> SAP는 그 연동을 위한 다양한 공식 인터페이스와 가이드를 제공하고 있다.


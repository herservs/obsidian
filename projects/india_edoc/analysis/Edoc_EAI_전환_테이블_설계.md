# [개념명] Edoc_EAI 인터페이스 테이블 설계 목적 - SAP ↔ e-Doc 연계

## 1. 개요
이 설계서는 SAP 시스템의 데이터를 e-Doc(전자결재시스템)으로 전송하기 위한 중간 인터페이스 테이블 구조를 정의한 문서로, EAI 연계를 위한 매핑 기준을 제공한다.

## 2. 목적
- SAP 전표/마스터 데이터를 e-Doc에 전송하기 위한 **Staging 인터페이스 테이블 생성**
- 데이터 정합성과 포맷 일치를 위한 **매핑 정보 제공**
- 추후 확장 및 유지보수를 고려한 **모듈화된 테이블 설계 기반 마련**

👉 [Staging 인터페이스](./참고_Staging_인터페이스)

## 3. 주요 기능

| 기능 항목         | 설명                                                     |
| ------------- | ------------------------------------------------------ |
| SAP 데이터 수신    | SAP에서 생성된 전표(Parked, Posted) 및 마스터(G/L, 고객, 구매처) 정보 수신 |
| 인터페이스 테이블 저장  | SAP 데이터를 내부 포맷에 맞춰 e-Doc 전용 인터페이스 테이블에 저장              |
| 컬럼 매핑         | 데이터 타입, 길이, 컬럼명 등의 구조를 명시하여 시스템 간 데이터 정합성 확보           |
| 결재요청 데이터 변환   | 인터페이스 테이블의 데이터를 기반으로 e-Doc 결재요청 대상 데이터 생성              |
| 로그 및 처리 상태 관리 | 전송 여부, 성공/실패 상태 등 EAI 상태 추적 가능                         |

## 4. 설계 흐름

1. SAP → e-Doc 인터페이스 대상 테이블 목록 정의
2. 각 테이블에 필요한 컬럼 정의 (PK, 데이터타입 등)
3. SAP ↔ e-Doc 간 전송 데이터 매핑 구조 확립
4. e-Doc 시스템에서 해당 인터페이스 테이블을 조회하여 결재요청 생성

## 5. 예시 테이블

| 테이블명 | 설명 |
|----------|------|
| EDOC_DYN_DOC_PARK_HEADER | SAP 임시전표 헤더 정보 수신용 인터페이스 테이블 |
| EDOC_DYN_DOC_PARK_ITEM | SAP 임시전표 항목 정보 수신용 인터페이스 테이블 |
| EDOC_MASTER_VENDOR | SAP 구매처 마스터 수신용 테이블 |
| EDOC_MASTER_GL | SAP 총계정 마스터 수신용 테이블 |

## 6. 활용 시나리오

- SAP에서 5분 주기로 자동 전송되는 전표 데이터를 EDOC_DYN_DOC_PARK_HEADER에 저장
- e-Doc 시스템이 해당 테이블을 읽어 결재요청 대상 데이터를 생성
- 결재 승인 후 SAP 연동 또는 내부 처리 완료

## 7. 기타 참고사항

- SAP ↔ e-Doc 간 연동은 EAI 또는 Batch 방식으로 구현 가능
- SAP의 인터페이스 전표 유형 설정은 ZUSFIV9110 T-Code 사용
- 확장성을 위해 각 인터페이스 테이블은 유연한 필드 구조로 설계됨


---

# [개념명] Edoc_EAI 전환 테이블 설계서 시트 요약

| 시트명                        | 주요 내용 요약                                                        | 분석 관점 설명 |
|-----------------------------|-----------------------------------------------------------------------|----------------|
| Overview                    | 전체 문서의 개요 설명 (Unnamed: 0 필드 존재)                           | 전체 문서 목적 및 개요 설명. 시스템 설계 방향 파악에 도움. |
| Table List                  | Category, Table, Description 컬럼 구성                                 | 전환 대상 테이블 목록. 전환 범위 및 마이그레이션 대상 테이블 파악에 유용. |
| EDOC_DYN_DOC_PARK_HEADER    | 전표 헤더: Column, Data Type, Size, PK 등 포함                         | 임시전표 헤더 정보 인터페이스 테이블. SAP → eDoc 전환의 헤더 매핑 구조 이해에 필수. |
| EDOC_DYN_DOC_PARK_ITEM      | 전표 항목: Column, Data Type, Size, PK 등 포함                         | 전표 항목 단위 처리 로직 설계에 참고. |
| EDOC_DYN_ASYNC_POST_RESULT  | SAP Posting 결과 수신 테이블 구조                                     | SAP Posting 결과 수신 테이블. eDoc에서 SAP 전표 처리 결과를 반영하는 데 활용. |
| EDOC_MST_CUSTOMER           | 고객 마스터 정보 테이블 구조                                          | 고객 마스터 전환 테이블. SAP의 고객 데이터를 eDoc에서 어떻게 쓰는지 파악 가능. |
| EDOC_MST_DOC_TYPE           | 전자결재 문서유형 정의 테이블                                          | 문서 유형 마스터. 전자결재 문서의 유형을 구분하는 기준 제공. |
| EDOC_MST_GL_COMPANY         | 회사코드 마스터 테이블                                                | GL 회사코드 마스터. SAP와 연동되는 조직 단위 구분을 위해 필수. |
| EDOC_MST_GL                 | 계정 마스터 테이블 구조                                               | 계정코드 마스터. 계정 처리 시 코드 매핑이 필요한 경우 참고. |
| EDOC_MST_VENDOR             | 공급업체(구매처) 마스터 테이블                                       | 구매처 마스터. 공급업체 관련 전자결재 연동 분석에 필요. |
| EDOC_MST_COST_CENTER        | 코스트센터 마스터 테이블                                              | 코스트센터 마스터. 조직 내 비용 집행 위치 매핑 구조 파악에 도움. |

---
# [테이블정의서] Table List

전환 대상 테이블 목록으로, 시스템 인터페이스 대상이 되는 테이블의 분류와 설명이 포함된다.

| No | 카테고리 (Category) | 테이블명 (Table) | 영문 설명 | 한글 설명 |
|----|----------------------|------------------|------------------------------|-----------------------------|
| 1  | Transaction          | EDOC_DYN_DOC_PARK_HEADER    | Parked Document I/F Table(Header)       | 임시전표 인터페이스 테이블(헤더) |
| 2  | Transaction          | EDOC_DYN_DOC_PARK_ITEM      | Parked Document I/F Table(Item)         | 임시전표 인터페이스 테이블(항목) |
| 3  | Transaction          | EDOC_DYN_ASYNC_POST_RESULT  | Post/Reverse/Delete Result I/F Table    | 전표 전기/취소/삭제 결과 인터페이스 테이블 |
| 4  | Master               | EDOC_MST_CUSTOMER           | Customer master data I/F                | 고객 마스터 데이터 인터페이스 |
| 5  | Master               | EDOC_MST_DOC_TYPE           | Document Type master data I/F           | 문서유형 마스터 데이터 인터페이스 |
| 6  | Master               | EDOC_MST_GL_COMPANY         | GL Account by Company I/F               | 회사별 계정코드 인터페이스 |
| 7  | Master               | EDOC_MST_GL                 | GL Account (COA) I/F                     | 계정과목 인터페이스 |
| 8  | Master               | EDOC_MST_VENDOR             | Vendor master data I/F                  | 공급업체 마스터 데이터 인터페이스 |
| 9  | Master               | EDOC_MST_COST_CENTER        | Cost Center master data I/F             | 코스트센터 마스터 데이터 인터페이스 |


---

# [테이블정의서] EDOC_DYN_DOC_PARK_HEADER

SAP → e-Doc 전환 과정에서 사용되는 **전표 헤더 인터페이스 테이블**로, 회사코드, 문서번호, 연도 등 주요 전표 메타데이터를 포함한다.

| No | 컬럼명 (Column) | 자료형 (Data Type) | 길이 (Size) | PK | 영문 설명 | 한글 설명 |
|----|------------------|---------------------|-------------|-----|------------------------------|----------------------------|
| 1  | COMPANY_CODE     | VARCHAR             | 10          | X   | Company Code                 | 회사 코드 |
| 2  | DOC_NO           | VARCHAR             | 10          | X   | Accounting Document Number   | 회계 문서 번호 |
| 3  | POST_YEAR        | INT                 | 4           | X   | Fiscal Year                  | 회계 연도 |
| 4  | DOC_TYPE         | VARCHAR             | 4           |     | Document Type                | 문서 유형 |
| 5  | DOC_DATE         | DATETIME            |             |     | Document Date in Document    | 문서 작성 일자 |
| 6  | POST_DATE        | DATETIME            |             |     | Posting Date in the Document | Posting Date in the Document |
| 7  | POST_MONTH       | INT                 | 2           |     | Fiscal Period                | Fiscal Period |
| 8  | REQ_DATE         | DATETIME            |             |     | Day On Which Accounting Document Was Entered | Day On Which Accounting Document Was Entered |
| 9  | INVOICE_NO       | VARCHAR             | 100         |     | Reference Document Number    | Reference Document Number |
| 10 | DOC_TITLE        | VARCHAR             | 100         |     | Document Header Text         | Document Header Text |
| 11 | DOC_CURRENCY     | VARCHAR             | 5           |     | Currency Key                 | Currency Key |
| 12 | ASSIGNMENT       | VARCHAR             | 18          |     | Assignment Number            | Assignment Number |
| 13 | WORK_TYPE        | VARCHAR             | 10          |     | Work Type                    | Work Type |
| 14 | MAX_GL_ACC_CODE  | VARCHAR             | 10          |     | Max Amount GL Account in document | Max Amount GL Account in document |
| 15 | MAX_SUB_GL_CODE  | VARCHAR             | 10          |     | Max Amount Sub GL Account in document | Max Amount Sub GL Account in document |
| 16 | MAX_AMOUNT       | DOUBLE              | 16,5        |     | Max Amount in document       | Max Amount in document |
| 17 | TRANS_DATE       | DATETIME            |             |     | Translation Date             | Translation Date |
| 18 | LOCAL_CURRENCY   | VARCHAR             | 5           |     | Local Currency Key           | Local Currency Key |
| 19 | EX_RATE          | DOUBLE              | 16,5        |     | Exchange Rate                | Exchange Rate |
| 20 | SAP_USER_NAME    | VARCHAR             | 80          |     | SAP User Name                | SAP User Name |
| 21 | SAP_DOC_STATUS   | VARCHAR             | 2           |     | SAP Document Status          | SAP Document Status |
| 22 | XREF01           | VARCHAR             | 20          |     |                              |                              |
| 23 | XREF02           | VARCHAR             | 20          |     |                              |                              |
| 24 | UPDATE_USER      | VARCHAR             | 50          |     | Update User Name             | Update User Name |
| 25 | UPDATE_DATE      | DATETIME            |             |     | Update Date                  | Update Date |

---
# [테이블정의서] EDOC_DYN_DOC_PARK_ITEM

SAP 전표의 라인 항목 정보를 담는 테이블로, 계정, 금액, 거래처 등의 정보가 포함된다.

| No | 컬럼명 (Column) | 자료형 (Data Type) | 길이 (Size) | PK | 영문 설명 | 한글 설명 |
|----|------------------|---------------------|-------------|-----|-------------|--------------|
| 1 | COMPANY_CODE | VARCHAR | 10 | X | Company Code | 회사 코드 |
| 2 | DOC_NO | VARCHAR | 10 | X | Accounting Document Number | 회계 문서 번호 |
| 3 | POST_YEAR | INT | 4 | X | Fiscal Year | 회계 연도 |
| 4 | ITEM_NO | INT | 5 | X | Number of Line Item Within Accounting Document | Number of Line Item Within Accounting Document |
| 5 | POST_KEY | VARCHAR | 2 | nan | Posting Key | 전기 키 |
| 6 | ENTRY_TYPE | VARCHAR | 10 | nan | Debit/Credit Indicator | Debit/Credit Indicator |
| 7 | BIZ_CODE | VARCHAR | 4 | nan | Business Area | Business Area |
| 8 | TAX_CODE | VARCHAR | 2 | nan | Sales Tax Code | Sales Tax Code |
| 9 | LOCAL_AMOUNT | DOUBLE | 16,5 | nan | Amount in Local Currency | Amount in Local Currency |
| 10 | DOC_AMOUNT | DOUBLE | 16,5 | nan | Amount in Document Currency | Amount in Document Currency |
| 11 | ASSIGN_NO | VARCHAR | 100 | nan | Assignment Number | Assignment Number |
| 12 | ITEM_REMARK | VARCHAR | 100 | nan | Item Text | Item Text |
| 13 | PAYMENT_PLAN_DATE | DATETIME | nan | nan | Planning Date | Planning Date |
| 14 | COST_CENTER | VARCHAR | 10 | nan | Cost Center | 코스트 센터 |
| 15 | SAKNR | VARCHAR | 10 | nan | G/L Account Number | G/L Account Number |
| 16 | HKONT | VARCHAR | 10 | nan | General Ledger Account / Vendor - Customer GL Account | General Ledger Account / Vendor - Customer GL Account |
| 17 | CUST_CODE | VARCHAR | 30 | nan | Customer Number 1 / Customer Code | Customer Number 1 / Customer Code |
| 18 | VENDOR_CODE | VARCHAR | 30 | nan | Account Number of Vendor or Creditor | Account Number of Vendor or Creditor |
| 19 | QTY | VARCHAR | 13 | nan | Quantity | Quantity |
| 20 | UNIT | VARCHAR | 3 | nan | Base Unit of Measure | Base Unit of Measure |
| 21 | PAYMENT_BASE_DATE | DATETIME | nan | nan | Baseline Date for Due Date Calculation | Baseline Date for Due Date Calculation |
| 22 | PAYMENT_TERM | VARCHAR | 10 | nan | Terms of Payment Key | Terms of Payment Key |
| 23 | PAYMENT_DAYS | INT | 3 | nan | Cash Discount Days 1 | Cash Discount Days 1 |
| 24 | PAYMENT_METHOD | VARCHAR | 10 | nan | Payment Method | Payment Method |
| 25 | REFER01 | VARCHAR | 12 | nan | Business Partner Reference Key | Business Partner Reference Key |
| 26 | REFER02 | VARCHAR | 12 | nan | Business Partner Reference Key | Business Partner Reference Key |
| 27 | REFER03 | VARCHAR | 20 | nan | Reference Key for Line Item | Reference Key for Line Item |
| 28 | PAYMENT_BLOCK | VARCHAR | 1 | nan | Payment Block | Payment Block |
| 29 | ASSET_TR_TYPE | VARCHAR | 3 | nan | Asset Transaction Type | Asset Transaction Type |
| 30 | COPA_CUST | VARCHAR | 10 | nan | nan | nan |
| 31 | COPA_CUST_TEXT | VARCHAR | 20 | nan | nan | nan |
| 32 | SALES01 | VARCHAR | 2 | nan | nan | nan |
| 33 | SALES01_TEXT | VARCHAR | 20 | nan | nan | nan |
| 34 | SALES02 | VARCHAR | 4 | nan | nan | nan |
| 35 | SALES02_TEXT | VARCHAR | 20 | nan | nan | nan |
| 36 | SALES03 | VARCHAR | 6 | nan | nan | nan |
| 37 | SALES03_TEXT | VARCHAR | 20 | nan | nan | nan |
| 38 | PORTS | VARCHAR | 2 | nan | Ports | Ports |
| 39 | PORT_TEXT | VARCHAR | 20 | nan | Name of characteristic valuse | Name of characteristic valuse |
| 40 | COMPOUND | VARCHAR | 3 | nan | Compound | Compound |
| 41 | COMPOUND_TEXT | VARCHAR | 20 | nan | Name of characteristic valuse | Name of characteristic valuse |
| 42 | ACC_NO_VENDOR | VARCHAR | 10 | nan | Account Number of Vendor or Creditor | Account Number of Vendor or Creditor |
| 43 | ACC_NO_VENDOR_TEXT | VARCHAR | 35 | nan | Name of characteristic valuse | Name of characteristic valuse |
| 44 | SUB_GL_CODE | VARCHAR | 6 | nan | Sub G/L | Sub G/L |
| 45 | SUB_GL_TEXT | VARCHAR | 20 | nan | Sub G/L Text | Sub G/L Text |
| 46 | TRUCK_NO | VARCHAR | 8 | nan | Truck No | Truck No |
| 47 | SALES_OFFICE | VARCHAR | 4 | nan | Sales Office | Sales Office |
| 48 | SALES_OFFICE_TEXT | VARCHAR | 20 | nan | Sales Office Text | Sales Office Text |
| 49 | PA_COST_CENTER | VARCHAR | 10 | nan | PA Cost Center | PA Cost Center |
| 50 | PA_COST_CENTER_TEXT | VARCHAR | 40 | nan | PA Cost Center Text | PA Cost Center Text |
| 51 | PA_BILL_TO | VARCHAR | 10 | nan | PA Bill to Party | PA Bill to Party |
| 52 | PA_BILL_TO_TEXT | VARCHAR | 35 | nan | PA Bill to Party Text | PA Bill to Party Text |
| 53 | TJC | VARCHAR | 15 | nan | TJC | TJC |
| 54 | ACCOUNT_TYPE | VARCHAR | 10 | nan | Account Type | Account Type |
| 55 | GL_ACC_CODE | VARCHAR | 10 | nan | GL Account Code | GL 계정 코드 |
| 56 | FS_CATEGORY | VARCHAR | 3 | nan | nan | nan |
| 57 | DOC_CURRENCY | VARCHAR | 5 | nan | Document Currency | Document Currency |
| 58 | LOCAL_CURRENCY | VARCHAR | 5 | nan | Local Currency | Local Currency |
| 59 | ALT_RECON_ACC_CODE | VARCHAR | 10 | nan | nan | nan |
| 60 | FUND_CENTER | VARCHAR | 16 | nan | nan | nan |
| 61 | UPDATE_USER | VARCHAR | 50 | nan | Update User Name | Update User Name |
| 62 | UPDATE_DATE | DATETIME | nan | nan | Update Date | Update Date |

---

# [테이블정의서] EDOC_DYN_ASYNC_POST_RESULT

SAP 전표 처리 결과를 비동기로 수신받는 테이블로, 상태 및 메시지 정보를 포함한다.

| No | 컬럼명 (Column) | 자료형 (Data Type) | 길이 (Size) | PK | 영문 설명 | 한글 설명 |
|----|------------------|---------------------|-------------|-----|-------------|--------------|
| 1 | COMPANY_CODE | VARCHAR | 10 | X | Company Code | 회사 코드 |
| 2 | DOC_NO | VARCHAR | 10 | X | Accounting Document Number | 회계 문서 번호 |
| 3 | POST_YEAR | INT | 4 | X | Fiscal Year | 회계 연도 |
| 4 | DOC_STATUS | VARCHAR | 2 | nan | Document Type | 문서 유형 |
| 5 | SAP_REVERSE_NO | VARCHAR | 10 | nan | SAP Reverse No | SAP Reverse No |
| 6 | SAP_REVERSE_YEAR | VARCHAR | 4 | nan | SAP Reverse Post Year | SAP Reverse Post Year |
| 7 | SAP_REVERSE_DATE | DATETIME | 2 | nan | SAP Reverse Date | SAP Reverse Date |
| 8 | E_TYPE | VARCHAR | 2 | nan | Result | Result |
| 9 | E_MSG | VARCHAR | 220 | nan | Result Description | Result Description |

---
# [테이블정의서] EDOC_MST_CUSTOMER

SAP 고객 정보를 e-Doc 시스템에 연동하기 위한 마스터 테이블이다.

| No | 컬럼명 (Column) | 자료형 (Data Type) | 길이 (Size) | PK | 영문 설명 | 한글 설명 |
|----|------------------|---------------------|-------------|-----|-------------|--------------|
| 1 | COMPANY_CODE | VARCHAR | 10.0 | X | Company Code | 회사 코드 |
| 2 | CUST_CODE | VARCHAR | 10.0 | X | Customer Code | Customer Code |
| 3 | CUST_NAME | VARCHAR | 300.0 | nan | Customer Name | Customer Name |
| 4 | CUST_GRP_CODE | VARCHAR | 10.0 | nan | Group Code | Group Code |
| 5 | ADDRESS | VARCHAR | 500.0 | nan | blank | blank |
| 6 | COUNTRY_CODE | VARCHAR | 10.0 | nan | County Code | County Code |
| 7 | STATE | VARCHAR | 30.0 | nan | State | State |
| 8 | CITY | VARCHAR | 30.0 | nan | City | City |
| 9 | STREET1 | VARCHAR | 200.0 | nan | Address | Address |
| 10 | STREET2 | VARCHAR | 200.0 | nan | blank | blank |
| 11 | PO_BOX | VARCHAR | 10.0 | nan | PO box | PO box |
| 12 | ZIP_CODE | VARCHAR | 10.0 | nan | ZIP code | ZIP code |
| 13 | TAX_NO | VARCHAR | 16.0 | nan | TAX ID | TAX ID |
| 14 | PHONE | VARCHAR | 20.0 | nan | Phone | Phone |
| 15 | CONTACT_EMAIL | VARCHAR | 100.0 | nan | e-mail | e-mail |
| 16 | CREATE_USER | VARCHAR | 50.0 | nan | Create User Name | Create User Name |
| 17 | UPDATE_USER | VARCHAR | 50.0 | nan | Update User Name | Update User Name |
| 18 | UPDATE_DATE | DATETIME | nan | nan | Update Date | Update Date |


---

# [테이블정의서] EDOC_MST_DOC_TYPE

전자결재 문서 유형 마스터 테이블로, 각 문서의 종류별 구분 정보를 담는다.

| No | 컬럼명 (Column) | 자료형 (Data Type) | 길이 (Size) | PK | 영문 설명 | 한글 설명 |
|----|------------------|---------------------|-------------|-----|-------------|--------------|
| 1 | COMPANY_CODE | VARCHAR | 10.0 | X | Company Code | 회사 코드 |
| 2 | WORK_TYPE | VARCHAR | 20.0 | X | Work Type(GL/AP/AR) | Work Type(GL/AP/AR) |
| 3 | DOC_TYPE | VARCHAR | 20.0 | X | Document Type | 문서 유형 |
| 4 | DOC_NAME | VARCHAR | 50.0 | nan | Document Type Description | Document Type Description |
| 5 | REV_DOC_TYPE | VARCHAR | 20.0 | nan | Reverse Document type | Reverse Document type |
| 6 | ASSET_FLAG | VARCHAR | 1.0 | nan | Asset Flag | Asset Flag |
| 7 | CUST_FLAG | VARCHAR | 1.0 | nan | Customer Flag | Customer Flag |
| 8 | VENDOR_FLAG | VARCHAR | 1.0 | nan | Vendor Flag | Vendor Flag |
| 9 | GL_FLAG | VARCHAR | 1.0 | nan | GL Flag | GL Flag |
| 10 | INTER_COM_FLAG | VARCHAR | 1.0 | nan | Intercompany Posting Flag | Intercompany Posting Flag |
| 11 | CREATE_USER | VARCHAR | 50.0 | nan | Create User Name | Create User Name |
| 12 | UPDATE_USER | VARCHAR | 50.0 | nan | Update User Name | Update User Name |
| 13 | UPDATE_DATE | DATETIME | nan | nan | Update Date | Update Date |

---

# [테이블정의서] EDOC_MST_GL_COMPANY

SAP GL 회사코드와 명칭 정보를 관리하는 테이블이다.

| No | 컬럼명 (Column) | 자료형 (Data Type) | 길이 (Size) | PK | 영문 설명 | 한글 설명 |
|----|------------------|---------------------|-------------|-----|-------------|--------------|
| 1 | COA_CODE | VARCHAR | 10.0 | X | Chart of Account | Chart of Account |
| 2 | COMPANY_CODE | VARCHAR | 10.0 | X | Company Code | 회사 코드 |
| 3 | GL_ACC_CODE | VARCHAR | 10.0 | X | GL Account | GL Account |
| 4 | GL_ACC_TYPE | VARCHAR | 10.0 | nan | GL Account Type | GL Account Type |
| 5 | CURRENCY | VARCHAR | 5.0 | nan | Currency | Currency |
| 6 | OPEN_ITEM_FLAG | VARCHAR | 1.0 | nan | Open Item Flag | Open Item Flag |
| 7 | DEL_FLAG | VARCHAR | 1.0 | nan | Deletion Flag | Deletion Flag |
| 8 | CREATE_USER | VARCHAR | 50.0 | nan | Creation user | Creation user |
| 9 | CREATE_DATE | DATETIME | nan | nan | Creation date | Creation date |
| 10 | UPDATE_USER | VARCHAR | 50.0 | nan | Update User Name | Update User Name |
| 11 | UPDATE_DATE | DATETIME | nan | nan | Update Date | Update Date |

---

# [테이블정의서] EDOC_MST_GL

SAP G/L 계정 코드 및 명칭 정보를 관리하는 마스터 테이블이다.

| No | 컬럼명 (Column) | 자료형 (Data Type) | 길이 (Size) | PK | 영문 설명 | 한글 설명 |
|----|------------------|---------------------|-------------|-----|-------------|--------------|
| 1 | COA_CODE | VARCHAR | 10.0 | X | Chart of Account | Chart of Account |
| 2 | GL_ACC_CODE | VARCHAR | 10.0 | X | G/L Account | G/L 계정 |
| 3 | GL_ACC_NAME | VARCHAR | 50.0 | nan | G/L Account Name - Long | G/L Account Name - Long |
| 4 | FS_CATEGORY | VARCHAR | 10.0 | nan | G/L Account Group | G/L Account Group |
| 5 | BS_ACC_FLAG | VARCHAR | 1.0 | nan | Balance Sheet Account ? | Balance Sheet Account ? |
| 6 | DEL_FLAG | VARCHAR | 1.0 | nan | Deletion Flag | Deletion Flag |
| 7 | CREATE_USER | VARCHAR | 50.0 | nan | Create User Name | Create User Name |
| 8 | CREATE_DATE | DATETIME | nan | nan | Create Date | Create Date |
| 9 | UPDATE_USER | VARCHAR | 50.0 | nan | Update User Name | Update User Name |
| 10 | UPDATE_DATE | DATETIME | nan | nan | Update Date | Update Date |

---

# [테이블정의서] EDOC_MST_VENDOR

구매처(공급업체) 정보를 연동하기 위한 마스터 테이블이다.

| No | 컬럼명 (Column) | 자료형 (Data Type) | 길이 (Size) | PK | 영문 설명 | 한글 설명 |
|----|------------------|---------------------|-------------|-----|-------------|--------------|
| 1 | COMPANY_CODE | VARCHAR | 10.0 | X | Company Code | 회사 코드 |
| 2 | WORK_TYPE | VARCHAR | 20.0 | X | Work Type(GL/AP/AR) | Work Type(GL/AP/AR) |
| 3 | DOC_TYPE | VARCHAR | 20.0 | X | Document Type | 문서 유형 |
| 4 | DOC_NAME | VARCHAR | 50.0 | nan | Document Type Description | Document Type Description |
| 5 | REV_DOC_TYPE | VARCHAR | 20.0 | nan | Reverse Document type | Reverse Document type |
| 6 | ASSET_FLAG | VARCHAR | 1.0 | nan | Asset Flag | Asset Flag |
| 7 | CUST_FLAG | VARCHAR | 1.0 | nan | Customer Flag | Customer Flag |
| 8 | VENDOR_FLAG | VARCHAR | 1.0 | nan | Vendor Flag | Vendor Flag |
| 9 | GL_FLAG | VARCHAR | 1.0 | nan | GL Flag | GL Flag |
| 10 | INTER_COM_FLAG | VARCHAR | 1.0 | nan | Intercompany Posting Flag | Intercompany Posting Flag |
| 11 | CREATE_USER | VARCHAR | 50.0 | nan | Create User Name | Create User Name |
| 12 | UPDATE_USER | VARCHAR | 50.0 | nan | Update User Name | Update User Name |
| 13 | UPDATE_DATE | DATETIME | nan | nan | Update Date | Update Date |


---

# [테이블정의서] EDOC_MST_COST_CENTER

조직 내 비용 집행 단위인 코스트센터 정보를 관리하는 테이블이다.

| No | 컬럼명 (Column) | 자료형 (Data Type) | 길이 (Size) | PK | 영문 설명 | 한글 설명 |
|----|------------------|---------------------|-------------|-----|-------------|--------------|
| 1 | COST_CENTER | VARCHAR | 10.0 | X | Cost Center | 코스트 센터 |
| 2 | CONTROL_AREA | VARCHAR | 4.0 | X | Controlling Area | Controlling Area |
| 3 | COMPANY_CODE | VARCHAR | 10.0 | nan | Company Code | 회사 코드 |
| 4 | VALID_TO | DATETIME | nan | nan | Valid To Date | Valid To Date |
| 5 | VALID_FROM | DATETIME | nan | nan | Valid-From Date | Valid-From Date |
| 6 | BIZ_CODE | VARCHAR | 4.0 | nan | Business Area | Business Area |
| 7 | CATEGORY | VARCHAR | 1.0 | nan | Cost Center Category | Cost Center Category |
| 8 | RES_PERSON | VARCHAR | 20.0 | nan | Person Responsible | Person Responsible |
| 9 | CURRENCY | VARCHAR | 5.0 | nan | Currency Key | Currency Key |
| 10 | ENTER_ON | DATETIME | nan | nan | Entered On | Entered On |
| 11 | HIER_AREA | VARCHAR | 12.0 | nan | Standard Hierarchy Area | Standard Hierarchy Area |
| 12 | OBJ_NO | VARCHAR | 22.0 | nan | Object number | Object number |
| 13 | GEN_NAME | VARCHAR | 20.0 | nan | General Name | General Name |
| 14 | GEN_DESC | VARCHAR | 40.0 | nan | Description | Description |
| 15 | SEARCH_TERM | VARCHAR | 20.0 | nan | Search Term for Matchcode Use | Search Term for Matchcode Use |
| 16 | CREATE_USER | VARCHAR | 50.0 | nan | Create User Name | Create User Name |
| 17 | UPDATE_USER | VARCHAR | 50.0 | nan | Update User Name | Update User Name |
| 18 | UPDATE_DATE | DATETIME | nan | nan | Update Date | Update Date |

# [개념명] E-Doc ↔ SAP EAI Interface 정리

## 1. 개요
e-Doc 시스템과 SAP S/4HANA 간 전표 데이터 및 마스터 데이터를 연동하기 위한 EAI 인터페이스 목록 정리.  
전표 등록, 상태 변경, 마스터 정보 연계 등을 실시간 또는 배치 방식으로 처리.

---

## 2. Interface 분류

### ✅ EDOC → SAP (전표 처리, 제출 등)
| No | Interface ID | 기능 | 비고 |
|----|---------------|------|------|
| 1 | IF_GUS_FI_EDOC_0054 | 역전송 전 데이터 유효성 검사 | Reverse 전 Clearing Check 등 |
| 2 | IF_GUS_FI_EDOC_0055 | 대량 업로드 전 데이터 유효성 체크 | |
| 3 | IF_GUS_FI_EDOC_0064 | 대량 파킹 등록 (재무팀) | 승인 없이 다중 전표 파킹 가능 |
| 4 | IF_GUS_FI_EDOC_0065 | 대량 파킹 등록 (운영팀) | 최종 승인 포함 |
| 5 | IF_GUS_FI_EDOC_0070 | 파킹 전표 SAP에 Post 처리 | |
| 6 | IF_GUS_FI_EDOC_0071 | 결재 문서 Reject | |
| 7 | IF_GUS_FI_EDOC_0072 | 문서 상태 초기화 (Review Pending) | 재결재용 |
| 8 | IF_GUS_FI_EDOC_0074 | 포스팅된 전표 Reverse | |
| 9 | IF_GUS_FI_EDOC_0075 | 파킹 전표 결재 요청 제출 | |
| 10 | IF_GUS_FI_EDOC_0079 | 선지급/보류 결재 요청 제출 | |
| 11 | IF_GUS_FI_EDOC_0093 | 전표 삭제 요청 | 불필요 문서 삭제 처리 |

---

### ✅ SAP → EDOC (전표 변경 수신, 마스터 데이터 수신 등)
| No | Interface ID | 기능 | 비고 |
|----|---------------|------|------|
| 12 | IF_GUS_FI_EDOC_0102 | 파킹 전표 변경사항 수신 | |
| 13 | IF_GUS_FI_EDOC_0057 | 고객 마스터 연동 | Customer Master I/F |
| 14 | IF_GUS_FI_EDOC_0060 | 전표유형 마스터 연동 | Document Type I/F |
| 15 | IF_GUS_FI_EDOC_0061 | 회사별 총계정원장 계정 연동 | GL Account by Company |
| 16 | IF_GUS_FI_EDOC_0062 | COA 총계정원장 계정 연동 | |
| 17 | IF_GUS_FI_EDOC_0063 | 파킹 전표 수신 | |
| 18 | IF_GUS_FI_EDOC_0076 | 포스팅 후 문서 상태 업데이트 | |
| 19 | IF_GUS_FI_EDOC_0078 | 공급업체 마스터 연동 | Vendor Master I/F |
| 20 | IF_GUS_FI_EDOC_0085 | 포스팅 전표 수신 | |
| 21 | IF_GUS_FI_EDOC_0092 | 코스트센터 마스터 연동 | Cost Center I/F |

---

## 3. 인터페이스 방식 요약

| Method | 설명 |
|--------|------|
| EAI | Enterprise Application Integration (API 연동 기반 실시간 또는 주기적 동기화) |

| Sender → Receiver |
|-------------------|
| EDOC → SAP (전표 제출, 반려, 리셋, 삭제 등) |
| SAP → EDOC (전표 변경, 마스터 정보 수신 등) |

---

## 4. 참고 사항
- 인터페이스 ID는 `IF_GUS_FI_EDOC_xxxx` 형식으로 관리됨
- 대부분 EAI 방식으로 실시간 또는 주기적 데이터 연동 수행
- 대량 업로드, 전표 상태 변경, 마스터 데이터 등 모든 회계 관련 주요 프로세스를 커버


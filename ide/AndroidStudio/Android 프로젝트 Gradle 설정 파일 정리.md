# [개념명] Android 프로젝트 Gradle 설정 파일 정리

## 1. 개요
Android 프로젝트에서 사용하는 주요 Gradle 설정 파일 3가지에 대해  
목적, 사용하는 시점, 설정 위치를 표 형식으로 정리한 문서

## 2. 주요 설정 파일 비교

| 항목               | 설정 파일                       | 목적                 | 주요 설정 항목                                                                                                                                                        | 파일 위치                                       |
| ---------------- | --------------------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| 🟦 프로젝트 수준 설정    | `build.gradle` (루트)         | 프로젝트 전반 설정         | - Kotlin 플러그인 버전<br>- Gradle 플러그인 classpath<br>- 공통 저장소 설정 (`repositories`)                                                                                     | `/build.gradle`                             |
| 🟩 모듈 수준 설정      | `app/build.gradle`          | 앱 모듈의 개별 빌드 설정     | - `compileSdkVersion`, `minSdkVersion`, `targetSdkVersion`<br>- 앱 버전, 앱 ID<br>- Java/Kotlin 설정 (`compileOptions`, `kotlinOptions`)<br>- 종속성 설정 (`dependencies`) | `/app/build.gradle`                         |
| 🟧 Gradle 실행기 설정 | `gradle-wrapper.properties` | Gradle 버전 및 실행기 제어 | - Gradle 버전 설정 (`distributionUrl`)<br>- JDK와의 호환성 보장                                                                                                            | `/gradle/wrapper/gradle-wrapper.properties` |

## 3. 사용 시점 요약

| 상황 | 수정 대상 |
|------|-----------|
| Java 버전 업그레이드 (ex. 17) | 🔧 `gradle-wrapper.properties`<br>🔧 `app/build.gradle` |
| Android SDK 또는 버전 변경 | 🔧 `app/build.gradle` |
| Gradle 빌드 에러 또는 버전 낮을 때 | 🔧 `gradle-wrapper.properties`<br>🔧 루트 `build.gradle` |
| 라이브러리 추가 및 설정 | 🔧 `app/build.gradle` |
| Kotlin 버전 업그레이드 | 🔧 루트 `build.gradle` |

## 4. 정리

- `gradle-wrapper.properties`: Gradle 버전 제어
- 루트 `build.gradle`: 전역 설정
- `app/build.gradle`: 앱 세부 설정
- 세 파일은 **함께 조정해야 호환성 및 빌드 오류 없이 안정적인 개발 가능**

---

## 5. 프로젝트/모듈 설정이 다를 수 있는가?

### ✅ 결론: 서로 다른 설정이 **가능**하지만, **되도록 일치시키는 것이 안정적**

| 항목 | 가능 여부 | 설명 |
|------|------------|-------|
| Java/Kotlin 버전 | ✅ 가능 | 실험용 모듈만 별도 JVM 타겟 설정 가능 |
| SDK 버전 (`minSdk`, `targetSdk`) | ✅ 가능 | 모듈별로 타겟 기기 다르게 설정 가능 |
| Gradle Plugin 버전 vs Gradle 버전 | ⚠️ 안 맞으면 에러 | 반드시 호환 조합 사용해야 함 |
| Kotlin 버전 | ⚠️ 다르면 충돌 가능 | 전역으로 통일 권장 |

### 📌 실무 팁

- Java/Kotlin 관련 설정은 **모든 모듈에서 동일하게 맞추는 게 유지보수에 유리**
- 테스트/실험 모듈은 다르게 설정할 수도 있음
- Gradle Plugin과 Gradle 버전은 항상 공식 호환 표 확인할 것


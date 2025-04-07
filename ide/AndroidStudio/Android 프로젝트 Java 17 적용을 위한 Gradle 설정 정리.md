# [개념명] Android 프로젝트 Java 17 적용을 위한 Gradle 설정 정리

## 1. 개요
Java 17을 Android 프로젝트에 적용하기 위해  
수정한 모든 Gradle 설정 사항을 파일별로 정리한 문서

---

## 2. 수정 항목 요약표

| 설정 파일 | 수정 항목 | 수정 이유 |
|------------|------------|------------|
| `gradle-wrapper.properties` | `distributionUrl=https\://services.gradle.org/distributions/gradle-7.6-all.zip` | Gradle 7.6 이상만 Java 17을 지원하기 때문에 필수 |
| 루트 `build.gradle` | `classpath 'com.android.tools.build:gradle:7.4.2'` | Android Gradle Plugin 7.0 이상이 Java 17과 호환 |
| `app/build.gradle` | `compileOptions { sourceCompatibility JavaVersion.VERSION_17; targetCompatibility JavaVersion.VERSION_17 }` | Java 17로 컴파일 및 실행 타겟을 맞추기 위해 |
| `app/build.gradle` | `kotlinOptions { jvmTarget = '17' }` | Kotlin에서 생성된 코드가 Java 17 JVM을 타겟으로 하도록 설정 |
| Android Studio 설정 | JDK 경로: `Temurin 17` 또는 `corretto-17` 선택 | 빌드에 실제 사용될 JDK를 명시적으로 지정 |
| Project Structure > Language level | (가능한 경우) `17 - Sealed types ...` 선택 | Java 17 문법 사용 가능하도록 설정<br>※ Android Studio에서 preview 또는 unsupported로 표시될 수 있음 |

---

## 3. 추가 팁

- 만약 Language level이 `17 (Preview) - Unsupported`로 표시되더라도,  
  실제 JDK와 Gradle 설정이 올바르면 빌드는 정상적으로 작동함
- Android Studio 버전이 오래되었을 경우 **Hedgehog 이상**으로 업그레이드 권장
- Kotlin 버전은 최소 `1.6.10` 이상이어야 `jvmTarget = '17'`이 적용됨


### 3-1. 📌 Java Preview 기능과 Language Level 관련 설명

#### ✅ 1. Preview란?

- Java에서는 새로운 기능을 정식 릴리스 전에 먼저 **"Preview 기능"**으로 제공해.
- Preview 기능은 실험적이지만 실제 코드에서 사용해볼 수 있도록 열려 있음.
- 예를 들어 Java 17에서는 다음과 같은 기능이 preview 상태였어:
  - `sealed class`
  - `pattern matching for instanceof`
  - `switch` 문 개선
- IDE(Android Studio 등)에서는 이런 기능이 **정식 문법이 아니라는 이유로 "Unsupported"로 표시**될 수 있어.

> 즉, Android Studio에서 `Language level 17 (Preview) - Unsupported`라고 뜨는 건  
해당 기능이 **아직 IDE가 안정적으로 지원하지 않거나 preview로 간주해서** 그래.


#### ✅ 2. 그렇다면 Java 16으로 낮춰도 될까?

| 항목 | Java 17 (Preview) | Java 16 |
|------|-------------------|---------|
| IDE 지원 | ❌ Preview → Unsupported 표시 | ✅ 안정적으로 지원 |
| 문법 사용 | 일부 최신 문법만 가능 | 일반 Java/Kotlin 개발에 충분 |
| 실무 안정성 | 미지원 문법으로 인해 하이라이팅/컴파일 이슈 가능 | ✅ 높은 안정성 |

✅ **추천 전략:**
- Gradle과 JDK는 Java 17로 유지하되,  
  **Project Language Level은 16으로 설정하면 IDE 에러 없이 빌드와 실행 모두 안정적으로 가능**.
- Java 17의 특수 문법(예: sealed class 등)이 꼭 필요하지 않다면 이 전략이 가장 안전해.

> 요약:  
> Language Level은 IDE용 설정일 뿐이고,  
> JDK와 Gradle이 17이면 실제 빌드는 Java 17 기준으로 문제없이 수행돼.

---

## 4. 최종 확인 체크리스트 ✅

- [x] Gradle 버전이 7.6 이상인가?
- [x] Android Gradle Plugin 버전이 7.0 이상인가?
- [x] 실제 사용하는 JDK가 Java 17인가?
- [x] `compileOptions`, `kotlinOptions`에 Java 17 적용되어 있는가?
- [x] Language level도 17로 가능하면 설정했는가?

---

## 5. 최종 수정된 `app/build.gradle` 예시

```groovy
apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'

android {
    compileSdkVersion flutter.compileSdkVersion

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_17    // ✅ Java 17로 수정
        targetCompatibility JavaVersion.VERSION_17    // ✅ Java 17로 수정
    }

    kotlinOptions {
        jvmTarget = '17'    // ✅ Kotlin JVM 타겟을 17로 설정
    }

    defaultConfig {
        applicationId "com.example.tmpproj"
        minSdkVersion flutter.minSdkVersion
        targetSdkVersion flutter.targetSdkVersion
        versionCode flutterVersionCode.toInteger()
        versionName flutterVersionName
    }

    buildTypes {
        release {
            signingConfig signingConfigs.debug
        }
    }
}

flutter {
    source '../..'
}
```

---

## 6. 루트 Gradle 및 Gradle Wrapper 설정 예시

### ✅ 루트 `build.gradle`

```groovy
buildscript {
    ext.kotlin_version = '1.6.10'
    repositories {
        google()
        mavenCentral()
    }

    dependencies {
        // classpath 'com.android.tools.build:gradle:4.1.0'
        classpath 'com.android.tools.build:gradle:7.4.2'  // ✅ AGP 버전 7.4.2로 수정
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}

allprojects {
    repositories {
        google()
        mavenCentral()
    }
}

rootProject.buildDir = '../build'
subprojects {
    project.buildDir = "${rootProject.buildDir}/${project.name}"
}
subprojects {
    project.evaluationDependsOn(':app')
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

---

### ✅ `gradle-wrapper.properties`

```properties
#Fri Jun 23 08:50:38 CEST 2017
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
#distributionUrl=https\://services.gradle.org/distributions/gradle-6.7-all.zip
distributionUrl=https\://services.gradle.org/distributions/gradle-7.6-all.zip   # ✅ Gradle 7.6으로 수정
```

# [개념명] sapjco3.jar - SAP Java Connector 설명

---

## 1. 개요

| 항목    | 설명                                                                                              |
| ----- | ----------------------------------------------------------------------------------------------- |
| 파일명   | `sapjco3.jar`                                                                                   |
| 전체 이름 | SAP Java Connector 3 (JCo 3)                                                                    |
| 용도    | **Java 애플리케이션에서 SAP 시스템의 RFC(Function Module)를 호출**할 수 있도록 도와주는 SAP 공식 라이브러리                    |
| 필수 구성 | `sapjco3.jar` + 플랫폼에 맞는 **native 라이브러리** (`libsapjco3.so` for Linux, `sapjco3.dll` for Windows) |

---

## 2. 왜 필요한가?

SAP 시스템은 외부에서 기능을 호출할 때 **RFC (Remote Function Call)** 방식을 사용해.  
Java는 기본적으로 SAP와 직접 통신할 수 없기 때문에  
**SAP에서 제공하는 전용 라이브러리**가 필요하고, 그게 바로 `sapjco3.jar`야.

📌 이걸 통해 Java 코드에서 SAP의 `Z_POST_DOC`, `Z_GET_DOC` 같은 **Function Module을 호출**할 수 있어.

---

## 3. 구성 요소

| 구성 파일 | 설명 |
|-----------|------|
| `sapjco3.jar` | Java 라이브러리 (클래스 및 인터페이스 제공) |
| `libsapjco3.so` 또는 `sapjco3.dll` | 플랫폼별 네이티브 라이브러리 |
| `license` | SAP 계정으로 라이선스 약관에 동의해야 다운로드 가능 |

---

## 4. 사용하는 방법 (e-Doc 예시 기준)

```java
JCoDestination dest = JCoDestinationManager.getDestination("SAP_CONN");
JCoFunction func = dest.getRepository().getFunction("Z_POST_DOC");

func.getImportParameterList().setValue("DOC_NUM", "100001");
func.execute(dest);
```

🔧 위 코드는 e-Doc에서 SAP에 전표(Post)를 요청하는 대표적인 사용 예야.

---

## 5. 시스템 적용 예 (우리 프로젝트)

- `pom.xml`에는 **system scope**으로 설정되어 있어:

```xml
<dependency>
  <groupId>sapjco3</groupId>
  <artifactId>sapjco3</artifactId>
  <version>3</version>
  <scope>system</scope>
  <systemPath>${project.basedir}/src/main/webapp/WEB-INF/lib/sapjco3.jar</systemPath>
</dependency>
```

→ 즉, **JAR은 로컬에 직접 위치시키고**,  
**빌드에는 포함하지 않음** (라이선스 이슈 때문에)

> 📁 출처: :contentReference[oaicite:0]{index=0}

---

## 6. 설치/배포 시 주의사항

| 항목 | 설명 |
|------|------|
| 🔐 라이선스 | SAP 사이트(Support Portal)에서 로그인 후 직접 다운로드해야 함 (재배포 불가) |
| 🖥️ OS 종속성 | JAR 외에도 OS에 맞는 `.dll`(Windows) 또는 `.so`(Linux) 파일을 함께 설치해야 함 |
| 🔁 호환성 | Java 버전 / SAP 버전에 맞는 sapjco3 버전을 반드시 확인해야 함 |
| ⚠️ 오류 예시 | native lib 누락 시 `java.lang.UnsatisfiedLinkError` 발생 |

---

## 7. 결론

`sapjco3.jar`은  
🧩 **SAP와 Java를 연결해주는 핵심 브릿지 라이브러리**야.  
SAP와 통합된 전자결

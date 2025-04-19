# [작업목록] Windows 기반 Hugo + GitHub 
Pages + Obsidian 뷰어 구축


## ✅ 전체 작업 목록 정리 (작업 목적 + 개념 요약 포함)

| 단계 | 작업 내용 | 목적 요약 / 개념 설명 | 상태 |
|------|-----------|------------------------|------|
| 1 | Chocolatey 설치 | Windows용 패키지 관리자 설치 | ⬜ |
| 2 | Git 설치 | GitHub 연동 및 버전관리 도구 | ⬜ |
| 3 | Hugo 설치 | 정적 사이트 생성기 도구 설치 | ⬜ |
| 4 | 새 Hugo 사이트 생성 | Obsidian Vault를 담을 Hugo 프로젝트 생성 | ⬜ |
| 5 | Hugo 테마 설치 (Book) | 문서형 UI 제공 Hugo 테마 | ⬜ |
| 6 | `config.toml` 설정 | 사이트 정보, baseURL, 테마 설정 | ⬜ |
| 7 | Obsidian 콘텐츠 복사 | `.md` 파일들을 `content/`로 이동 | ⬜ |
| 8 | 내부 링크 점검 | `[[링크]]` 형식을 `[링크](./링크.md)`로 수정 | ⬜ |
| 9 | 로컬 서버 실행 | 사이트를 브라우저로 테스트 (`localhost`) | ⬜ |
| 10 | 사이트 빌드 (`hugo`) | HTML 정적 파일 생성 (`public/`) | ⬜ |
| 11 | `.nojekyll` 파일 생성 | GitHub Pages에서 `_` 폴더 인식되도록 설정 | ⬜ |
| 12 | GitHub Pages로 배포 | `public/` 폴더를 깃허브에 업로드 | ⬜ |
| 13 | 최종 접속 확인 | `https://herservs.github.io/obsidian/` 확인 | ⬜ |

---

# [개념명] Chocolatey 설치 - Windows 패키지 관리자

---

## ✅ 개요

**Chocolatey**는 Windows에서 사용하는 대표적인 패키지 관리자이다.  
명령어 한 줄로 다양한 소프트웨어를 설치, 업데이트, 제거할 수 있어 개발 환경 구축에 매우 유용하다.

---

## 🛠 설치 절차 (PowerShell 기반)

### 1. PowerShell 관리자 권한 실행

- 시작 메뉴에서 `PowerShell` 검색  
- **우클릭 → "관리자 권한으로 실행"** 선택

---

### 2. 아래 명령어 복사 & 붙여넣기

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; `
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; `
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

> ⏳ 설치 스크립트가 다운로드되어 자동으로 실행됨

---

### 3. 설치 완료 확인

```powershell
choco -v
```

> 버전이 출력되면 설치 성공 (예: `1.4.0`)

---

## 📌 참고 사항

- 설치 후 PowerShell 재시작 권장
- 이후 Chocolatey를 통해 다양한 툴 설치 가능:
  ```powershell
  choco install git -y
  choco install hugo -y
  choco install vscode -y
  ```

---

## ✅ 요약

| 항목 | 설명 |
|------|------|
| 용도 | Windows용 패키지 설치 자동화 도구 |
| 유사 도구 | macOS: `brew`, Linux: `apt`, `yum` |
| 설치 위치 | PowerShell (관리자 권한)에서 실행 |
| 대표 사용 명령 | `choco install 패키지명` |

---

# [개념명] Hugo 설치 - 정적 사이트 생성기

---

## ✅ 개요

**Hugo**는 Go 언어 기반의 정적 사이트 생성기로,  
Markdown 파일을 HTML 사이트로 빠르게 변환하는 데 사용된다.  
속도가 빠르고 테마가 풍부하여 기술 문서, 블로그, 포트폴리오에 널리 사용된다.

---

## 🛠 설치 절차 (Windows + Chocolatey 사용)

### 1. PowerShell 관리자 권한 실행

- 시작 메뉴에서 `PowerShell` → "관리자 권한으로 실행"

---

### 2. Chocolatey로 Hugo 설치

```powershell
choco install hugo -y
```

> `-y` 옵션은 설치 중 사용자 확인 없이 자동 진행됨

---

### 3. 설치 완료 확인

```powershell
hugo version
```

#### ✅ 예시 출력
```bash
hugo v0.146.5-61328976e11cbb9d906b4a790b8a7cd25b2a914f windows/amd64
BuildDate=2025-04-15T17:54:38Z
VendorInfo=gohugoio
```

---

## 📌 참고 사항

- `hugo`는 CLI 기반 도구로, 명령어 하나로 정적 웹사이트 빌드 가능
- `hugo server` 명령으로 로컬 테스트 가능
- 기본적으로 `content/`, `layouts/`, `themes/`, `public/` 등의 폴더 구조 사용

---

## ✅ 설치 요약

| 항목 | 상태 |
|------|------|
| Hugo 설치 경로 | Windows (PowerShell + Chocolatey) |
| 설치 방법 | `choco install hugo -y` |
| 설치 확인 | `hugo version` 출력 완료 |
| 설치 버전 | `v0.146.5` (2025년 4월 기준 최신 버전) |

---

# [개념명] Hugo 새 사이트 생성 - Obsidian 콘텐츠를 위한 정적 웹사이트 구조 만들기

---

## ✅ 개요

`hugo new site` 명령을 사용하면  
Hugo가 정적 사이트 프로젝트에 필요한 기본 폴더와 파일 구조를 자동으로 만들어준다.

이 단계는 **Obsidian Vault를 웹사이트로 바꾸기 위한 기본 틀을 준비하는 작업**이다.

---

## 🛠 새 Hugo 사이트 생성 절차

### 1. 원하는 디렉토리 위치로 이동

```powershell
cd C:\Users\사용자명\Documents  # 또는 원하는 경로
```

> ⚠️ `공백 없는 경로` 또는 `한글 없는 경로` 권장

---

### 2. Hugo 사이트 생성

```powershell
hugo new site obsidian-hugo
```

> `obsidian-hugo`는 프로젝트 폴더 이름이며, 자유롭게 변경 가능

---

### 3. 생성된 기본 폴더 구조

```bash
obsidian-hugo/
├── archetypes/      # 콘텐츠 템플릿 정의
├── content/         # Markdown 콘텐츠 위치 (Obsidian md 파일 넣는 곳)
├── layouts/         # 사용자 정의 템플릿
├── static/          # 이미지, JS, CSS 등 정적 리소스
├── themes/          # 테마 저장소
├── config.toml      # 사이트 전역 설정 파일
└── public/          # hugo 빌드 결과물 (HTML 정적 파일)
```

---

## ✅ 생성 후 해야 할 일 (다음 단계)

| 항목 | 설명 |
|------|------|
| 테마 설치 | 원하는 UI 테마를 `themes/` 폴더에 추가 |
| config.toml 설정 | 사이트 URL, 테마명 등 설정 |
| 콘텐츠 복사 | Obsidian Vault의 `.md` 파일을 `content/`로 복사 |
| 테스트 실행 | `hugo server` 명령으로 로컬 확인 가능 |

---

## 💬 명령어 요약

```powershell
# 1. 디렉토리 이동
cd C:\Workspace

# 2. 사이트 생성
hugo new site obsidian-hugo
```

---

## ✅ 확인 체크

| 항목 | 상태 |
|------|------|
| 프로젝트 생성 | ⬜ `obsidian-hugo/` 폴더 생성되었는가? |
| 내부 폴더 구조 확인 | ⬜ `archetypes/`, `content/`, `themes/` 등 생성되었는가? |


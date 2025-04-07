# [개념명] Git 기본 초기 설정 - 설치 후 필수 환경 구성

## 1. 개요
Git 설치 후 모든 프로젝트에 공통으로 적용되는 사용자 정보, 브랜치 명칭, 편집기, 인증, 줄바꿈 등의 글로벌 설정 목록

## 2. 필수 설정 항목

### 🔹 사용자 정보 등록
```bash
git config --global user.name "너의 이름"
git config --global user.email "너의 이메일 주소"
```

### 🔹 기본 브랜치 명칭(main으로)
```bash
git config --global init.defaultBranch main
```

### 🔹 컬러 출력 활성화
```bash
git config --global color.ui auto
```

### 🔹 줄바꿈 처리 방식 설정
- **Windows**
```bash
git config --global core.autocrlf true
```
- **macOS / Linux**
```bash
git config --global core.autocrlf input
```

### 🔹 기본 편집기 설정
```bash
git config --global core.editor "code --wait"    # VSCode
git config --global core.editor "vim"            # Vim
```

### 🔹 인증 정보 저장 (토큰/비밀번호 캐시)
```bash
git config --global credential.helper cache
git config --global credential.helper 'cache --timeout=3600'
```

### 🔹 diff 도구 설정 (선택)
```bash
git config --global diff.tool vscode
git config --global difftool.vscode.cmd "code --wait --diff $LOCAL $REMOTE"
```

## 3. 설정 확인
```bash
git config --list
```

## 4. 보너스: .gitconfig 직접 수정
```bash
code ~/.gitconfig   # VSCode
notepad ~/.gitconfig   # Windows 메모장
```

## 5. 참고 사이트
- [https://gitignore.io](https://gitignore.io) – `.gitignore` 템플릿 생성기

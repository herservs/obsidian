# [개념명] Git 커밋 메시지 태그 정리 - Conventional Commits

## 1. 개요
Git 커밋 메시지를 일관성 있게 작성하기 위한 표준 태그 규칙.  
주로 CI/CD, 체인지로그 자동 생성 등에 활용됨.

## 2. 대표 태그 목록

| 태그       | 의미 설명                                      | 사용 예시                                  |
|------------|-----------------------------------------------|---------------------------------------------|
| `feat`     | 새로운 기능 추가                                | 로그인 기능 추가, API 신규 구현 등           |
| `fix`      | 버그 수정                                       | 로그인 실패 문제 해결, NullPointer 해결 등   |
| `docs`     | 문서 변경 (README, 주석 등)                     | README 수정, 주석 보완 등                    |
| `style`    | 코드 스타일 수정 (포맷팅, 세미콜론 등)           | 코드 정렬, 공백 정리, 들여쓰기 수정 등       |
| `refactor` | 코드 리팩토링 (기능 변화 없이 구조 개선)         | 중복 제거, 함수 분리, 변수명 개선 등         |
| `perf`     | 성능 개선                                       | 쿼리 최적화, 캐싱 추가 등                    |
| `test`     | 테스트 코드 추가 또는 수정                      | 유닛 테스트 추가, 테스트 구조 변경 등        |
| `chore`    | 빌드, 설정, 기타 잡일 등                         | Gradle 설정 변경, Lint 설정 추가 등          |
| `ci`       | CI/CD 설정 변경                                 | GitHub Actions 설정, Jenkins 스크립트 변경 등 |
| `build`    | 빌드 시스템 또는 의존성 수정                     | dependency 추가, 빌드 스크립트 수정 등       |
| `revert`   | 이전 커밋 되돌림                                 | 특정 기능 롤백 등                            |

## 3. 커밋 메시지 기본 형식

```
<type>: <간단한 설명>
```

예:
```
feat: Add user profile page
fix: Correct typo in login message
chore: Update ESLint config for dev environment
```

## 4. 참고
- Conventional Commits 공식 문서: [conventionalcommits.org](https://www.conventionalcommits.org/)
- 커밋 메시지에 태그를 붙이면 자동 릴리즈 툴이나 changelog 생성 도구에서 분류하기 쉬움

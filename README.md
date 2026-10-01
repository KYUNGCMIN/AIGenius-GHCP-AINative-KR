# AI Genius Episode 1: 실습 워크숍 저장소

> **한국어 번역본 안내:** 이 저장소는 Michelle Sandford의 [원본 워크숍 저장소](https://github.com/codess-aus/AIGenius-GHCP-AINative)를 한국어로 옮긴 비공식 번역본입니다. Microsoft의 공식 번역이 아니며, 원저작권 고지와 [MIT 라이선스](./LICENSE)를 그대로 유지합니다.

![AI Genius Episode 1 워크숍 표지](./assets/AI-Genius-Ep1.png)

## "AI와 함께 코딩하기: GitHub Copilot을 활용한 AI 네이티브 코딩 워크플로"

환영합니다! 이 저장소는 **AI Genius Episode 1**의 실습 워크숍을 위한 공간입니다. 이슈 작성, Copilot에 작업 위임, 생성된 코드 검토, 풀 리퀘스트(PR) 댓글을 통한 개선까지 AI 네이티브 개발의 전체 과정을 경험합니다.

---

## 학습 내용

- 개발자에게 "AI 네이티브"가 실제로 어떤 의미인지 이해하기
- Copilot에 필요한 맥락을 제공하는 이슈 작성하기
- Copilot에 작업을 할당하고 작업 과정 살펴보기
- 시니어 개발자의 관점으로 Copilot이 생성한 PR 검토하기
- 처음부터 다시 시작하는 대신 PR 댓글로 결과 개선하기
- 코딩 과정 전반에서 AI와 효과적으로 협업하는 방법 익히기

---

## AI 네이티브 워크플로의 반복 과정

```
아이디어
  └─► GitHub 이슈 작성  (할 일 설명)
        └─► Copilot에 할당  (Copilot 에이전트가 작업 시작)
              └─► 안전한 샌드박스에서 코드 생성
                    └─► 초안 PR 생성  (세션 로그 포함)
                          └─► 사람이 검토하고 PR 댓글로 개선 요청
                                └─► 병합 및 배포
```

이 워크플로에서 여러분은 **테크 리드**입니다. Copilot은 *어떻게 구현할지*를 담당하고, 여러분은 *무엇을 왜 만들지*를 정의합니다.

---

## 환경 설정 안내

### 사전 준비 사항

- GitHub Copilot을 사용할 수 있는 GitHub 계정
- 데스크톱용 [GitHub Copilot App](https://github.com/features/copilot) 설치
- 로컬에 Python 3.10 이상 설치(시작 앱 실행용)
- Git 설치

### 시작하기

1. **[이 한국어 저장소](https://github.com/KYUNGCMIN/AIGenius-GHCP-AINative-KR)를 자신의 GitHub 계정으로 포크**합니다. 페이지 오른쪽 위의 **Fork** 버튼을 사용하세요.

2. **자신의 포크를 로컬에 복제**합니다. `YOUR-USERNAME`은 자신의 GitHub 사용자 이름으로 바꾸세요.
   ```bash
   git clone https://github.com/YOUR-USERNAME/AIGenius-GHCP-AINative-KR.git
   cd AIGenius-GHCP-AINative-KR
   ```

3. **시작 앱을 실행**합니다.
   ```bash
   cd starter-app
   pip install -r requirements.txt
   python app.py add "Deploy the API" --priority high --due 2025-12-31 --tag work
   python app.py add "Buy coffee" --priority low --tag personal
   python app.py list
   python app.py stats
   ```

4. **GitHub Copilot App을 열고** 자신의 포크 저장소에 연결합니다.

5. [`exercises/01-write-an-issue`](./exercises/01-write-an-issue/README.md)부터 실습을 순서대로 진행합니다.

---

## 저장소 구조

```
📁 AIGenius-GHCP-AINative-KR/
  ├── README.md                        # 에피소드 소개 및 환경 설정 안내
  ├── .github/
  │   ├── copilot-instructions.md      # Copilot에 제공할 맥락: 규칙, Azure 패턴, 비밀 정보 관리
  │   └── ISSUE_TEMPLATE/
  │       └── feature-request.md       # AI 네이티브 워크플로용 이슈 템플릿
  ├── exercises/
  │   ├── 01-write-an-issue/           # 실습: 명확한 이슈 작성(클라우드/AI 선택 과제)
  │   ├── 02-assign-to-copilot/        # 실습: 작업 할당 및 관찰
  │   ├── 02a-fleet-mode/              # 선택 실습: /fleet으로 하위 작업 병렬 실행
  │   ├── 02b-squad-framework/         # 선택 실습: /squad로 지속적인 에이전트 팀 구성
  │   ├── 03-review-a-pr/              # 실습: PR 검토 및 댓글 작성
  │   ├── 04-iterate/                  # 실습: PR 댓글을 통한 반복 개선
  │   └── 05-azure-and-ai/             # 심화 실습: Azure + OpenAI 기능용 이슈 예시
  └── starter-app/                     # 실습에서 확장할 Python CLI 작업 관리자
      ├── app.py                       # CLI: add, list, complete, edit, delete, stats
      ├── requirements.txt             # click, rich, pytest
      └── tests/
          ├── conftest.py              # 공용 픽스처(격리된 작업 파일 사용)
          └── test_tasks.py            # 모든 명령과 경계 사례를 다루는 테스트 41개
```

---

## AI 네이티브 코딩의 5가지 핵심 원칙

1. **이슈의 품질을 높이세요.** 이슈가 곧 프롬프트입니다. 구체적으로 작성하세요.
2. **시니어 개발자처럼 검토하세요.** AI는 빠르게 생성하고, 사람은 면밀하게 검증합니다.
3. **`copilot-instructions.md`를 활용하세요.** Copilot이 지속적으로 참고할 프로젝트 맥락을 제공하세요.
4. **다시 생성하기보다 개선하세요.** 처음부터 시작하는 대신 댓글로 방향을 제시하세요.
5. **과정에 계속 참여하세요.** 세션 로그를 확인하고 Copilot이 무엇을 왜 했는지 이해하세요.

---

## 발표자

**Michelle Sandford** — 호주·뉴질랜드 지역 개발자 참여 프로그램 리드(Developer Engagement Lead)

Michelle은 Microsoft에서 개발자 참여 프로그램을 이끌고 있습니다. 직접 코드를 작성하고 GitHub와 Azure AI로 서비스를 만들며, 배운 내용을 공개적으로 나눕니다.

---

## 한국어 워크숍 문서 사이트(MkDocs)

이 저장소에는 참가자용 MkDocs Material 문서 사이트도 포함되어 있습니다. 이 README와 7개 실습 README뿐 아니라 [`docs/`의 모든 문서](./docs/index.md)와 사이트 내비게이션도 **한국어로 제공**합니다.

- 로컬에서 실행:
  ```bash
  pip install -r docs-requirements.txt
  mkdocs serve
  ```
- 로컬에서 빌드:
  ```bash
  mkdocs build --strict
  ```
- 배포 구성:
  - 원본의 GitHub Actions 워크플로 `.github/workflows/docs.yml`은 `attendee-mkdocs-site` 및 `main` 브랜치에 푸시하면 사이트를 빌드하고 GitHub Pages에 배포하도록 정의되어 있습니다. 실제 배포에는 해당 워크플로와 GitHub Pages가 활성화되어 있어야 합니다.
  - 이 한국어 번역 작업은 GitHub 저장소의 README와 MkDocs 문서 소스 공개를 대상으로 하며, GitHub Pages 배포나 워크플로 활성화는 포함하지 않습니다.

# 실습 02B -- `/squad`로 지속적인 팀 구성하기

> ⚠️ **토큰 비용 주의:** Squad는 `/fleet`과 마찬가지로 이름이 있는 여러 에이전트를 실행할 수 있으므로 실습 02의 단일 에이전트 작업 흐름보다 AI 사용량이 많습니다. 추가 사용량을 감수할 만한 실제 프로젝트가 없다면, 이 실습을 읽기만 하고 직접 실행은 건너뛰어도 좋습니다.

## 목표

프로젝트에 오픈 소스 [Squad](https://github.com/bradygaster/squad) 프레임워크를 설치하고, 이름이 있는 에이전트들로 지속적인 팀을 구성합니다. 프런트엔드, 백엔드, 테스터, 리드 등의 역할을 맡은 에이전트들은 이슈와 세션이 바뀌어도 유지됩니다. 하나의 목표를 처리한 뒤 사라지는 Fleet의 일회성 하위 에이전트와 다른 점입니다.

## 사전 준비 사항

- 실습 02A를 완료했거나 최소한 읽어 보고 `/fleet`과 `/squad`의 차이를 이해한 상태
- Node.js 22.5.0 이상 및 npm
- Git 설치 및 설정 완료
- Squad의 이슈/PR 기능에 필요한 [GitHub CLI(`gh`)](https://cli.github.com/) 설치
- Copilot CLI 설치(실습 02A의 환경 설정 1단계 참고)
- Squad는 **알파 버전 소프트웨어**입니다. 릴리스에 따라 CLI 명령이 달라질 수 있습니다.

## 환경 설정

### 1단계 -- 도구 설치 상태 확인하기

```bash
node --version
npm --version
git --version
gh --version
```

**✓ 확인:** 네 명령 모두 버전을 출력해야 합니다. Node.js는 22.5.0 이상이어야 합니다.

### 2단계 -- 프로젝트 만들기 또는 선택하기

임시 프로젝트에서 먼저 시도하거나, 로컬에 복제한 `starter-app`에서 바로 진행할 수 있습니다.

```bash
mkdir my-squad-demo && cd my-squad-demo
git init
```

**✓ 확인:** 새 프로젝트에서 `git status`를 실행하면 "No commits yet"(아직 커밋 없음)이 표시되어야 합니다.

### 3단계 -- Squad CLI 설치하기

```bash
npm install -g @bradygaster/squad-cli
squad init
```

Squad가 설정 과정을 단계별로 안내합니다. 미리 구성된 팀으로 시작하려면 다음 명령을 사용하세요.

```bash
squad init --preset default
```

이 명령은 팀원, 역할 정의(charter), 작업 라우팅 규칙이 모두 설정된 Squad 팀의 기본 구조를 즉시 생성합니다.

**✓ 확인:** 프로젝트에 `.squad/team.md`가 생성되었는지 확인합니다.

### 4단계 -- GitHub 인증하기

```bash
gh auth login
```

**✓ 확인:** `gh auth status`를 실행하면 "Logged in to github.com"(github.com에 로그인됨)이 표시되어야 합니다. 이 인증을 통해 Squad가 여러분을 대신해 이슈와 PR을 생성할 수 있습니다.

## 실습

### 1단계 -- Squad 에이전트로 Copilot 열기

```bash
copilot --agent squad --yolo
```

> `--yolo` 플래그는 도구를 호출할 때마다 표시되는 승인 요청을 건너뜁니다. Squad는 일반적인 세션에서도 도구를 여러 번 호출하므로, 이 플래그가 없으면 승인을 반복해야 합니다.

VS Code에서는 대신 Copilot Chat을 열고 에이전트 선택 메뉴에서 **Squad**를 선택할 수 있습니다.

### 2단계 -- 만들려는 프로젝트 설명하기

채팅에서 Squad에 프로젝트를 설명합니다.

```
새 프로젝트를 시작하려고 합니다. 팀을 구성해 주세요.
만들려는 것은 Azure OpenAI 태그 추천 기능을 갖춘 Python CLI 작업 관리자입니다.
```

### 3단계 -- 제안된 팀 구성 확인하기

Squad가 작업에 적합한 전문 에이전트 팀을 제안합니다. 각 에이전트에는 이름과 역할이 있으며, 예를 들어 백엔드 에이전트, 테스터 에이전트, 리드 에이전트가 포함될 수 있습니다. 제안을 검토한 뒤 `yes`라고 답해 확정합니다.

**✓ 확인:** Squad가 팀의 작업 준비가 완료되었다고 알리고, `.squad/` 아래에 팀원 정보가 반영되어 있는지 확인합니다.

### 4단계 -- 팀에 작업 위임하기

실습 02에서 Copilot에 이슈를 할당했던 것처럼 Squad에 실제 작업을 맡깁니다. 이번에는 해당 작업에 가장 적합한 전문 에이전트에게 전달되도록 요청합니다.

```
백엔드 전문 에이전트가 list 명령에 --tag 필터를 추가하고,
테스터가 해당 기능의 테스트를 작성하도록 해 주세요.
```

### 5단계 -- 팀과 맥락이 유지되는지 확인하기

세션을 닫았다가 나중에 다시 엽니다. `copilot --agent squad --yolo`를 다시 실행하거나 VS Code에서 Squad를 다시 선택하면 됩니다. 매번 새로 시작하는 Fleet 실행과 달리, 팀 구성과 맥락, 이전 결정이 `.squad/` 아래에 그대로 남아 있는지 살펴보세요.

## 나중에 Squad 업그레이드하기

```bash
npm install -g @bradygaster/squad-cli@latest
squad upgrade
```

`squad upgrade`는 Squad가 관리하는 파일과 워크플로를 갱신하지만, `.squad/`의 팀 상태는 변경하지 않습니다. 따라서 에이전트, 결정 사항, 이력이 보존됩니다.

## 돌아보기

- 이름이 있는 전문 에이전트에게 위임하는 방식은 실습 02에서 이슈 전체를 하나의 Copilot 에이전트에게 맡기는 방식과 어떻게 달랐나요?
- 새로 실행한 `/fleet`은 기억하지 못하지만 Squad는 세션 간에 유지한 정보가 무엇이었나요?
- 어떤 경우에 `/fleet` 대신 `/squad`를 선택하겠나요? 반대로 하나의 Copilot 에이전트(실습 02)만으로 충분한 경우는 언제일까요?

## 다음 단계

[2장 - Copilot에 할당하기(원본 영문 문서)](../../docs/chapter-2-assign-to-copilot.md)로 돌아가거나, [실습 03 - 초안 PR 검토하기](../03-review-a-pr/README.md)로 이동하세요.

# 실습 02A -- `/fleet`으로 병렬 실행하기

> ⚠️ **토큰 비용 주의:** `/fleet`은 하나가 아닌 여러 하위 에이전트를 실행합니다. 개인용/유료 요금제를 사용 중이고 추가 사용량을 감수할 만한 실제 백로그가 없다면, 이 실습을 읽기만 하고 직접 실행은 건너뛰어도 좋습니다.

## 목표

실습 02의 단일 이슈 작업 흐름을 하나씩 실행하는 대신, Copilot CLI의 `/fleet` 슬래시 명령으로 하나의 목표를 독립적인 하위 작업으로 나누고 여러 하위 에이전트가 병렬로 처리하게 합니다.

## 사전 준비 사항

- 실습 02 완료(이슈 할당 및 샌드박스에서의 작업 과정 관찰)
- Node.js 22 이상 설치(Copilot CLI 필수 요구사항)
- 로컬에 복제한 `starter-app`에 접근할 수 있는 터미널
- [GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli)를 사용할 수 있는 요금제

## 환경 설정

### 1단계 -- Copilot CLI 설치하기

운영 체제에 맞는 방법을 선택하세요.

```bash
# npm (모든 운영 체제, Node.js 22 이상 필요)
npm install -g @github/copilot

# Homebrew (macOS/Linux)
brew install --cask copilot-cli

# WinGet (Windows)
winget install GitHub.Copilot

# 설치 스크립트 (macOS/Linux)
curl -fsSL https://gh.io/copilot-install | bash
```

**✓ 확인:** `copilot --version`을 실행해 버전 번호가 출력되는지 확인합니다.

### 2단계 -- 인증하기

```bash
copilot
```

처음 실행할 때 표시되는 대화형 안내에 따라 자신의 GitHub 계정으로 로그인합니다.

**✓ 확인:** `copilot`을 다시 실행하면 로그인 요청 없이 바로 세션이 시작되어야 합니다.

## 실습

### 1단계 -- 저장소에서 세션 열기

`starter-app`이 포함된 로컬 복제 저장소의 루트에서 실행합니다.

```bash
copilot
```

### 2단계 -- 독립적인 하위 작업으로 나눌 수 있는 목표를 `/fleet`에 전달하기

대화형 세션에서 다음을 입력합니다.

```
/fleet 실습 05의 남은 작업을 서로 독립적인 하위 작업
(Azure OpenAI 클라이언트 설정, 태그 추천 함수, 테스트)으로 나누고
병렬로 진행해 주세요
```

또는 셸에서 비대화형으로 실행할 수 있습니다. 대화형 모드가 아닐 때는 `--no-ask-user` 플래그가 필요합니다. 아래 명령은 위와 같은 요청을 영어로 전달합니다.

```bash
copilot -p "/fleet Break the remaining Exercise 05 work into independent sub-tasks (Azure OpenAI client setup, tag suggestion function, tests) and work them in parallel" --no-ask-user
```

### 3단계 -- 오케스트레이터의 작업 살펴보기

Copilot의 메인 에이전트는 다음과 같이 동작합니다.

1. 목표를 분석하고 독립적인 하위 작업으로 나눌 수 있는지 판단합니다.
2. 오케스트레이터로서 병렬 실행이 가능한 하위 에이전트에 작업을 배정합니다.
3. 각 하위 에이전트는 동일한 파일 시스템을 공유하되, 각자의 컨텍스트 창에서 작업합니다.
4. 오케스트레이터가 결과를 모아 하나의 통합된 결과물로 정리합니다.

### 4단계 -- 통합된 결과 검토하기

`/fleet`이 끝나면 단일 에이전트의 PR을 검토할 때와 같은 방식으로 결과를 검토합니다. 변경 내역과 테스트 실행 여부를 확인하고, 순차적으로 처리해야 할 작업이 잘못 병렬화되지는 않았는지 살펴보세요. 예를 들어 다른 하위 작업의 결과에 의존하는 작업은 실행 순서를 지켜야 합니다.

## 좋은 `/fleet` 프롬프트 작성 요령

- 결과물을 명확하게 지정하세요. 예를 들어 각 에이전트가 담당할 파일이나 모듈을 정확히 나열합니다.
- 작업을 어떻게 나눌지 드러나도록 프롬프트를 구성하세요. 예: "`docs/authentication.md`, `docs/endpoints.md`, `docs/errors.md`를 각각 작성해 주세요."
- 모호한 프롬프트는 병렬이 아닌 순차 방식으로 처리될 수 있습니다. `/fleet`은 서로 독립적임을 확인할 수 있는 작업만 병렬화합니다.
- 순차 작업(2단계에 1단계의 실제 결과가 필요한 경우)이나 여러 에이전트가 같은 파일을 수정해야 하는 밀접하게 연결된 작업에는 `/fleet`을 피하세요.

## 돌아보기

- Copilot이 실제로 목표를 병렬 하위 작업으로 나눴나요, 아니면 순차적으로 실행했나요? 그 이유는 무엇인가요?
- `/fleet`의 결과를 검토하는 과정은 실습 02의 단일 에이전트 PR 검토와 어떻게 달랐나요?
- 자신의 백로그에서 어떤 작업이 `/fleet`에 적합하고, 어떤 작업은 적합하지 않을까요?

## 다음 단계

[실습 02B - `/squad`로 지속적인 팀 구성하기](../02b-squad-framework/README.md)로 이동하세요.

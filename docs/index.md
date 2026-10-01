# AI Genius Episode 1 - 참가자 안내서

> **한국어 번역본 안내:** 이 문서는 Michelle Sandford의 [원본 워크숍 저장소](https://github.com/codess-aus/AIGenius-GHCP-AINative)를 한국어로 옮긴 비공식 번역본이며, Microsoft의 공식 번역이 아닙니다. 원저작권 고지(Copyright (c) 2026 Michelle Mei-Ling Sandford)와 [MIT 라이선스](https://github.com/KYUNGCMIN/AIGenius-GHCP-AINative-KR/blob/main/LICENSE)를 그대로 유지합니다.

![행사 제목과 브랜드가 표시된 AI Genius Episode 1 워크숍 대표 이미지](assets/ai-genius-ep1.png){ .home-hero }

이 안내서는 참가자가 워크숍 전체를 진행할 수 있도록 구성된 자료입니다. 사전 준비 사항, 환경 설정, 모든 실습의 단계별 안내까지 필요한 내용을 이 사이트에서 확인할 수 있습니다. 저장소는 시작 코드를 복제할 때 이용하면 됩니다.

## 학습 내용

- 개발자에게 "AI 네이티브"가 실제로 어떤 의미인지 이해하기
- Copilot에 필요한 맥락을 제공하는 이슈 작성하기
- Copilot에 작업을 할당하고 작업 과정 살펴보기
- 시니어 개발자의 관점으로 Copilot이 생성한 PR 검토하기
- 처음부터 다시 시작하는 대신 PR 댓글로 결과 개선하기
- 코딩 과정 전반에서 AI와 효과적으로 협업하는 방법 익히기

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

## 사전 준비 사항

1장을 시작하기 전에 다음 사항을 준비하세요.

- GitHub Copilot을 사용할 수 있는 GitHub 계정
- 데스크톱용 [GitHub Copilot App](https://github.com/features/copilot) 설치
- 로컬에 Python 3.10 이상 설치
- 로컬에 Git 설치

## 환경 설정: 시작 앱 실행하기

1. **저장소를 자신의 GitHub 계정으로 포크**합니다. [KYUNGCMIN/AIGenius-GHCP-AINative-KR](https://github.com/KYUNGCMIN/AIGenius-GHCP-AINative-KR)로 이동해 오른쪽 위의 **Fork** 버튼을 클릭하세요.

2. **자신의 포크를 로컬에 복제**합니다. `YOUR-USERNAME`은 자신의 GitHub 사용자 이름으로 바꾸세요.
   ```bash
   git clone https://github.com/YOUR-USERNAME/AIGenius-GHCP-AINative-KR.git
   cd AIGenius-GHCP-AINative-KR
   ```

3. **의존성을 설치하고 시작 앱을 실행**해 정상 동작하는지 확인합니다.
   ```bash
   cd starter-app
   pip install -r requirements.txt
   python app.py add "Deploy the API" --priority high --due 2025-12-31 --tag work
   python app.py add "Buy coffee" --priority low --tag personal
   python app.py list
   python app.py stats
   ```
   작업 두 개가 정리된 표와 통계 요약이 표시되어야 합니다. 정상적으로 표시되면 준비가 완료된 것입니다.

4. 데스크톱에서 **GitHub Copilot App을 열고** 자신의 포크 저장소에 연결합니다.

5. 아래 **1장**부터 순서대로 진행합니다. 각 장에 수행할 작업이 구체적으로 안내되어 있으므로, 저장소의 README나 `exercises/` 폴더로 돌아갈 필요가 없습니다.

## AI 네이티브 코딩의 5가지 핵심 원칙

1. **이슈의 품질을 높이세요.** 이슈가 곧 프롬프트입니다. 구체적으로 작성하세요.
2. **시니어 개발자처럼 검토하세요.** AI는 빠르게 생성하고, 사람은 면밀하게 검증합니다.
3. **`copilot-instructions.md`를 활용하세요.** Copilot이 지속적으로 참고할 프로젝트 맥락을 제공하세요.
4. **다시 생성하기보다 개선하세요.** 처음부터 시작하는 대신 댓글로 방향을 제시하세요.
5. **과정에 계속 참여하세요.** 세션 로그를 확인하고 Copilot이 무엇을 왜 했는지 이해하세요.

<div class="home-chapters" markdown>
<div class="grid cards chapter-grid" markdown>

-   ![1장 미리 보기: 체계적인 프롬프트 카드로 표현한 이슈 작성 과정](assets/1-issue.png){ .chapter-thumb }

    <span class="chapter-eyebrow">1장</span>
    ### 명확하고 체계적인 이슈 작성하기

    좋은 이슈의 구성 요소를 익히고 완성된 예시를 살펴본 뒤, 네 가지 기능 중 하나를 골라 직접 이슈를 작성합니다.

    [1장 읽기 →](chapter-1-write-an-issue.md)

-   ![2장 미리 보기: 안전한 샌드박스에서 작업할 Copilot에 이슈 할당](assets/2-assign.png){ .chapter-thumb }

    <span class="chapter-eyebrow">2장</span>
    ### Copilot에 이슈 할당하기

    이슈를 할당하고 Copilot App을 연 뒤, 에이전트가 저장소 복제, 탐색, 구현, 초안 PR 생성을 진행하는 모습을 실시간으로 관찰합니다.

    [2장 읽기 →](chapter-2-assign-to-copilot.md)

-   ![3장 미리 보기: 체크리스트를 활용한 풀 리퀘스트 검토](assets/3-review.png){ .chapter-thumb }

    <span class="chapter-eyebrow">3장</span>
    ### 초안 PR 검토하기

    실용적인 검토 기준으로 정확성, 품질, 보안, 테스트 범위를 확인한 뒤 실제 검토 댓글을 남깁니다.

    [3장 읽기 →](chapter-3-review-a-pr.md)

-   ![4장 미리 보기: 사람 검토자와 AI 에이전트 사이의 피드백 반복 과정](assets/4-iterate.png){ .chapter-thumb }

    <span class="chapter-eyebrow">4장</span>
    ### PR 댓글로 반복 개선하기

    Copilot이 피드백을 반영하는 과정을 살펴보고 변경 사항을 다시 검토한 뒤, 직접 PR을 병합해 전체 과정을 마무리합니다.

    [4장 읽기 →](chapter-4-iterate.md)

-   ![5장 미리 보기: Azure 및 AI 확장을 위한 아키텍처 다이어그램](assets/5-azure.png){ .chapter-thumb }

    <span class="chapter-eyebrow">5장</span>
    ### Azure + AI 확장

    Azure Table Storage를 이용한 데이터 영구 저장이나 Azure OpenAI 기반 지능형 태그 지정 등 더 난도가 높은 클라우드 과제에 도전합니다.

    [5장 읽기 →](chapter-5-azure-and-ai.md)

-   ![6장 미리 보기: 추천 워크숍 자료와 다음 학습 단계를 지원하는 Copilot 에이전트 팀](assets/fleet.png){ .chapter-thumb }

    <span class="chapter-eyebrow">6장</span>
    ### 참고 자료와 다음 단계

    Copilot 에이전트, Fleet 워크플로, 커뮤니티 프로젝트, 자격증에 관한 추천 링크로 학습을 이어 갑니다.

    [6장 읽기 →](resources.md)

</div>
</div>

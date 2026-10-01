# 실습 05 -- Azure + AI: 클라우드 네이티브 확장

## 목표

Copilot이 실제 클라우드 SDK 연동과 AI 기능 개발을 어떻게 처리하는지 살펴보고, 이런 작업을 위임할 때 얻는 이점과 수반되는 위험을 이해합니다.

## 이전 실습과 다른 점

이전 실습에서는 Copilot이 로컬 Python 앱을 확장했습니다. 이제 다음 작업을 Copilot에 요청하는 이슈를 작성합니다.

- 클라우드 저장소를 위한 **Azure SDK**(`azure-data-tables`) 사용
- **Azure OpenAI**를 호출해 앱 실행 중 AI 기능 제공
- 환경 변수를 이용해 비밀 정보를 안전하게 처리
- 클라우드 호출을 모의 처리(mock)하는 테스트 작성

작업의 복잡도가 상당히 높아집니다. 그만큼 이슈의 품질과 검토 역량이 가장 중요해지는 단계이기도 합니다.

---

## 선택 1: 저장소를 Azure Table Storage로 이전하기

### 아키텍처

```
CLI (app.py)
    └─► storage.py  (새로운 추상화 계층)
            ├─► LocalStorage (기존 JSON 파일 — 기본값)
            └─► AzureTableStorage (새 구현 — 환경 변수로 활성화)
```

`AZURE_STORAGE_CONNECTION_STRING`이 설정되어 있으면 앱은 Azure Table Storage를 사용합니다. 그렇지 않으면 기존 로컬 JSON 파일을 사용합니다. **기존 동작과의 호환성을 그대로 유지합니다.**

### 바로 사용할 수 있는 이슈 예시

아래 내용을 실습 01의 이슈(선택 A)로 사용하거나, 이 내용으로 새 이슈를 작성하세요.

---

**제목:** 작업 저장소를 Azure Table Storage로 이전

**문제 정의:**
현재 작업은 로컬 JSON 파일(`tasks.json`)에 저장됩니다. 따라서 컴퓨터를 바꾸면 기존 데이터를 사용할 수 없고 여러 기기에서 공유할 수도 없습니다. 클라우드 기반 저장소를 선택할 수 있어야 합니다.

**기대 동작:**
- `AZURE_STORAGE_CONNECTION_STRING` 환경 변수가 설정되어 있으면 Azure Table Storage의 `tasks` 테이블에 작업을 저장하고 해당 테이블에서 불러옵니다.
- 환경 변수가 설정되어 있지 않으면 기존과 동일하게 로컬 JSON 파일을 사용합니다.
- 어떤 저장소 백엔드를 사용하더라도 기존 CLI 명령(`add`, `list`, `complete`, `edit`, `delete`, `stats`)은 모두 동일하게 동작합니다.

**인수 기준:**
- [ ] 새 `storage.py` 모듈에 `load() -> list[dict]` 및 `save(tasks: list[dict]) -> None` 메서드를 갖는 `TaskStorage` 프로토콜을 정의합니다.
- [ ] `LocalStorage`는 기존 JSON 파일 방식을 사용해 `TaskStorage`를 구현합니다.
- [ ] `AzureTableStorage`는 `azure-data-tables`를 사용해 `TaskStorage`를 구현합니다.
- [ ] `app.py`는 시작 시 `get_storage()`를 호출해 적절한 구현을 가져옵니다.
- [ ] `.env` 파일이 있으면 `python-dotenv`를 사용해 `AZURE_STORAGE_CONNECTION_STRING`을 불러옵니다.
- [ ] 환경 변수가 설정되어 있지만 연결에 실패하면 명확한 오류를 출력하고 종료 코드 1로 종료합니다.
- [ ] `requirements.txt`에 `azure-data-tables`와 `python-dotenv`를 추가합니다.
- [ ] 두 저장소 구현을 모두 테스트합니다. Azure 호출은 `unittest.mock`으로 모의 처리합니다.
- [ ] 소스 코드에 연결 문자열이나 계정 키가 포함되지 않습니다.

**제약 사항:**
- 이전 버전의 `azure-storage-table` SDK가 아닌 `azure-data-tables`를 사용합니다.
- Azure 엔터티에는 `PartitionKey = "tasks"`와 `RowKey = str(task["id"])`를 사용합니다.
- CLI 인터페이스와 작업 스키마는 변경하지 않습니다.

**완료 기준:**
- [ ] 실제 Azure Storage 계정으로 `python app.py add "Test" && python app.py list`가 동작합니다.
- [ ] 기존 테스트가 모두 통과합니다.
- [ ] Azure 호출을 모의 처리하는 `AzureTableStorage` 테스트를 추가합니다.

---

### PR에서 중점적으로 확인할 사항

Copilot의 구현을 검토할 때 다음 사항에 특히 주의하세요.

- **하드코딩된 자격 증명이 있나요?** 있다면 심각한 보안 문제입니다.
- **저장소 추상화가 실제로 두 구현을 분리하나요?** 아니면 모든 로직이 `app.py`에 들어가 있나요?
- **Azure 오류를 적절하게 처리하나요?** 아니면 Python 스택 추적을 그대로 노출하나요?
- **테스트가 실제로 외부 서비스와 격리되어 있나요?** 실제 Azure 호출이 아니라 모의 호출을 사용해야 합니다.

---

## 선택 2: Azure OpenAI를 활용한 작업 자동 분류 추가하기

### 아키텍처

```
python app.py add "Renew SSL certificate"
    └─► Azure OpenAI: "Renew SSL certificate 작업의 카테고리를 하나 추천해 주세요"
            └─► 반환값: "devops"
                    └─► 작업을 다음 태그와 함께 저장: ["devops"]
```

### 바로 사용할 수 있는 이슈 예시

---

**제목:** `add` 명령에 Azure OpenAI 기반 지능형 태그 추천 추가

**문제 정의:**
사용자는 작업을 추가할 때 태그 지정을 자주 잊습니다. 태그가 지정되지 않았을 때 Azure OpenAI를 활용해 카테고리 태그 하나를 자동으로 추천하려고 합니다.

**기대 동작:**
- `AZURE_OPENAI_ENDPOINT`, `AZURE_OPENAI_API_KEY`, `AZURE_OPENAI_DEPLOYMENT`가 모두 설정되어 있고 사용자가 `--tag` 인수를 하나도 지정하지 않았을 때만 Azure OpenAI를 호출해 작업에 맞는 태그 하나를 추천합니다.
- 추천 태그를 자동으로 추가하고 사용자에게 `[AI suggested tag: devops]`와 같이 표시합니다.
- 환경 변수 중 하나라도 없거나 AI 호출이 실패하면 태그 없이 작업을 저장합니다. AI 기능을 사용할 수 없어도 사용자의 작업을 막지 않도록 기본 기능은 정상 제공해야 합니다.
- AI 추천을 완전히 건너뛰는 `--no-ai` 플래그를 `add`에 추가합니다.

**인수 기준:**
- [ ] 새 `ai.py` 모듈에 `suggest_tag(task_name: str, description: str) -> str | None` 함수를 구현합니다.
- [ ] 환경 변수의 자격 증명을 사용해 `openai.AzureOpenAI`를 호출합니다.
- [ ] 시스템 프롬프트는 문장 부호 없이 소문자 태그 하나만 반환하도록 모델에 지시합니다.
- [ ] `add` 명령은 `--tag` 플래그가 하나도 없고 `--no-ai`도 설정되지 않았을 때만 `suggest_tag`를 호출합니다.
- [ ] AI 호출에서 발생하는 모든 예외를 처리하고 로그에 기록하며, 작업은 정상적으로 저장합니다.
- [ ] `requirements.txt`에 `openai`를 추가합니다.
- [ ] `suggest_tag` 테스트는 OpenAI 클라이언트를 모의 처리하며, 실제 API를 호출하지 않습니다.

**제약 사항:**
- AI 호출로 사용자가 5초 넘게 기다리게 해서는 안 됩니다. 클라이언트에서 `timeout=5`를 사용합니다.
- API 키 원문을 로그에 남기거나 출력하지 않습니다.
- `--help`에 `--no-ai` 플래그를 설명합니다.

**완료 기준:**
- [ ] 환경 변수를 설정한 상태에서 `python app.py add "Deploy to production"`을 실행하면 AI가 추천한 태그를 표시합니다.
- [ ] `python app.py add "Deploy" --no-ai`는 AI 호출을 건너뜁니다.
- [ ] 기존 테스트가 모두 통과합니다.
- [ ] 모의 응답과 오류 사례를 포함한 `suggest_tag` 테스트를 추가합니다.

---

### PR에서 중점적으로 확인할 사항

- **AI 호출이 정말 선택 사항인가요?** 환경 변수가 설정되어 있지 않아도 앱은 동작해야 합니다.
- **시간 제한이 실제로 적용되나요?** 느린 OpenAI 호출 때문에 CLI가 멈춰서는 안 됩니다.
- **프롬프트가 잘 설계되어 있나요?** Copilot에 시스템 프롬프트를 보여 달라고 요청하세요. 출력 형식을 명확하게 제한하나요?
- **오류를 조용히 무시하고 있지는 않나요?** 오류는 처리하고 로그에 기록해야 하며, 아무런 기록 없이 무시해서는 안 됩니다.

---

## 심화 목표: 전체 개발 과정을 두 번 반복하기

1. Copilot과 함께 AI 네이티브 개발 과정을 따라 선택 1(Azure 저장소)을 완료합니다.
2. 병합한 뒤 선택 2(Azure OpenAI)에 대한 새 이슈를 작성하고 같은 과정을 다시 진행합니다.

마치고 나면 다음 기능을 갖춘 앱이 완성됩니다.
- Azure Table Storage에 작업 저장
- 작업 생성 시 AI를 이용한 자동 분류
- 클라우드 호출을 모의 처리하는 전체 테스트 모음
- 환경 변수를 통한 모든 자격 증명 로딩

여러분과 Copilot의 협업으로 프로덕션 수준의 AI 네이티브 클라우드 애플리케이션을 만든 것입니다.

---

## 다음 단계

- [Azure Table Storage Python 빠른 시작](https://learn.microsoft.com/en-us/azure/storage/tables/table-storage-quickstart-create-python) 읽어 보기
- [Azure OpenAI Python 빠른 시작](https://learn.microsoft.com/en-us/azure/ai-services/openai/quickstart?pivots=programming-language-python) 읽어 보기
- [GitHub Copilot 문서](https://docs.github.com/en/copilot) 살펴보기

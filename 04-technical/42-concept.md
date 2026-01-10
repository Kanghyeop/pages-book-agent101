---
title: "42. 개발자와 대화하기 위한 어휘"
nav_order: 2
parent: Part 4
---

앞선 간호사 번표 사례에서 복잡한 용어들이 등장했습니다. 이 챕터에서 그 용어들을 정리합니다. 어려운 기술을 이해한다기보다는 국어사전을 보듯이 단어들의 의미를 파악하면 됩니다.

---

## LLM은 언제 쓰이나?

간호사 번표 사례에서 LLM이 사용된 시점을 명확히 하겠습니다:

**구축 시 (Development)**
- Claude Code로 바이브 코딩
- 규칙을 수학적 제약조건으로 변환하는 코드 작성
- Streamlit UI 개발
- → LLM이 "개발자 역할"을 함

**운영 시 (Runtime)**
- 완성된 Python 스크립트가 실행됨
- 최적화 엔진은 수학적 알고리즘 (LLM 아님)
- → LLM을 반복 호출하지 않음 (비용 효율적)

**예외: 인코딩/디코딩**
- Excel을 숫자 행렬로 변환할 때 LLM 사용 가능
- 복잡한 형식 해석이 필요한 경우

이 구분이 중요한 이유: 모든 시스템이 LLM을 "매번" 호출하는 건 아닙니다.

---

## Part 1: 모델 계층 - 두뇌의 종류

### LLM이란?

LLM은 Large Language Model의 약자입니다. 텍스트를 받아서 텍스트를 내보내는 기계입니다.

```
입력: "오늘 서울 날씨는"
    ↓
LLM: 다음에 올 단어 확률 계산
    - "맑습니다" 35%
    - "흐립니다" 25%
    ↓
출력: "맑습니다"
```

비유: 수십억 권의 책을 읽은 사람에게 "이 문장 뒤에 뭐가 올 것 같아?"라고 물어보는 것.

### Serving: 모델을 쓸 수 있게 만드는 것

모델을 학습했다고 바로 쓸 수 있는 게 아닙니다. 서비스로 제공해야 합니다.

![LLM Serving 구조](/pages-book-agent101/assets/llm-serving.svg)

OpenAI, Anthropic, Google 같은 회사들이 하는 일의 상당 부분이 이 Serving입니다.

### Routing: 어떤 모델로 보낼지 결정

- 간단한 질문: 작고 빠른 모델로 충분
- 복잡한 추론: 크고 정확한 모델이 필요
- 코드 생성: 코드 특화 모델이 유리

요청의 특성에 따라 적절한 모델로 보내는 것을 Routing이라고 합니다.

### Aggregator: 여러 모델을 하나로

여러 AI 회사의 모델을 각각 따로 연동하려면 귀찮습니다. Aggregator는 하나의 API로 여러 모델을 사용할 수 있게 해줍니다. 대표적으로 OpenRouter가 있습니다.

---

## Part 2: 연결 기초 - 시스템이 대화하는 법

### Interface와 Protocol

두 시스템이 대화하려면 규약이 필요합니다.

- Interface(인터페이스): 두 시스템이 만나는 접점. 예: API 엔드포인트
- Protocol(프로토콜): 대화하는 방식. 예: HTTP, HTTPS

비유: 국제 비즈니스 미팅에서 Interface는 회의실, Protocol은 영어.

### HTTP와 API

![HTTP 요청/응답](/pages-book-agent101/assets/http-request-response.svg)

HTTP: 웹에서 정보를 주고받는 표준 방식
API: 프로그램이 다른 프로그램과 대화하는 방법

![API 호출 구조](/pages-book-agent101/assets/api-structure.svg)

### JSON: 데이터의 공용어

```json
{
  "name": "홍길동",
  "age": 30,
  "skills": ["Python", "SQL", "Excel"]
}
```

사람이 읽기 쉽고, 컴퓨터가 처리하기도 쉬워서 API의 표준 형식이 되었습니다.

### SDK: 개발 도구 모음

SDK(Software Development Kit): 특정 서비스를 쉽게 사용하게 해주는 도구 모음

```python
# SDK 없이: 직접 HTTP 요청 작성, 헤더, 인증, 에러 처리 모두 직접

# SDK 사용:
from openai import OpenAI
client = OpenAI()
response = client.chat.completions.create(...)
```

---

## Part 3: 개발 스택 - 무엇으로 만드나

### Application과 Framework

- Application: 완성된 프로그램 (예: ChatGPT, Notion)
- Framework: 프로그램을 만드는 뼈대 (예: LangChain, Django)

비유: Application은 완성된 집, Framework은 조립식 주택 골조.

### Frontend와 Backend

![Frontend vs Backend](/pages-book-agent101/assets/frontend-backend.svg)

- Frontend: 사용자가 보는 화면 (버튼, 입력창, 결과 표시)
- Backend: 서버에서 돌아가는 로직 (데이터 처리, API 호출)

간호사 번표에서:
- Frontend: Streamlit (파일 업로드/다운로드 버튼, 점수 표시)
- Backend: Python (Excel 파싱, 규칙 검증, 최적화 엔진)

---

## Part 4: 비개발자를 위한 핵심 용어

### Runtime: 실행 환경

프로그램이 "돌아가는" 환경입니다.

비유: 대본(코드)이 있어도 극장(Runtime)이 없으면 연극 불가.

### 토큰: LLM의 처리 단위

LLM은 글자 단위가 아니라 토큰 단위로 처리합니다.

- "Hello, world!" → 4 토큰
- "안녕하세요" → 3~5 토큰 (모델마다 다름)

왜 중요한가?
- 비용이 토큰당 과금됨
- 컨텍스트 윈도우가 토큰 단위로 제한됨

### 파이프라인: 데이터가 흐르는 경로

![파이프라인 흐름](/pages-book-agent101/assets/pipeline-flow.svg)

파이프라인이 명확하면:
- 어느 단계에서 문제가 생겼는지 찾기 쉬움
- 각 단계를 독립적으로 개선 가능

### 서버리스: 서버 관리 없이 배포

Serverless: 서버가 없는 게 아니라, 서버 관리를 안 해도 되는 것

간호사 번표 시스템:
- Streamlit으로 만든 앱을 Streamlit Cloud에 올리면
- 자동으로 서버 설정, 배포, 운영
- 개발자 1명이 서버 걱정 없이 서비스 운영 가능

---

## 마무리

이 개념들은 간호사 번표뿐 아니라 어떤 시스템을 만들든 등장합니다:

- **Frontend/Backend**: 사용자 화면과 서버 로직의 분리
- **API/HTTP**: 시스템 간 통신
- **파이프라인**: 데이터 처리 흐름
- **서버리스**: 배포와 운영

다음 챕터에서는 이 개념들을 활용해 더 복잡한 워크플로우를 다루는 LangChain과 LangGraph를 살펴봅니다.

---

작성일: 2026-01-08
Chapter: Part 4, Chapter 4.2
키워드: LLM, Serving, Routing, API, JSON, SDK, Frontend, Backend, Runtime, 토큰, 파이프라인, 서버리스

---
<!-- LLM Context Anchor -->
**핵심 요약**: 에이전트 시스템 핵심 용어 정리. LLM=다음 단어 확률 예측기. Serving=모델을 서비스로 제공, Routing=적절한 모델로 분배, Aggregator(OpenRouter)=여러 모델 통합. API/HTTP=시스템 간 통신, JSON=데이터 공용어, SDK=개발도구모음. Frontend=사용자화면, Backend=서버로직. 토큰=LLM처리단위(과금/컨텍스트 제한), 파이프라인=데이터 흐름, 서버리스=서버관리 없이 배포.

**키워드**: `LLM` `Serving` `Routing` `API` `JSON` `SDK` `Frontend` `Backend` `토큰` `파이프라인` `서버리스`

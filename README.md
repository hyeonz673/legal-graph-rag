# Legal AI Chatbot

> **누구나 쉽고 정확하게 법률 정보를 찾는 AI 챗봇**

Legal AI Chatbot은 복잡하고 어려운 법률 문제로 고민하는 일반인들을 위해 만들어졌습니다. 고성능 벡터 검색(Vector Search) 기술을 활용하여 사용자의 질문을 법률적 문맥으로 이해하고, 신뢰할 수 있는 법령과 판례를 기반으로 명확한 답변을 제공합니다.

## 🌟 주요 특징

*   **🤝 Accessibility**: 법률 전문가가 아니어도 자연어로 편하게 질문할 수 있습니다.
*   **🎯 High-Precision**: 사용자의 질문 의도를 정확히 파악하여, 방대한 법령 데이터 속에서 꼭 필요한 정보를 찾아냅니다.
*   **📝 Verifiable Citations**: LLM이 생성한 답변에 반드시 실제 법령/조문/판례의 출처를 명시하도록 강제하여 신뢰성을 보장합니다.

## 🏗️ 아키텍처

```mermaid
graph TD
    subgraph "Data Pipeline (ETL)"
        RAW[법령 API/PDF] -->|Ingest| NORM[정규화]
        NORM -->|Chunking + Meta| CHUNK[Document Chunks]
        CHUNK -->|Embedding| VEC[(Chroma Vector DB)]
    end

    subgraph "Retrieval & Generation (RAG)"
        Q[User Query] -->|Embedding| Q_VEC[Query Vector]
        Q_VEC -->|Vector Search| CAND[Candidate Chunks]
        CAND -->|Rerank| CTX[Top-K Context]
        
        CTX -->|Prompt| LLM[Local LLM (Ollama)]
        LLM -->|Generate| ANS[Answer w/ Citation]
    end
```

## 🛠️ 기술 스택

| 분류 | 기술 | 비고 |
| --- | --- | --- |
| **Language** | Python 3.10+ | |
| **LLM** | Ollama | Llama 3, Qwen 2.5 등 사용 |
| **Vector DB** | Chroma | 임베딩 벡터 저장소 |
| **Embedding** | bge-m3 / e5 | 로컬 임베딩 모델 |
| **Framework** | FastAPI / Streamlit | 백엔드 및 UI |

## 🚀 시작하기 (Getting Started)

### 사전 요구사항 (Prerequisites)
1.  **[Ollama](https://ollama.com/)** 설치 및 모델 다운로드 (`ollama pull llama3`)
2.  Python 3.10 이상

### 설치 (Installation)

```bash
# 저장소 클론
git clone https://github.com/hyeonz673/legal-graph-rag.git
cd legal-graph-rag

# 가상환경 생성 및 활성화
python -m venv venv
source venv/bin/activate  # Mac/Linux
# venv\Scripts\activate  # Windows

# 의존성 설치 (추후 requirements.txt 생성 예정)
# pip install -r requirements.txt
```

### 실행 (Usage) - *개발 예정*

1.  **데이터 수집 및 적재**
    ```bash
    python pipeline/ingest_api.py
    python pipeline/index_docs.py
    ```

2.  **웹 인터페이스 실행**
    ```bash
    streamlit run app.py
    ```

## 📂 프로젝트 구조

```
legal-graph-rag/
├── data/               # Raw 데이터 및 DB 파일
├── docs/               # 문서 (PRD, Ideation, Task)
├── pipeline/           # ETL 파이프라인 스크립트
│   ├── ingest_api.py
│   └── index_docs.py
├── rag/                # RAG 검색 및 생성 로직
└── app.py              # Streamlit UI 진입점
```

## 📜 문서
상세한 기획 및 개발 계획은 다음 문서들을 참고하세요.
*   [제품 요구사항 정의서 (PRD)](./docs/PRD.md)
*   [개발 태스크 목록 (Task)](./docs/Task.md)
*   [아이디에이션 노트 (Ideation)](./docs/Ideation.md)

## 🤝 기여 (Contributing)
이 프로젝트는 학습 및 포트폴리오 목적으로 진행 중입니다. 이슈 및 PR 환영합니다!

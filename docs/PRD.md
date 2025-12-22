# Legal GraphRAG 프로젝트 PRD

## 1. 프로젝트 개요
*   **제품명**: Legal GraphRAG (Local-first)
*   **비전**: 법률 지식 그래프(KG)와 벡터 검색을 결합하여, 검증 가능하고 근거가 확실한 법률 답변을 제공하는 로컬 우선, 프라이버시 중심의 RAG 시스템.
*   **핵심 가치**: "로컬에서 빠르게 돌아가는 GraphRAG." 그래프 연결(법령-조문-개념)을 엄격하게 따르고 벡터 유사도로 검증함으로써, 법률 검색의 "환각(Hallucination)" 문제와 "정확한 정보 찾기의 어려움"을 해결함.

## 2. 핵심 목표
*   **로컬 우선 아키텍처 (Local-First)**: 프라이버시 보호와 데이터 유출 방지를 위해 모든 인프라(Ollama, Neo4j Desktop, 내장 벡터 저장소)를 로컬에서 구동.
*   **하이브리드 검색 (Hybrid Retrieval)**: 높은 재현율(Recall)을 위한 그래프 탐색과 높은 정밀도(Precision)를 위한 벡터 재순위화(Reranking)를 결합한 2단계 검색 프로세스 구현.
*   **검증 가능한 생성 (Verifiable Generation)**: LLM 출력을 엄격히 제어하여 반드시 인용(법령/조문/판례)을 포함하게 하고, 사용자가 출처를 검증할 수 있도록 함.
*   **자동화된 평가 (Automated Evaluation)**: 검색 품질(Hit@K, Graph Coverage)과 생성 품질(인용률, 근거 기반성)을 측정하는 파이프라인 구축.

## 3. 기술 스택
| 구성 요소 | 기술 | 선정 이유 |
| :--- | :--- | :--- |
| **언어** | Python 3.10+ | AI/데이터 엔지니어링 표준 언어. |
| **인터페이스** | Streamlit | 빠른 프로토타이핑 및 "처리 과정" 패널 시각화 용이. |
| **Graph DB** | Neo4j Desktop | Graph RAG 표준, 뛰어난 시각화 도구(Bloom) 제공. |
| **Vector DB** | Chroma | 가볍고 로컬 친화적이며 통합이 쉬움. |
| **LLM** | Ollama (Llama3 / Qwen2.5) | 고성능 로컬 인퍼런스 지원. |
| **임베딩** | bge-m3 or e5-mistral | 로컬 구동 가능한 최신 다국어/Dense 검색 모델. |
| **ETL** | Makefile / CLI (Typer) | 단순하고 명확한 파이프라인 단계 정의. |
| **평가** | Custom Rules + Ragas | 법률 도메인 정확도를 위한 맞춤형 지표. |

## 4. 시스템 아키텍처 및 데이터 파이프라인
시스템은 크게 **인덱싱(ETL)**과 **검색(Retrieval)** 두 가지 주요 워크플로우로 나뉩니다.

### 4.1 데이터 파이프라인 (인덱싱)
1.  **수집 (`ingest_api.py`)**:
    *   공공 법률 API에서 표준 법률 데이터(법령, 조문, 시행일)를 가져옵니다.
    *   Raw 응답을 JSON으로 저장합니다.
2.  **정규화 (`normalize.py`)**:
    *   Raw 데이터를 관계형/그래프 적재 가능한 스키마(Parquet/CSV)로 변환합니다.
    *   엔티티: `Law`(법령), `Article`(조문), `Clause`(항/호), `Case`(판례).
3.  **그래프 구축 (`build_graph.py`)**:
    *   노드와 엣지를 Neo4j에 적재합니다.
    *   **노드**: `(Law)`, `(Article)`, `(Concept)`, `(Case)`, `(DocumentChunk)`
    *   **엣지**:
        *   `(:Law)-[:HAS_ARTICLE]->(:Article)`
        *   `(:Article)-[:REFERS_TO]->(:Article)` (인용 추출)
        *   `(:Case)-[:CITES]->(:Article)`
        *   `(:Concept)-[:DEFINED_IN]->(:Article)`
4.  **벡터 인덱싱 (`chunk_docs.py`)**:
    *   PDF 문서(판례, 해설서)를 청킹(Chunking)합니다.
    *   임베딩을 생성하여 Chroma DB에 저장합니다.
5.  **링킹 (`linking.py`)**:
    *   비정형 청크를 구조화된 그래프 노드와 연결합니다 (예: 청크가 "제5조"를 언급하면 `(:DocumentChunk)-[:MENTIONS]->(:Article {id:5})` 생성).

### 4.2 검색 파이프라인 (Hybrid RAG)
1.  **엔티티 추출**: 사용자 질문에서 키워드/엔티티를 추출합니다 (예: "계약 해지", "제54조").
2.  **Step A: 그래프 검색 (후보 생성)**:
    *   Neo4j에서 시작 노드를 찾습니다.
    *   K-hop 확장을 통해 관련된 조문, 판례, 개념을 탐색합니다.
    *   출력: 관련 텍스트 블록(조문) 및 관계들의 집합.
3.  **Step B: 벡터 검색 및 재순위화 (Reranking)**:
    *   벡터 유사도를 기반으로 Chroma에서 Top-K 청크를 검색합니다.
    *   그래프 후보군 + 벡터 후보군을 결합합니다.
    *   질문과의 관련성에 따라 재순위화합니다 (Cross-Encoder 또는 가중치 코사인 유사도 사용).
4.  **생성 (Generation)**:
    *   컨텍스트(텍스트 + 그래프 경로)를 프롬프트에 주입합니다.
    *   엄격한 인용 형식을 갖춘 답변을 생성하도록 LLM에 요청합니다.

## 5. 기능 요구사항
### 5.1 MVP 기능 (우선순위 1)
*   [ ] **CLI 파이프라인**: 수집, 구축, 청킹을 위한 스크립트 명령어 제공.
*   [ ] **기본 그래프 지원**: Law -> Article 구조의 완전한 탐색 지원.
*   [ ] **하이브리드 검색**: 그래프 결과와 벡터 결과를 병합하는 쿼리 핸들러.
*   [ ] **프론트엔드**: 다음을 포함한 간단한 Streamlit 앱:
    *   채팅 입력창.
    *   "그래프 경로" 시각화/텍스트 패널 (추적 가능성).
    *   "출처 문서" 기능.
*   [ ] **인용**: 답변은 반드시 `[법령명 제X조]` 형식을 포함해야 함.

### 5.2 확장 기능 (우선순위 2)
*   [ ] **개념 그래프 (Concept Graph)**: 약 50개의 주요 법률 용어를 수동 또는 자동으로 큐레이션.
*   [ ] **스마트 링킹**: 조문 텍스트 내 "REFERS_TO" 관계를 LLM 기반으로 추출.
*   [ ] **평가 대시보드**: 테스트 셋에 대한 Hit@K 점수를 보여주는 간단한 CLI 출력.

## 6. 데이터 모델 (Schema)
### 노드 (Nodes)
*   **Law**: `id`, `name`, `enact_date` (시행일)
*   **Article**: `id`, `number`, `content`, `law_id`
*   **Concept**: `name`, `definition`
*   **DocumentChunk**: `id`, `text`, `source_file`, `page`

### 엣지 (Edges)
*   `HAS_ARTICLE`: Law -> Article
*   `REFERS_TO`: Article -> Article (상호 참조)
*   `DEFINED_IN`: Concept -> Article
*   `MENTIONS`: DocumentChunk -> Law/Article/Concept

## 7. 성공 지표
*   **검색 성능 (Retrieval Performance)**:
    *   **Hit@K**: 정답 조문이 상위 K개(예: 5개) 검색 결과 내에 존재하는가?
    *   **Graph Coverage**: 쿼리당 연결된 고유 법령/조문의 평균 개수.
*   **답변 품질 (Answer Quality)**:
    *   **인용률 (Citation Rate)**: 주장이 유효한 출처 인용으로 뒷받침되는 비율.
    *   **근거 기반성 (Groundedness)**: 답변의 키워드가 검색된 컨텍스트에서 추적 가능한 비율.

## 8. 개발 로드맵
1.  **Day 1-2**: 데이터 API 클라이언트 및 그래프/벡터 DB 설정.
2.  **Day 3**: 파이프라인 구현 (수집 -> 그래프 적재 + 임베딩).
3.  **Day 4**: 검색 로직 (하이브리드 전략) 및 기본 평가 구현.
4.  **Day 5**: UI 구현 (Streamlit) 및 데모 완성도 제고.

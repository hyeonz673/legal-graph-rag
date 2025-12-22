MVP 한 줄 컨셉

Legal GraphRAG (Local-first)

법률 API(법령/조문/판례 메타) + 로컬 문서(PDF/메모)를 수집해 법률 지식그래프(KG) 를 만들고,
그래프 탐색 + 벡터 검색을 결합해 답변하며, Retrieval/Answer 평가까지 자동화하는 시스템

0) 기술 스택 (로컬 생산성 최적)

Python: FastAPI(옵션) / Streamlit(UI) / Typer(CLI)

Graph DB: Neo4j Desktop 로컬 (가장 면접 친화 + 시각화 가능)

Vector DB: Chroma (로컬 간편)

LLM: Ollama (Llama3 / Qwen2.5 등)

Embedding: bge-m3 or e5-mistral(로컬 가능)

ETL/파이프라인: Prefect(옵션) or Makefile + CLI (MVP는 CLI 추천)

Eval: Ragas(가능하면) + 룰 기반(필수)

핵심: “GraphRAG인데도 로컬에서 빠르게 돌아간다”를 보여주는 구성

1) 데이터 파이프라인 설계 (API → 정규화 → 그래프 적재)
1.1 입력 데이터

법률 API (필수)

법령 목록 / 조문 / 시행일 / 개정이력 / 관련 법령 링크(있으면)

로컬 문서 (선택이지만 강력 추천)

약관 PDF, 내부 가이드 문서, 판례 요약 등

1.2 정규화 스키마 (면접에서 “모델링”으로 먹히는 포인트)
그래프 노드(예시)

Law (법령)

Article (조문)

Clause (항/호) — MVP에선 Article까지만 해도 OK

Case (판례) — API 있으면 넣고, 없으면 로컬문서로 대체 가능

Concept (법률 용어/개념) — 최소 20~50개만이라도 만들면 GraphRAG 느낌 확 살아남

DocumentChunk (로컬 문서 chunk)

관계(예시)

(Law)-[:HAS_ARTICLE]->(Article)

(Article)-[:REFERS_TO]->(Law|Article) ← 조문 내 “준용/인용”을 잡아주면 GraphRAG 감성 제대로

(Case)-[:CITES]->(Article)

(Concept)-[:DEFINED_IN]->(Article)

(DocumentChunk)-[:MENTIONS]->(Law|Article|Concept)

MVP 핵심은 “그래프에서 연결을 따라가며 근거를 모은다”가 성립하는 최소 관계만 잡는 것.

1.3 파이프라인 단계 (CLI로 쪼개기)

ingest_api.py: API 수집 → raw JSON 저장

normalize.py: raw → 정규화 테이블(parquet/csv)

build_graph.py: Neo4j 적재 (MERGE로 중복 방지)

chunk_docs.py: PDF → chunk + embedding → Chroma

linking.py: chunk ↔ (Law/Article/Concept) 매핑(간단한 rule + LLM optional)

2) Graph RAG 설계 (Graph Traversal + Vector Hybrid)

GraphRAG를 “말로만” 하지 않고, 실제로 두 단계 Retrieval로 보여주면 직무 어필이 강해져.

2.1 Retrieval 전략 (권장)
Step A) 그래프 기반 후보 생성 (Graph Retrieval)

질문에서 엔티티 후보 추출 (law/article/concept)

Neo4j에서 K-hop 확장으로 관련 조문/법령/개념 후보를 뽑음

예: “해지 환급금” → (Concept:해지환급금) → 연결된 Article들

Step B) 후보를 벡터로 랭킹 (Vector Rerank)

그래프에서 뽑은 후보(Article 텍스트, 관련 chunk)를 임베딩 유사도로 재정렬

동시에 Chroma에서 문서 chunk top-k도 가져와 합치기

👉 결론: Graph로 “범위를 줄이고” Vector로 “정밀도”를 올림

3) Generation 설계 (근거 강제 + 인용)

LLM이 아무 말 못 하게 프롬프트를 강제하는 게 포인트.

입력: {Question, GraphContext(조문/연결), DocContext(Chunk), Citations}

출력 규칙:

결론 3줄

근거 조문/출처 bullet

“불확실/추가 확인 필요” 조건 명시

또한 결과에 항상:

참고 조문: 법령명-조문번호

참고 문서: 파일명-페이지/chunk_id
를 붙이게 하면 “RAG 시스템”이라는 게 한 눈에 보여.

4) 평가(Evaluation) 설계 (이게 합격/불합격 갈림)

GraphRAG MVP에서 평가를 “딱 3개 지표”만 잡아도 충분히 강해져.

4.1 Retrieval 평가 (필수)

Hit@K: 정답 조문(또는 정답 chunk)이 Top-K에 들어왔는가

MRR: 정답의 평균 순위

Graph Coverage: 답변에 사용된 근거 노드 수(조문 n개, 법령 n개)

“그래프를 썼더니 근거 범위가 넓어졌다/정확해졌다”를 수치로 보여줄 수 있음

4.2 Answer 평가 (필수)

Citation Rate: 문장/단락마다 출처가 붙었는가

Groundedness(룰): 답변에 등장한 핵심 키워드가 Context에 존재하는가(간단 string match로도 MVP OK)

(옵션) Ragas: faithfulness / answer relevancy

5) UI/데모 (Streamlit로 최소만)

“보여주기”는 UI, “실력”은 파이프라인.

화면 구성:

질문 입력

그래프 근거 패널: “선택된 법령/조문/연결(K-hop)”

문서 근거 패널: “Top chunks”

최종 답변(인용 포함)

평가 점수(Hit@K / Citation Rate)

면접관이 가장 좋아하는 화면은 ‘왜 이 답이 나왔는지’가 눈에 보이는 화면이야.

6) 레포 구조 (면접 친화 + 확장 가능)
legal-graph-rag/
├─ data/
│  ├─ raw_api/               # API 원천
│  ├─ normalized/            # 정규화 산출물
│  └─ docs/                  # PDF 등
├─ pipeline/
│  ├─ ingest_api.py
│  ├─ normalize.py
│  ├─ build_graph.py         # Neo4j 적재
│  ├─ chunk_docs.py          # Chroma 적재
│  └─ linking.py             # chunk -> graph linking
├─ rag/
│  ├─ entity_extract.py
│  ├─ graph_retrieve.py
│  ├─ vector_retrieve.py
│  ├─ fusion_rank.py
│  └─ generate.py
├─ eval/
│  ├─ retrieval_eval.py
│  └─ answer_eval.py
├─ app.py                    # Streamlit
├─ Makefile or task.py        # run: make ingest/build/query/eval
└─ README.md

7) “MVP 범위”를 딱 잡아주는 기능 리스트 (너무 커지지 않게)
반드시 포함 (2~3일 MVP로도 가능)

API 수집 → Law/Article 그래프 적재

질문 → 엔티티 추출(룰 기반도 OK) → K-hop 그래프 탐색

그래프 결과 + 벡터 결과 합쳐서 답변

인용 출력

Retrieval/Answer 평가 최소 2개 지표

있으면 확실히 ‘GraphRAG’ 느낌 나는 것 (추가 1~2일)

조문 내 “인용/준용” 관계 추출 → REFERS_TO

Concept 노드(용어 사전) 추가

Neo4j Bloom 스크린샷/짧은 데모 GIF

8) 면접에서 이 프로젝트를 “직무 요구사항”으로 번역하는 문장

데이터 파이프라인: “법령 API 수집→정규화→그래프 적재 및 문서 임베딩까지 자동화”

RAG: “그래프 기반 후보 생성 후 벡터 랭킹으로 정밀도를 높이는 Hybrid GraphRAG 구현”

평가: “Hit@K/MRR + Citation/Groundedness로 검색과 답변 품질을 분리 측정”

원하면 다음은 내가 바로 만들어줄 수 있어 (선택만 해줘, 질문은 안 할게):

Neo4j 그래프 스키마(Cypher constraints + MERGE 쿼리)

Graph Retrieval Cypher 템플릿(K-hop, refer-to 확장, score)

Fusion Rank 로직(그래프 후보 + 벡터 후보 결합)

평가 스크립트 최소 구현(Hit@K / Citation Rate)

README(채용용 스토리 + 아키텍처 그림 포함)
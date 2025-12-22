# Legal GraphRAG 프로젝트 작업 목록

이 문서는 `docs/PRD.md`에 정의된 요구사항과 로드맵을 기반으로 작성된 상세 개발 작업 목록입니다.

## 1. 초기 설정 및 환경 구축 (Day 1-2)
- [ ] **프로젝트 구조 설정**
    - [ ] GitHub 저장소 초기화 및 `.gitignore` 설정
    - [ ] Python 가상환경 및 `poetry` 또는 `pip` 의존성 설정 (`requirements.txt`)
    - [ ] `docs/`, `data/`, `pipeline/`, `rag/`, `app/` 디렉토리 구조 생성
- [ ] **데이터베이스 설치 및 설정**
    - [ ] Neo4j Desktop 설치 및 로컬 인스턴스 구동
    - [ ] Neo4j 연결 테스트 (`neo4j-driver`)
    - [ ] ChromaDB 설치 및 간단한 입출력 테스트

## 2. 데이터 파이프라인 (ETL) 구현 (Day 2-3)
- [ ] **데이터 수집 (`pipeline/ingest_api.py`)**
    - [ ] 법제처 등 Open API 연동 클라이언트 구현
    - [ ] 법령/조문 데이터 Fetch 및 Raw JSON 저장 기능
- [ ] **데이터 정규화 (`pipeline/normalize.py`)**
    - [ ] JSON 데이터를 Law, Article, Clause 엔티티로 파싱
    - [ ] CSV 또는 Parquet 형태로 정규화된 데이터 저장
- [ ] **그래프 적재 (`pipeline/build_graph.py`)**
    - [ ] Neo4j 제약 조건(Constraints) 및 인덱스 생성
    - [ ] `Law`, `Article` 노드 생성 Cypher 쿼리 구현
    - [ ] `HAS_ARTICLE` 엣지 생성
- [ ] **문서 임베딩 (`pipeline/chunk_docs.py`)**
    - [ ] 판례/문서 PDF 텍스트 추출
    - [ ] Chunking 로직 구현
    - [ ] 임베딩 모델(bge-m3 등) 로드 및 벡터 생성
    - [ ] ChromaDB 적재

## 3. 검색(RAG) 엔진 구현 (Day 4)
- [ ] **검색 모듈 (`rag/`)**
    - [ ] **엔티티 추출**: 사용자 쿼리에서 법률 용어/조문 번호 추출 로직
    - [ ] **Graph Retrieval**: Neo4j Cypher 쿼리로 K-hop 이웃 조문 탐색
    - [ ] **Vector Retrieval**: ChromaDB 유사도 검색
    - [ ] **Hybrid Fusion**: 그래프 및 벡터 결과 결합 및 재순위화(Reranking)
- [ ] **생성 모듈 (`rag/generate.py`)**
    - [ ] Ollama (Llama 3 등) 연동
    - [ ] System Prompt 작성 (인용 형식 강제, 근거 기반 답변 유도)

## 4. UI 및 평가 (Day 5)
- [ ] **웹 인터페이스 (`app.py`)**
    - [ ] Streamlit 기본 레이아웃 구성
    - [ ] 채팅 입력창 및 히스토리 관리
    - [ ] **사이드 패널**: 검색된 그래프 경로(Graph Path) 및 참고 문헌 시각화
- [ ] **기본 평가 수행**
    - [ ] 테스트 셋(질문-정답 쌍) 10개 준비
    - [ ] Hit@K (검색 정확도) 측정 스크립트 작성
    - [ ] Citation Rate (인용 포함 여부) 확인

## 5. 확장 기능 (Phase 2 - Optional)
- [ ] `REFERS_TO` (참조) 관계 자동 추출 및 그래프 적재
- [ ] 중요 법률 용어(Concept) 노드 추가 및 연결
- [ ] Ragas 등을 활용한 심화 평가 파이프라인

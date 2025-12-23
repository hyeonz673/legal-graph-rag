# Legal AI Chatbot 프로젝트 작업 목록

이 문서는 `docs/PRD.md`에 정의된 요구사항과 로드맵을 기반으로 작성된 상세 개발 작업 목록입니다.

## 1. 초기 설정 및 환경 구축 (Day 1-2)
- [ ] **프로젝트 구조 설정**
    - [ ] GitHub 저장소 설정 및 `.gitignore` 확인
    - [ ] Python 가상환경 및 의존성 설정 (`requirements.txt`)
    - [ ] `docs/`, `data/`, `pipeline/`, `rag/`, `app/` 디렉토리 구조 생성
- [ ] **벡터 데이터베이스(Vector DB) 설정**
    - [ ] ChromaDB 설치 및 간단한 입출력 테스트
    - [ ] 로컬 임베딩 모델(HuggingFace/SentenceTransformers) 로드 테스트

## 2. 데이터 파이프라인 (ETL) 구현 (Day 2-3)
- [ ] **데이터 수집 (`pipeline/ingest_api.py`)**
    - [ ] 법제처 등 Open API 연동 클라이언트 구현
    - [ ] 법령/조문 데이터 Fetch 및 Raw JSON 저장 기능
- [ ] **데이터 정규화 (`pipeline/normalize.py`)**
    - [ ] JSON 데이터를 분석용 엔티티로 파싱
    - [ ] CSV 또는 Parquet 형태로 정규화된 데이터 저장
- [ ] **벡터 인덱싱 (`pipeline/index_docs.py`)**
    - [ ] **청킹(Chunking)**: 법령 조문 및 판례 텍스트 분할 전략 구현
    - [ ] **메타데이터 처리**: 법령명, 조문번호 등을 메타데이터로 변환
    - [ ] **임베딩 및 적재**: 청크를 임베딩하여 ChromaDB에 적재

## 3. 검색(RAG) 엔진 구현 (Day 4)
- [ ] **검색 모듈 (`rag/`)**
    - [ ] **쿼리 분석**: 사용자 질문 전처리
    - [ ] **Vector Retrieval**: Chroma 유사도 검색 구현 (메타데이터 필터링 포함)
    - [ ] **Reranking**: Cross-Encoder 등을 활용한 검색 결과 재순위화 로직
- [ ] **생성 모듈 (`rag/generate.py`)**
    - [ ] Ollama (Llama 3 등) 연동
    - [ ] System Prompt 작성 (법률 용어 해설 포함, 친절한 어조, 명확한 출처 인용)

## 4. UI 및 평가 (Day 5)
- [ ] **웹 인터페이스 (`app.py`)**
    - [ ] **사용자 친화적 디자인**: 일반인도 쉽게 쓸 수 있는 직관적인 UI 구성 (Streamlit)
    - [ ] ** 친절한 가이드**: 사용법 및 예시 질문 제공
    - [ ] **채팅 입력창**: 자연어 질문 입력을 위한 대화형 인터페이스
    - [ ] **출처 시각화**: 답변의 근거가 되는 법령/판례를 누구나 쉽게 확인할 수 있는 사이드 패널
- [ ] **기본 평가 수행**
    - [ ] 테스트 셋(질문-정답 쌍) 10개 준비
    - [ ] Hit@K (검색 정확도) 등 정량 지표 측정
    - [ ] Citation Rate (인용 포함 여부) 확인

## 5. 확장 기능 (Phase 2 - Optional)
- [ ] 키워드 검색(BM25)을 결합한 하이브리드 검색 구현
- [ ] Ragas 등을 활용한 심화 평가 파이프라인

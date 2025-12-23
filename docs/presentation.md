---
marp: true
theme: gaia
class: lead
paginate: true
backgroundColor: #fff
backgroundImage: url('https://marp.app/assets/hero-background.svg')
style: |
  section { font-family: 'Pretendard', sans-serif; }
  h1 { font-size: 2.5em; color: #333; }
  h2 { font-size: 1.8em; color: #555; }
  strong { color: #0066cc; }
---

# Legal AI Chatbot
## Legal AI Chatbot

**누구나 쉽게 쓰는 내 손안의 AI 변호사**

---

# 1. 프로젝트 개요

## Vision
**"Legal AI for Everyone"**
법률 지식이 없는 일반인도 쉽고 정확하게 법률 정보를 찾을 수 있도록 돕는 **대국민 법률 상담 챗봇**

## 핵심 가치
*   🔒 **Local Environment**: 데이터 유출 걱정 없는 로컬 환경 (Ollama, Chroma)
*   🎯 **High-Precision**: Dense Retrieval + Reranking으로 정확도 향상
*   ⚖️ **Verifiable**: 엄격한 출처(법령/조문) 인용 강제

---

# 2. 핵심 목표

1.  **쉬운 접근성 (Accessibility)**
    *   어려운 법률 용어 대신 쉬운 자연어로 질문 가능
    *   누구나 무료로 평등하게 법률 정보 획득

2.  **신뢰성 (Reliability)**
    *   AI의 답변이 실제 법령/판례에 근거하는지 확인
    *   출처(URL) 제공으로 교차 검증 가능

3.  **정확성 (Accuracy)**
    *   사용자의 모호한 질문도 찰떡같이 알아듣는 검색 성능

---

# 3. 기술 스택 (Local Stack)

| 구분 | 기술 | 설명 |
| :--- | :--- | :--- |
| **Lang** | Python 3.10+ | 주 개발 언어 |
| **V-DB** | **Chroma** | 경량 벡터 저장소 |
| **LLM** | **Ollama** | Llama 3, Qwen 2.5 (Local) |
| **UI** | Streamlit | 빠른 프로토타이핑 |

---

# 4. 시스템 아키텍처

![width:900px](https://mermaid.ink/img/pako:eNp1VE1v2zAM_SuEzmkD-4PtYYc2Q9Fh22EYCvTBcixbaiRJ9BAnGfrvIyU_sixD0Q6JRD4-PpKSH1iZSqGgob5R9oOF0rDeaMvOEla0X7TWinEv2h_Y88P2_cfrt2-b9x_71-37j_3793-717N_-Wq_bt_9P33-_LFPWAew_efX7_7C-MefH7YL_v6_Q_vafrr-CcuPT5_x6_ufQDmgg89ccYg_Fnyy-KewHCzIz_ixBBa8ITSjCQqLXjJYuELrRg8WeFygjU-MWOgd8xZ8bixoXrdgwRQEzEzQ0BhQL_LP4PNiQVofrAX_QWljzjITcDxOTa1PoXoW5KT0LZ8XeIfnYG9Lg3sL9A7DDTgHpcOVeoLFeyNq9S18iXFvJyfX-ryHOYOsZ83tZFOLYBXdeI_2Ezwf8XqChYTrGXcTrIP4CnuHYA1ex9hNrnOxius7iDcRrIPZE7wXmPoAuw1ynrA7gN5g09Xk3E-4vBCr97jcQbQOtcetm1x1Yf1Bug5-fbh9Rf0J0u1kN4vt7eQ8ilVd8dUj9Lfw-gSvz7D6irsJ1reQXsDvMLofqP4BuwfxOoTuR-ofsHsQr4PZR9ydW_cgXYfR_chvrv4BuwfxOph9xN0E61tYeQC_w-h-5HdX_4Ddg3gdQvcTdzPWvxvpC_gdRvcT9S_YPVi0DmafcHfEPYjXYXQ_Uv+A3YN4HUb3E3czrG8h5QH8DqP7iZcX3D2I1yF0P3E30T3o7IT3B-sf8HeY3YNvR-h-RvcgXgd7e5i7Cda3kPIAfofR_UT9C3YPFnUw-wj3K-pPkK_D6NwP1T9g9yBeh9D9CPsWhj9g9yBeB7NPuF9Rf4J4HcYvYf4BuwfxOph9xN0E61tIeQC_w-h+4G6C-gfsHsTrEDqfcD_j-h_E6zC6n3C/wv5BZw_E62BvD3M3w_oWnZ3wP4zOJ9yvuP4H8TqEzifsP1z3IF6H0HnE_YT7D5cfLOpAegfROpQe4X5F_QnydRid-6H6B-wexOsQuh9h3wL_QbxCdw_idTC9A_1P0H8QrxC6A_0P0L8Qr4HZXbj_AdMvsH9Asg6lR7hfoX8hXofSvXD_A6ZfUP8C8YpsHaZ3cL2D6z8QryC6g-sdXP_C_EGyDqV3cL2D638Qr5Csw_QOrndw_YGzD5J1KL2D6x1c_4F4hWwdTO_gegfXPyBeh9K7sL8w_wGzP4zOJ9yvuP4H8TqEziPcT7j-B_E6jN6F2T_495zsLTq7sPjzEXobgTvC3YPdz-vlwO8wup-4X3H9D-J1CN1P3E-4_gPxOozeh9lH3E10D3b3MF_A7zC6n7if8fqDeB1C9xPuP9z_gHgdRu_DLvMR7ia6B5ud8P7Ar8PofuJ+xvUfiNchdD_h_sP9D4jXYfRl2_kIdxPdg909-HaE7rfdT9zPuP4D8TqE7ifcf7j_AfE6jL7sPx_hbqJ7sLsH347Q_cz9hPsJ9x-gd9jWYXQ_8vKBu4nuwWYnvD_w6xC6H7mfcP_h-g_E6xC6H7mfcP_h_gfE6zD6sp98hLuJ7sHuHnw7Qvcz9xPuJ9x_gN5hW4fR_cj9hPsPnX0Qr8Poy37xEe4mugebnTbmq0fovtvdz9xPuJ9w_QfidQjdT7j_cP8D4nUYfdmvPsLdRPdgkxNeBuB3GN2P3E-4_gfxOoTuJ9x_uP8B8TqMvuxvH+FungKDuwffjtD9ufsJ9xPuP0DvsK3D6H7k5QN3E8DvsK3Ddhft5G-htoPKDgorKCx8i_bP5v0H3cO6wQ)

*API 수집 → 정규화 → 청킹/벡터 적재(Chroma) → 벡터 검색(+Rerank) → LLM 생성*

---

# 5. 데이터 파이프라인

1.  **Ingestion**: 법제처 API 수집 (JSON)
2.  **Normalization**: `Law` / `Article` 정규화
3.  **Indexing**:
    *   **Chunking**: 조문/판례 단위 텍스트 분할
    *   **Embedding**: Chroma Vector DB 적재
    *   **Metadata**: 법령명/날짜 필터링 메타데이터

---

# 6. 검색 전략 (Vector RAG)

**"고정밀 벡터 검색과 Reranking의 조화"**

1.  **Step A: Vector Retrieval**
    *   사용자 질문 임베딩 생성
    *   Top-K 유사 문서 검색 (with Metadata Filtering)

2.  **Step B: Reranking**
    *   검색된 후보군 + 활용자 질문 → 정밀 재채점
    *   최상위 연관성 조문 선별

---

# 7. 데이터 모델 (Schema)

*   **Document Chunk**
    *   `content`: 법령 조문 본문, 판례 요지
    *   `metadata`:
        *   `type`: 'article' | 'case'
        *   `law_name`: 법령명
        *   `article_no`: 조문 번호
        *   `date`: 시행일/선고일

---

# 8. 성공 지표 (Metrics)

*   **검색 성능**
    *   🎯 **Hit@K**: 정답 조문이 Top-5 안에 있는가?
    *   🥇 **MRR**: 정답이 얼마나 상위에 랭크되는가?

*   **답변 품질**
    *   📢 **Citation Rate**: 인용이 포함된 문장 비율
    *   ✅ **Groundedness**: 답변의 근거가 검색 결과에 있는가?

---

# 9. 로드맵

1.  **Phase 1 (MVP)** - *Current*
    *   ETL & Vector Indexing 파이프라인
    *   검색(Retrieval) + 재순위화(Rerank) 구현
    *   Streamlit 데모 완성

2.  **Phase 2 (Extension)**
    *   키워드 검색 (BM25) 하이브리드 확장
    *   심화 평가 (Ragas) 및 UI 고도화

---

# 감사합니다

**질문 있으신가요?**

*GitHub: https://github.com/hyeonz673/legal-graph-rag*

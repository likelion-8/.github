# KDIC RAG Workflow

예금보험공사(KDIC) 웹 지식 기반 RAG 평가·운영 파이프라인

## 개요

KDIC 공식 웹사이트(www.kdic.or.kr, fins.kdic.or.kr)의 FAQ·안내문·표·첨부파일을 지식 소스로 활용하여, 사용자 질의에 근거 출처를 명시한 답변을 생성하는 RAG 시스템입니다.

## 파이프라인 구성

### 1. 데이터 수집 · 인덱싱
```
KDIC Web Sources → 크롤링/변경 감지 → 본문·표·링크 파싱 
→ 메타데이터 태깅 → Corpus 생성 → 청킹 전략 → Corpus/Chunks
```
- **청킹 전략**: FAQ는 Atomic 단위, 표는 Header+3행 Row 단위, 원문은 페이지 단위로 분리
- **저장소**: PostgreSQL(메타데이터·실험 이력), BM25 인덱스, Qdrant 벡터 DB(BGE-M3 임베딩)

### 2. 질의 응답
```
User Query → 질문 전처리 → 질문 유형 라우팅(실험) 
→ 검색(BM25 / Dense / Hybrid) → Top-k 검색 결과 → Reranker(선택)
→ Prompt Builder(출처·브랜드톤·첨부 링크 포함) → LLM 답변 생성
→ Safety Check(선택) → 최종 답변
```
- 최종 답변에는 근거 출처, URL/Breadcrumb, 거절 정책 및 안전성 정보 포함

### 3. 관리자 대시보드
문서 관리 · 청크 확인 · 검색 테스트 · 평가 결과 · 실험 비교

### 4. 평가 · LLMOps
```
평가셋 → 검색 평가 → 답변 평가 → 운영 지표 → 버전 관리/실험 추적 → 재수집/재색인
```
- **평가셋**: 856 questions / Dev 405
- **검색 평가**: Recall@k, MRR, Hit Rate
- **답변 평가**: Faithfulness, Hallucination, Strict Pass Rate
- **운영 지표**: Latency, Token, API 안정성, Cost
- **버전 관리**: Git 기반 실험 Run 비교
- **재수집/재색인**: 홈페이지 변경 사항 반영

## 기술 스택

| 영역 | 기술 |
|---|---|
| 벡터 검색 | Qdrant (BGE-M3 Embedding) |
| 키워드 검색 | BM25 |
| 메타데이터 저장 | PostgreSQL |
| 실험 관리 | Git 기반 버전 관리 |

<img width="1672" height="941" alt="kdic_workflow" src="https://github.com/user-attachments/assets/7350ba93-aa6c-4e45-b82c-58bee132bb13" />


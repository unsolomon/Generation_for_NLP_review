# 🧩 8강. Embedding

## 1️⃣ 임베딩의 개념

### 1.1 임베딩이란?
임베딩(Embedding)은 **이산적(discrete)** 데이터를 **연속적(continuous)** 벡터 공간으로 사영(embedding)하는 과정입니다.  
즉, 토큰화된 문장(정수 ID로 표현된 벡터)을 의미를 보존한 실수 벡터로 변환합니다.

- 이산 벡터 → 연속 벡터 변환
- 의미론적 정보(Semantic Information)를 인코딩
- 텍스트, 이미지, 오디오 등 다양한 형태의 데이터를 수치 벡터로 표현

> 예시:  
> "I like natural language processing" → [0.12, 0.69, 0.32, 0.86, 0.31, …]

---

### 1.2 임베딩이 필요한 이유
- 이산적인 텍스트를 **의미 있는 벡터 공간**으로 표현하기 위해
- **유사한 의미의 문장끼리 가깝게 위치**하도록 하기 위해  
- 예: “고양이”와 “강아지”는 벡터 공간상 가까운 위치에 매핑

> 🔸 초기에는 단어 수준(Word-level) 유사도를 학습 (Word2Vec)  
> 🔸 현재는 문장(Sentence-level)·문서(Document-level)로 확장

---

### 1.3 임베딩의 형태
| 형태 | 설명 |
|------|------|
| **Dense Representation** | 모든 차원에 실수값을 갖는 밀집 벡터 |
| **Low-Dimensional** | 계산 효율을 위해 차원을 축소 |
| **Semantic Preservation** | 의미가 비슷한 데이터는 가까운 벡터 위치 |

---

### 1.4 임베딩 적용 예시
학습된 임베딩 모델로 문장을 벡터화하면,  
유사한 문장들은 벡터 공간에서 가까운 위치에 존재합니다.

> 예시 시각화: [Semantle-Ko](https://semantle-ko.newsjel.ly/)  
> 비슷한 의미의 문장이 클러스터링되어 나타남.

---

## 2️⃣ 임베딩의 중요성

### 2.1 주요 활용 분야

| 분야 | 설명 |
|------|------|
| **IR (Information Retrieval)** | 대규모 문서에서 의미 기반 검색 수행 |
| **QA (Question Answering)** | 질문과 답변 간 의미적 연관성 계산 |
| **STS (Semantic Textual Similarity)** | 두 문장의 의미 유사도 평가 |
| **RAG (Retrieval-Augmented Generation)** | 외부 지식을 검색해 LLM의 생성 품질 향상 |

---

### 2.2 임베딩 성능의 중요성

#### ✅ 성능이 좋을 때
```
User Question: AI 기술을 활용한 단백질 구조 예측 방법은?
Similarity Search: AI 기술은 AlphaFold 모델을 통해 단백질 구조 예측에 혁신을 보임.
LLM Response: AI 기반 AlphaFold 모델은 단백질 구조 예측에서 큰 진전을 이루었습니다.
```

#### ❌ 성능이 나쁠 때
```
User Question: AI 기술을 활용한 단백질 구조 예측 방법은?
Similarity Search: AI는 자율주행차와 같은 기술에서 사용됩니다.
LLM Response: AI는 자율주행차, 이미지 인식 등에서 중요한 기술입니다.
```

> → 임베딩 품질은 **검색 정확도, 응답의 신뢰도(Hallucination 감소)** 에 직결됩니다.

---

## 3️⃣ 임베딩 모델 학습 방법

### 3.1 Word2Vec — 초기 임베딩 모델
단어 수준 임베딩 학습 모델로 CBOW와 Skip-gram 구조 사용.

| 모델 | 방식 | 특징 |
|------|------|------|
| **CBOW** | 주변 단어 → 중심 단어 예측 | 문맥 포착에 강함 |
| **Skip-gram** | 중심 단어 → 주변 단어 예측 | 희귀 단어 학습에 강함 |

> 단점: 문맥 정보가 부족하고, 다의어 표현이 어렵다.

---

### 3.2 Sentence-BERT (Bi-Encoder)
- BERT를 기반으로 문장 수준 임베딩을 생성
- **Siamese Network 구조**로 두 문장의 유사도를 코사인 거리로 계산
- **NLI 데이터로 사전학습**, **STS 데이터로 미세조정**

> 장점: 빠른 검색 및 클러스터링  
> 단점: Cross-Encoder에 비해 정확도가 낮음

---

### 3.3 Contrastive Learning (+ Self-Supervised Learning)
- **레이블 없이** 유사도 학습을 수행하는 방법
- **유사한 데이터 → 가까이**, **다른 데이터 → 멀리**

**학습 절차**
1. 데이터 증강으로 Positive Pair 생성  
2. 다른 샘플로 Negative Pair 생성  
3. 대조 손실 함수 (InfoNCE, NT-Xent 등) 사용  

> 대표 모델: SimCLR, MoCo, CLIP  
> → 대규모 데이터에서도 강건한 표현 학습 가능

---

## 4️⃣ 최신 임베딩

### 4.1 많이 활용되는 모델
주요 모델: **E5, BGE, JinaEmbedding**

| 특징 | 설명 |
|------|------|
| 입력 길이 증가 | 긴 문장·문서 단위 임베딩 가능 |
| 대규모 데이터 기반 Contrastive Learning | 풍부한 의미 정보 학습 |
| 고품질 Negative Sampling Fine-tuning | 세밀한 의미 구분 가능 |

**SentenceTransformer 사용 예시**
```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer('all-MiniLM-L6-v2')
sentences = ['This is an example sentence', 'Each sentence is converted']
embeddings = model.encode(sentences)
print(embeddings)
```

---

### 4.2 임베딩 모델 벤치마크
- 대표 데이터셋: **MTEB**, **BEIR**
- 평가 방식: 유사도, 검색, 분류, 클러스터링 등 다양한 Task 수행

**MTEB 사용 예시**
```python
from mteb import MTEB
from sentence_transformers import SentenceTransformer

model = SentenceTransformer("all-MiniLM-L6-v2")
evaluation = MTEB(tasks=["Classification"])
results = evaluation.run(model, output_folder="results")
```

> 📊 [MTEB Leaderboard](https://huggingface.co/spaces/mteb/leaderboard)

---

### 4.3 LLM 기반 임베딩 모델

#### ⚙️ Encoder 기반의 한계
- 데이터셋 수집이 복잡 (Manual Curation)
- 컨텍스트 길이 제한 (ex. BERT: 512 tokens)

#### 🚀 Decoder 기반 모델의 강점
- 오픈소스 LLM 활용 → 일반 정보 처리에 강함
- LLM을 임베딩 모델로 직접 튜닝 가능
- 자동 데이터 수집 (OpenAI API 등으로 180M+ 토큰 생성)

#### 학습 방식
1. 문장 끝에 **EOS 토큰 삽입**
2. **EOS Hidden States**를 임베딩으로 사용
3. Contrastive Learning으로 최적화

> 대부분의 상위권 임베딩 모델은 **Decoder-Only LLM 기반**

---

### 4.4 한국어 임베딩 성능
- **allganize/RAG-Evaluation-Dataset-KO**를 기반으로  
  Marker에서 **한국어 벤치마크 데이터셋** 제작  
  🔗 [AutoRAG Example Korean Benchmark](https://github.com/Marker-Inc-Korea/AutoRAG-example-korean-embedding-benchmark)

---

## 5️⃣ 임베딩 전후 비교

### 5.1 RAG 성능 비교
- 임베딩 성능에 따라 **RAG의 Hallucination, 신뢰도(Trustworthiness)** 가 크게 달라짐.
- 고품질 임베딩은 **정확한 문맥 검색과 안정적인 응답 생성**을 가능하게 함.

---

## 📚 Reference
- Efficient Estimation of Word Representations in Vector Space (Word2Vec)  
- Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks  
- SimCSE: Simple Contrastive Learning of Sentence Embeddings  
- MTEB: Massive Text Embedding Benchmark  
- BEIR: Zero-shot Evaluation of IR Models  
- Improving Text Embeddings with LLMs  
- [Jina Embeddings v2](https://huggingface.co/jinaai/jina-embeddings-v2-base-code)  
- [Intfloat Multilingual E5](https://huggingface.co/intfloat/multilingual-e5-large)

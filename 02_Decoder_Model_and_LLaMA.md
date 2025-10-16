# 02_Decoder-Model_and_LLaMA.md

---

## 🧩 1. Decoder-only Model Review and Intro to LLaMA

Decoder-only 모델은 **Auto-Regressive 구조**를 통해 이전 토큰들을 기반으로  
다음 토큰을 순차적으로 생성하는 방식입니다.  
대표적인 예시는 **GPT 시리즈**이며, 생성형 언어모델의 기반이 됩니다.

### 🔹 Decoder-only 모델의 3대 핵심 메커니즘

#### 1️⃣ Auto-Regressive (자기회귀)
- **의미:** 이전에 생성된 토큰들을 기반으로 다음 토큰을 순차적으로 예측  
- **장점:** 문맥 일관성과 자연스러운 문장 생성에 유리  
- **예시:** 문장을 완성하거나 스토리를 이어가는 생성 작업에 효과적  

#### 2️⃣ Self-Attention (자기 주의)
- **의미:** 입력 시퀀스 내 모든 토큰 간의 관계를 고려  
- **작동:** 각 토큰이 다른 모든 토큰과의 관련성을 계산하여 중요도를 파악  
- **장점:** 긴 거리의 의존성(Long-range dependency)을 포착 가능 → 문맥 이해력 향상  

#### 3️⃣ Causal Masking (인과적 마스킹)
- **의미:** 현재 토큰이 미래 토큰 정보를 참조하지 못하도록 제한  
- **작동:** 자기 주의 연산 시, 현재 이후 토큰의 attention 점수를 마스킹(0으로 설정)  
- **목적:** 미래 정보를 미리 알지 못하게 하여 자연스러운 텍스트 생성 유도  

---

## 🦙 2. LLaMA 모델 소개

### 📖 LLaMA란?
- **Meta AI**에서 공개한 **오픈소스 LLM 시리즈**  
- **Auto-Regressive Decoder-only 구조** 기반  
- **LLaMA 1 → 2 → 3 → 3.1 → 3.2**로 발전  
- 최신 버전(LLaMA 3.2)은 **텍스트 + 비전 멀티모달 모델**

### 🔹 LLaMA 모델의 처리 순서

1. **Input Token Embedding**  
   → 단어를 벡터로 변환 (입력 임베딩)  
2. **RMS Normalization**  
   → 입력을 정규화하여 안정적인 학습 유도  
3. **Rotary Positional Embedding (RoPE)**  
   → Q, K에 위치정보를 회전(rotation) 방식으로 주입  
4. **Multi-Head Attention (FlashAttention2)**  
   → 병렬 self-attention으로 관계 파악 및 효율 향상  
5. **RMS Normalization (2nd)**  
6. **Feed-Forward Network (SwiGLU 활성화 함수)**  
7. **출력층: Linear + Softmax**  
   → 다음 토큰 확률 분포 예측

---

## ⚙️ 3. LLaMA의 주요 구성 요소 (Key Components)

| 구성 요소 | 설명 | 주요 역할 |
|------------|------|------------|
| **RoPE (Rotary Positional Embedding)** | 각 토큰의 위치 정보를 회전 형태로 인코딩 | 상대적 위치 정보 보존 |
| **Multi-Head Attention** | 여러 관점(head)에서 관계 파악 | 문맥적 연결 강화 |
| **Feed-Forward Network (FFN)** | 비선형 변환을 통해 특징 확장 | 표현력 향상 |
| **RMS Normalization** | 평균 대신 제곱평균으로 정규화 | 계산 효율성 및 안정성 |
| **FlashAttention2** | GPU 최적화된 어텐션 계산 | 메모리 절감 및 속도 향상 |
| **SwiGLU Activation** | ReLU 개선형 활성화 함수 | 비선형 학습 성능 향상 |

---

## 🧠 4. Code-Level Analysis of LLaMA

> HuggingFace Transformers 내 `modeling_llama.py` 기준으로 구성요소별 작동 방식 정리

---

### 🔸 4.1 Rotary Positional Embedding (RoPE)

**개념:**  
토큰의 위치 정보를 “회전(rotation)”으로 표현하여 상대적 위치를 자연스럽게 반영.

**작동 원리:**  
1. 각 토큰의 위치에 따라 회전 각도(θ)를 계산  
2. 임베딩 벡터의 절반을 cos(θ), 나머지를 sin(θ)로 회전  
3. Query(Key)에 RoPE를 적용 → Attention 시 위치정보 반영

**비유:**  
> 시계 바늘이 시간에 따라 회전하듯, 토큰도 문장 내 위치에 따라 회전 각도가 달라짐.  
> 두 토큰의 각도 차이가 바로 “거리 정보”를 의미함.

---

### 🔸 4.2 Multi-Headed Attention

**개념:**  
입력 데이터를 여러 “머리(head)”로 나누어 병렬적으로 다양한 관계를 학습.

**작동 순서:**  
1. Query, Key, Value 행렬 생성  
2. RoPE를 Query와 Key에 적용  
3. Attention Score 계산 (Q × K^T / √d_k)  
4. Softmax 후 Value와 곱해 최종 출력 생성  
5. 여러 Head 결과를 결합해 풍부한 표현력 확보

**비유:**  
> 여러 전문가가 각각의 시점에서 문장을 읽고,  
> 그들의 의견을 종합해 더 깊이 있는 해석을 만드는 과정과 유사.

---

### 🔸 4.3 Feed-Forward Network (FFN / MLP)

**구조:**  
- 완전연결층 2개  
- 비선형 활성화 함수 (SwiGLU) 사용  
- 입력을 더 큰 차원으로 확장 → 다시 축소 (Gate 구조)

**기능:**  
- Attention이 학습한 관계를 해석하고,  
- 더 복잡한 비선형 패턴을 학습

---

### 🔸 4.4 RMS Normalization

**정의:**  
- 입력을 평균 대신 **Root Mean Square** 값으로 정규화  
- LayerNorm의 간소화 버전  
- 계산량 ↓, 안정성 ↑

**수식:**  
\[
RMSNorm(x) = \frac{x}{\sqrt{mean(x^2)}}
\]

**특징:**
- 평균 뺄셈 과정 생략 → 더 빠름  
- 배치 크기에 덜 민감  
- T5 모델의 LayerNorm과 거의 동일  

---

### 🔸 4.5 Flash Attention 2

**목적:**  
- 메모리 사용량 절감  
- 긴 시퀀스 처리 속도 향상

**핵심 기술:**
- **Tiling:** 큰 행렬을 블록 단위로 나눠 GPU 고속 메모리(SRAM)에서 계산  
- **Recomputation:** 중간결과 저장 대신 필요 시 재계산  

**FlashAttention2 개선점:**
- Q를 분할하고 K, V를 공유 → 동기화 감소, 병렬성 향상  
- 기존 대비 **2~3배 빠른 연산 속도**

**LLaMA 내 구현:**  
- `LlamaAttention` 클래스 상속  
- FlashAttention API로 query, key, value 계산  
- RoPE 적용 후 attention 결과 반환  

---

## 🧾 Reference Links

- [LLaMA Architecture Overview](https://devopedia.org/llama-llm)  
- [Rotary Embedding (RoFormer)](https://arxiv.org/pdf/2104.09864)  
- [Attention is All You Need](https://arxiv.org/abs/1706.03762)  
- [RMS Normalization (Zhang, 2019)](https://arxiv.org/abs/1910.07467)  
- [Flash Attention 2 (Dao, 2023)](https://arxiv.org/pdf/2307.08691)  
- [HuggingFace LLaMA Source Code](https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/modeling_llama.py)

---

**ⓒ NAVER Connect Foundation | Boostcamp AI Tech NLP Recent Trends**

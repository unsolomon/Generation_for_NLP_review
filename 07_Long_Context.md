# 🧠 Long Context: Need and Challenge

## 1.1 Need — 장문 맥락 LLM의 필요성

### 🔹 긴 문맥 이해 능력에 대한 수요 증가
- LLM의 전반적인 성능이 향상됨에 따라 **긴 문맥(Long Context)** 을 이해할 수 있는 모델의 필요성이 급증하고 있음.
- 장문 맥락을 이해하는 LLM은 기존의 **검색 증강 생성(Retrieval-Augmented Generation, RAG)** 방식을 대체할 가능성을 보임.

### 🔹 맥락 확장 경쟁 (Context Extension War)
| 시점 | 모델 | 최대 Context Window |
|------|-------|--------------------|
| 2023.10 | Claude | 100K tokens |
| 2023.11.6 | GPT-4 Turbo | 128K tokens |
| 2023.11.21 | Claude 2.1 | 200K tokens |
| 2024.2.15 | Gemini 1 | 1M tokens |
| 2024.5 | Gemini 1.5 Pro | 2M tokens |

- 주요 LLM 기업들이 **맥락 길이(Context Length)** 확장 경쟁에 돌입함.
- 이는 “더 긴 문맥을 처리할수록 모델이 더 똑똑해질 것”이라는 기대를 반영함.

---

## 1.2 Challenge — 장문 맥락 입력의 한계

### ⚙️ 일반적인 문제점
- 기존 모델은 **학습 시 사용된 데이터보다 긴 입력(Longer-than-training input)** 에서 성능이 저하됨.
- **연산 복잡도(Computation Complexity)** 증가 → 추론 속도 저하 및 **메모리 사용량 폭증**.
- 맥락 길이가 길어질수록 **효율성(Efficiency)** 과 **정확도(Accuracy)** 사이에 트레이드오프 발생.

### 🧩 “Lost in the Middle” 문제
- 관련 정보가 문맥의 **중간 부분**에 위치할 경우, 모델이 이를 잘 인식하지 못하고 처음이나 끝의 정보에 더 의존함.
- Liu et al. (TACL 2023) *“Lost in the Middle”* 연구에서, 모델이 중간 정보에 대한 주의(attention)를 제대로 유지하지 못함을 실험적으로 확인.

### ⚖️ 품질 대 길이(Quality vs Length)의 트레이드오프
- 많은 모델들이 형식적으로는 긴 맥락을 지원하지만, 실제로 **효과적으로 활용 가능한 길이**는 훨씬 짧음.
- 즉, “길게 볼 수는 있지만, 깊게 이해하지는 못한다”는 한계 존재.

---

# 🚀 Towards Long Context Models

## 2.0 위치 임베딩(Position Embedding)

### 🔸 절대 위치 임베딩 (Absolute Position Embedding)
- Transformer가 문장 내 각 토큰의 **절대적 위치 정보**를 인식하도록 설계.
- 단점: **상대적 거리 정보(예: 1↔2 vs 1↔10)** 를 반영하지 못함.  
  → 문맥 구조에 대한 유연한 이해가 어려움.

### 🔸 상대 위치 임베딩 (Relative Position Embedding)
- 아이디어: Attention score가 두 토큰 간의 **상대적 거리(m−n)** 에 의존해야 함.
- 단점: 거리 계산을 위해 **추가 메모리와 연산**이 필요하며, 긴 문맥일수록 속도가 느려짐.

### 🔸 회전 위치 임베딩 (Rotary Position Embedding, RoPE)
- 절대 위치와 상대 위치 임베딩의 장점을 결합한 방식.
- 방법: 각 토큰의 임베딩 벡터를 위치 인덱스만큼 회전시켜, **코사인/사인 회전**을 통해 상대적 위치 관계를 보존.
- 장점: Transformer가 **상대적 위치 정보**를 효율적으로 학습할 수 있음.
- 적용: GPT-NeoX, LLaMA 등에서 사용됨.

---

## 2.1 위치 보간법 (Position Interpolation)
- 목적: 모델을 처음부터 재학습하지 않고도 **맥락 창(context window)** 을 확장.
- 방법: RoPE의 위치 인덱스를 **보간(interpolation)** 하여 스케일 조정.
- 예시: LLaMA 모델을 1000 step 미세 조정(fine-tuning)하여 맥락 창을  
  **2048 → 32768 tokens** 으로 확장 (Chen et al., 2023)

---

## 2.2 선형 편향 어텐션 (Attention with Linear Biases, ALiBi)
- 동기: 기존 위치 임베딩 방식은 토큰 수가 증가할수록 **외삽(extrapolation)** 이 어려움.
- 아이디어: 위치 임베딩을 추가하지 않고,  
  **쿼리-키(query-key) 거리**에 비례하는 **페널티(bias)** 를 어텐션 점수에 부여.
- 효과: 모델이 “짧은 길이로 학습하고 긴 길이로 추론(Train Short, Test Long)” 가능.
- 적용 사례: MPT, BLOOM, Replit 모델에서 장문 맥락 학습에 활용됨.

---

## 2.3 링 어텐션 (Ring Attention)
- 동기: 장문 맥락 입력에서 **막대한 메모리 사용량** 감소 목표.
- 방법:
  1. Transformer를 **여러 블록(block)** 으로 나눔  
  2. Key-Value 쌍을 블록 간에 전달하며 순차적으로 Attention 계산
- 구조:
  - 각 장치(device)가 Query 블록을 보유
  - Key-Value 블록은 장치 간 “링(ring)” 형태로 순환하며 Attention 수행
- 장점:
  - GPU/TPU 등 여러 장치를 사용할 때 **거의 무한대 수준의 맥락 확장 가능**
  - 메모리 효율성 및 병렬화 측면에서 뛰어남
- 출처: *Liu et al., 2023. “Ring Attention with Blockwise Transformers for Near-Infinite Context.”*

---

# 📚 Reference
- Liu et al., *Lost in the Middle: How Language Models Use Long Contexts*, TACL 2023  
- gkamradt, *Needle In A Haystack Benchmark*  
- AI21 Labs, *Jamba 1.5: Long Context Model Family*  
- Chen et al., *Extending Context Window via Position Interpolation*, arXiv 2023  
- Press et al., *Train Short, Test Long: ALiBi*, ICLR 2022  
- Liu et al., *Ring Attention with Blockwise Transformers for Near-Infinite Context*, arXiv 2023

---

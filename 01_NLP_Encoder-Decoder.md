# 01_Encoder-Decoder.md

---

## 1️⃣ Encoder-Decoder / Encoder-only 모델

### 1.1 모델의 발전 흐름

| 연도 | 주요 모델 | 특징 |
|------|------------|------|
| 2017 | Transformer | Attention 기반 구조의 등장 |
| 2018 | BERT | Encoder 중심의 이해 모델 |
| 2019 | GPT-2 | 생성형 Decoder 모델의 시작 |
| 2020 | GPT-3 | 대규모 Pretrained LM 시대 |
| 2022~ | ChatGPT, GPT-4/4o | LLM 기반 생성형 AI 시대 개막 |

- Encoder 모델은 **NLU(Natural Language Understanding)** 발전을 주도  
- Decoder 모델은 **Generation 중심의 AI 모델 시대**를 열었음  

---

## 1.2 Encoder-Decoder 모델

### 대표 모델
- **Transformer, BART, T5 등**

### 구조 및 작동 방식
- Encoder에서 입력 정보를 **해석**
- Decoder에서 Cross-Attention을 통해 Encoder 정보 기반으로 **출력 생성**
- 입력의 의미를 압축하고, 이를 이용해 자연스러운 생성 수행

### 활용 분야
- **기계 번역 / 요약 / Generative QA**
- 생성 Task 수행 시 Decoder-only 모델의 부족한 부분 보완

### 한계
- **효율성 문제**: Self-Attention + Cross-Attention으로 인한 계산 병목  
- **확장성 한계**: In-Context Learning 최적화에 추가 비용 발생

---

## 1.3 Encoder-only 모델

### 대표 모델
- **BERT (Masked Language Model)**  
  → 문장 내 [MASK] 토큰을 예측하여 문맥 이해 능력 강화

### 특징
- 문맥 이해 및 분류(Classification) Task에 강점  
- 문장 내 관계 이해에 탁월

### 활용 분야
- 문서 분류(감정 분석, 스팸 필터링)
- 정보 검색(IR, Embedding)
- Extractive QA (지문 내에서 정답 추출)

### 한계
- Sequential Task (생성형)에 약함  
  → Auto-Regressive 구조가 아님  
  → **생성 Task 수행 불가**

---

## 2️⃣ Decoder-only 모델

### 2.1 GPT (Causal Language Model)
- 이전 토큰과 문맥을 바탕으로 **다음 토큰(Next Token)** 예측  
- 자연스러운 문장 생성에 탁월  
- **생성 Task** 중심의 모델 구조

### 2.2 발전
- **Emergent Abilities**:  
  모델 크기와 학습량(Training FLOPs)이 일정 수준을 넘으면  
  **Few-Shot Learning, Reasoning** 등 새로운 능력이 자발적으로 등장

### 2.3 성능
- Generation Task에서 매우 뛰어남  
- NLU Task에서도 충분히 높은 성능  
- **MMLU Benchmark**: 상위 모델 대부분이 Decoder-only 구조 사용

---

## 3️⃣ LLM 시대

### 3.1 LLM(Decoder-only)의 장점 및 성능

**장점**
- Encoder-Decoder보다 효율적 (학습/추론 속도 빠름)
- Scaling Up에 유리
- In-Context Learning을 통해 입력 기반 텍스트 생성 능력 강화

**성능**
- 대부분의 생성 및 이해 Task를 수행 가능  
- 실제 서비스에서도 다양한 보조 역할 수행 (예: ChatGPT)

---

### 3.2 In-Context Learning (ICL)

- **Few-shot Learning**: 프롬프트에 소수 예시만으로도 작업 수행
- **Chain-of-Thought (CoT)**: 복잡한 추론 과정을 단계적으로 표현
- Standard Prompting보다 높은 성능
- 모델 크기가 커질수록 성능 향상 폭 증가

**예시 (감정 분석 Prompting)**  



---

### 3.3 Human Alignment

**Instruction Tuning**
- 다양한 지시사항(instruction)을 포함한 데이터로 모델을 미세조정  
- 모델이 일반적인 명령을 이해하고 따르도록 학습  
- 예시: FLAN, T5

**RLHF (Reinforcement Learning with Human Feedback)**
- 인간 피드백을 통해 모델 응답의 품질을 개선  
- 과정:
  1. 초기 응답 생성  
  2. 인간의 선호도 평가  
  3. 보상 모델 학습  
  4. 정책 최적화
- ChatGPT가 이 방식으로 발전

---

### 3.4 LLM의 성능
- Pretraining + Instruction Tuning + RLHF를 통해  
  고품질의 자연어 이해 및 생성 능력을 달성  
- Chatbot Arena 등에서 모델 간 성능 비교 가능  
  👉 [https://chat.lmsys.org/](https://chat.lmsys.org)

---


## 📚 참고 논문 및 링크

- [BART](https://arxiv.org/pdf/1910.13461)  
- [Emergent Abilities of Large Language Models](https://arxiv.org/pdf/2206.07682)  
- [Language Models are Few-Shot Learners](https://arxiv.org/pdf/2005.14165)  
- [Multi-task Language Understanding on MMLU](https://paperswithcode.com/sota/multi-task-language-understanding-on-mmlu)  
- [Chain-of-Thought Prompting](https://arxiv.org/pdf/2201.11903)  
- [Instruction Tuning](https://arxiv.org/pdf/2109.01652)  
- [RLHF (Direct Preference Optimization)](https://arxiv.org/pdf/2305.18290)

---

**ⓒ NAVER Connect Foundation | Boostcamp AI Tech NLP Recent Trends**

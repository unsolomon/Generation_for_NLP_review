
# ✅ 요약

| 구분 | 주요 특징 | 장점 | 한계 |
|------|------------|------|------|
| **정량 평가** | 자동화된 수치 지표 활용 | 객관적, 대규모 비교 가능 | 문맥/자연스러움 반영 어려움 |
| **정성 평가** | 사람의 직관적 판단 | 뉘앙스·윤리 평가 가능 | 비용 높음, 일관성 낮음 |
| **LLM 기반 평가** | LLM이 직접 평가자 역할 | 효율적, 반복 가능 | Bias 존재, 수학·추론 약점 |
| **LLM 벤치마크** | 표준화된 성능 비교 지표 | 재현성 높음, 글로벌 비교 용이 | 실제 사용 시나리오 반영 어려움 |

---



# 1. 정량 평가 (Quantitative Evaluation)

정량 평가는 **객관적인 수치 지표(metric)** 를 활용하여 모델의 출력을 평가하는 방식입니다. 주로 자동화된 측정 기준을 사용하며, **대규모 테스트 시 효율적**입니다.

## 1.1 주요 평가 지표 및 벤치마크

| Task | Common Metric / Benchmark | 설명 |
|------|---------------------------|------|
| Translation | BLEU, ROUGE | n-gram 일치 비율을 기반으로 번역 품질 평가 |
| QA (Question Answering) | F1 Score, Exact Match (EM) | 정답과 생성 텍스트 간의 단어 일치도 및 정확 일치 평가 |
| Bias | Fairness Benchmarks | 성별, 인종, 연령 등 사회적 편향 정도 평가 |
| Hallucination | Faithfulness Tests (HaluEval, FaithDial), Self-Consistency | 생성 결과의 사실성 및 일관성 평가 |
| Math | GSM8K, Logical Consistency | 수학 문제 해결력과 논리적 일관성 평가 |

## 1.2 예시

- **BLEU/ROUGE**  
  - 정답: “The cat is on the mat.”  
  - 예측: “The cat is on a mat.”  
  → n-gram 일치가 높아 높은 점수 획득

- **F1 / Exact Match**  
  - 정답: “Paris”  
  - 예측: “Lyon”  
  → F1: 약 0.8점, EM: 0점

## 1.3 장단점

- **장점:** 자동화 가능, 객관적 수치 제공, 대규모 비교 용이  
- **단점:** 문맥, 논리, 자연스러움 등 정성적 측면 반영 어려움  

---

# 2. 정성 평가 (Qualitative Evaluation)

정성 평가는 **사람이 직접 모델의 응답을 평가하는 방식**으로, 자연스러움·논리성·윤리성 등 **정량화하기 어려운 부분**을 파악할 수 있습니다.

## 2.1 평가 관점

- 정확성(Accuracy)  
- 유창성(Fluency)  
- 일관성(Coherence)  
- 관련성(Relevance)  
- 자연스러움(Naturalness)  
- 사실성(Factuality)  
- 할루시네이션(Hallucination) 검출

## 2.2 평가 방식

- 평가자가 가이드라인에 따라 각 응답에 점수를 부여 (예: 1~5점 척도)
- A/B 테스트 형태로 두 모델 응답 중 더 나은 결과 선택
- Human Alignment 기반 선호도 평가 (Chatbot Arena 등)

## 2.3 장단점

- **장점:**  
  - 뉘앙스, 문체, 윤리성 등 인간적 요소 반영 가능  
  - 전문 영역(법률, 의료, 금융 등)에 대한 의미 있는 평가 가능  
- **단점:**  
  - 평가 비용이 높고, 평가자 간 일관성 확보 어려움  
  - 대규모 평가에는 부적합

---

# 3. LLM 기반 평가 (LLM-as-a-Judge)

최근에는 LLM이 직접 평가자 역할을 수행하는 **LLM-as-a-judge** 방식이 활발히 사용됩니다.

## 3.1 모델 기반 평가

### a) 임베딩 기반 (Embedding-based)
- **BERTScore**: BERT 등 인코더 모델을 사용해 문장 간 의미적 유사도(Semantic Similarity)를 계산.  
  → 단어가 달라도 의미가 유사하면 높은 점수 부여.

### b) 확률 기반 (Likelihood-based)
- **GPTScore, FFLM 등**  
  - 모델이 생성한 토큰의 확률(Conditional Probability)을 이용해 품질 평가.  
  - 더 확신 있게 다음 토큰을 예측할수록 더 좋은 텍스트로 간주.

## 3.2 프롬프트 기반 평가 (Prompting LLMs)
- LLM에게 **Task Instruction**, **평가 기준(Criteria)**, **대상 응답(Target)**, **출력 형식(Output Format)** 을 제공하여 직접 평가하도록 함.  
- 예:  
  “다음 두 응답 중 더 정확한 것을 선택하세요.”  
  “응답 A와 B를 1~10점으로 평가하세요.”

## 3.3 파인튜닝된 평가 모델 (Fine-tuned LLMs)
- 비용 문제와 일관성 문제를 해결하기 위해, 오픈소스 모델을 평가 전용으로 Fine-Tuning.  
- 자체 구축한 평가 데이터셋을 통해 신뢰성 있는 스코어링 수행.

## 3.4 Human-LLM 협업 평가 (Human-LLM Collaborative)
- 인간 평가와 LLM 평가를 병행하여 효율성과 신뢰성을 확보.  
- 예시: **COEVAL**, **AdaTest**, **AdaTest++**

## 3.5 주요 편향(Challenges)
1. **Position Bias** – 정답 문서의 위치에 따른 선호 편향  
2. **Verbosity Bias** – 길이가 긴 답변을 선호하는 경향  
3. **Self-enhancement Bias** – 동일한 모델이 생성한 답변을 더 높게 평가  
4. **Limited Grading Capability** – 수학·논리적 추론 문제 채점 한계  

---

# 4. LLM 평가 벤치마크 (LLM Evaluation Benchmarks)

## 4.1 개요
LLM 벤치마크는 **모델의 성능을 표준화된 기준으로 비교하기 위한 평가 프레임워크**입니다.  
일반적으로 샘플 데이터셋, 평가 태스크, 메트릭, 점수 산정 방식으로 구성됩니다.

## 4.2 주요 벤치마크 종류

### ✅ Chatbot Arena
- 두 모델의 응답을 사람이 직접 비교 선택 (A/B 테스트)  
- 승리 점수를 기반으로 순위 계산  
- 인간의 선호도 기반 평가 방식  
🔗 https://chat.lmsys.org

### ✅ WildBench
- 실제 사람의 대화 데이터를 기반으로 구축된 1,024개 샘플  
- WildChat Dataset을 큐레이션해 만든 실제 대화형 벤치마크  
🔗 https://huggingface.co/spaces/allenai/WildBench

### ✅ OpenKo-LLM Leaderboard v2 (한국어)
- 한국어 LLM 평가용 오픈 리더보드  
- KorNAT 등 한국 문화 및 실생활 맥락을 반영한 데이터 추가  
🔗 https://huggingface.co/spaces/upstage/open-ko-llm-leaderboard

### ✅ LC AlpacaEval (Length-Controlled)
- AlpacaEval의 편향(Verbosity Bias)을 보정한 버전  
- Human correlation이 0.98로 매우 높음 (기존 0.94 → 개선)  
🔗 https://tatsu-lab.github.io/alpaca_eval/

## 4.3 평가 시 고려사항

1. **복잡한 질문 처리 한계**
   - 추론 기반 질문 포함 필요
2. **긴 문맥 처리 한계**
   - 핵심 정보 손실 가능성 존재
3. **다중 턴 대화 일관성 문제**
   - 주제 전환이 빠른 대화 포함 필요
4. **평가 편향 완화**
   - A/B 테스트 순서 무작위화 (Order Bias 완화)
   - LC AlpacaEval과 같은 길이 보정 적용 (Length Bias 완화)

---
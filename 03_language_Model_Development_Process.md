# 03_Language-Model-Development-Process.md

## 📘 Lecture 3: Language Model Development Process
> Pre-training → Fine-tuning → Preference tuning(사람 선호도 반영) 등 언어 모델 개발의 전반적 단계와 각 단계에서의 핵심 개념, 실무 고려사항을 정리합니다. (참조: 강의 PDF). :contentReference[oaicite:1]{index=1}

---

## 1. 개요: 전통적 NLP vs. 현대적 NLP 패러다임

- **Traditional NLP (Supervised Training)**  
  - Task별로 레이블 있는 데이터 준비 → 모델을 처음부터 학습(from scratch).  
  - 예: TextCNN, RNN 기반 분류기 등.

- **Modern NLP (Pre-training + Fine-tuning)**  
  - 대규모 비지도(또는 자기지도) 데이터로 **Pre-training** 수행 → 일반적 언어지식 습득  
  - 이후 특정 Task(분류/번역/생성 등)에 맞춰 **Fine-tuning** 또는 Inference-time prompting 적용  
  - 필요시 **Preference tuning(RLHF, DPO 등)** 로 사용자 선호/안전성 개선

---

## 2. Pre-Training (사전 학습)

### 목적
- 레이블 없는 대규모 텍스트로 일반적 언어 패턴과 지식을 학습하게 함.
- 이후 적은 데이터로도 다양한 Downstream Task에 빠르게 적응 가능하게 만드는 기반 단계.

### 주요 방식 (Objective)
- **Encoder-style (BERT 계열)**: Masked Language Modeling (MLM) — 문장 일부 마스킹 후 예측 → 문장 이해에 유리.
- **Decoder-style (GPT / LLM 계열)**: Causal Language Modeling / Next Token Prediction — 앞 토큰 기반으로 다음 토큰 예측 → 텍스트 생성에 유리.

### Scaling Laws (핵심 인사이트)
- 모델 크기·데이터·계산량 간 trade-off 존재 (Kaplan et al., Hoffmann et al.).
- 일반적 경향:
  - 모델이 클수록 동일 성능에 도달하기 위한 데이터/학습 조건이 바뀜.
  - optimal compute budget에 따라 모델 크기와 데이터 양을 조정해야 함.
- 실무: 예산(컴퓨팅) 대비 모델 크기·토큰 수(데이터)를 설계할 때 참고.

### 데이터 품질과 전처리
- **데이터 품질이 성능에 큰 영향**: 중복 제거(deduplication), 노이즈/유해 콘텐츠 필터링, 개인정보 제거, 스팸 필터링 등 필수.
- 데이터 소스: 웹 크롤링, 뉴스, 위키, 포럼, 책, 코드 등 → 도메인/목적에 따라 가중치 조절.
- 전처리 고려사항: 토크나이제이션 정책, 문서 분할(시퀀스 길이), 레이블(없음) 포맷 변환(MLM / Causal).

### Pre-training의 한계
- **Hallucination** (사실 기반 응답의 신뢰도 문제)  
- 도메인·최신성·정확성 측면의 한계 → downstream에서 보정 필요

---

## 3. Fine-Tuning (미세 조정)

### 목적
- Pre-trained 모델이 가진 일반적 지식을 **특정 Task**에 맞게 조정.
- 도메인 적응, 성능 향상, 출력 형식 제어 등에 사용.

### 종류 및 방식
- **Supervised Fine-Tuning (SFT)**  
  - 레이블(정답)이 있는 데이터로 지도학습 진행.  
  - 최근엔 instruction (instruction-input-output) 포맷을 많이 사용 (Alpaca, Self-Instruct 등).
- **Few-shot / In-Context Learning (ICL)**  
  - 파라미터 업데이트 없이 prompt 내 예시로 적응. 간단한 태스크에 유용하지만 복잡한 태스크나 도메인 적응은 한계.
- **Task-specific fine-tuning**  
  - 분류, QA, 요약, 번역 등 목적별 데이터로 추가 학습.

### SFT 데이터 예시 (instruction template)

### 장·단점 요약
- 장점: 특정 Task에서 ICL보다 높은 성능 보장, 도메인 적응 가능.  
- 단점: 라벨 데이터 수집 비용, 오버피팅 위험, 배포·버전 관리 필요.

---

## 4. Preference Tuning (선호도 튜닝) — Human-in-the-loop

### 목적
- 모델 출력을 **사람이 선호하는 방식**으로 조정하여 유용성·안전성 향상.
- 단순 성능(정확도)뿐 아니라 응답의 바람직함(정중함, 사실성, 규범 준수 등)을 반영.

### 대표 방법
1. **RLHF (Reinforcement Learning from Human Feedback)** — 보상 모델(Reward Model) 학습 후 PPO 같은 RL로 정책(모델)을 최적화.  
   - 흐름: SFT → 후보 응답 생성 → 인간이 선호 라벨(순위) 수집 → 보상 모델 학습 → 보상으로 RL 최적화.
2. **DPO (Direct Preference Optimization)** — 보상 모델 없이 선호도 데이터를 직접 최적화하는 방법.
3. **ORPO (Odds Ratio Preference Optimization)** — reference-free 방식의 선호 최적화 연구.

### 데이터(라벨) 수집 방식
- A/B 비교: 두 응답을 비교해서 더 좋은 쪽을 투표하게 함(순위/선호 데이터).
- 평가자 가이드라인이 중요: 일관된 품질의 라벨 확보 필요.
- 규모: 많은 비교 데이터가 좋지만 비용·시간 트레이드오프 존재.

### 효과
- RLHF 적용 모델이 SFT만 적용한 모델보다 인간 중심 지표에서 우수하다는 보고가 다수 존재.
- 다만 잘못된 보상 설계는 undesired behavior를 초래할 수 있으므로 신중한 보상 설계·검증 필요.

---

## 5. 실무 체크리스트 / 팁

1. **데이터**  
   - 수집: 다양한 도메인 확보 → 품질 필터링(중복, 유해, 개인정보)  
   - 레이블링: SFT·Preference용 라벨 가이드라인 표준화

2. **학습 설계**  
   - Scaling law 고려: 예산 대비 모델 크기·토큰 수 설계  
   - Mixed precision, 효율적 optimizer(AdamW), gradient accumulation 등으로 리소스 최적화

3. **평가**  
   - 자동 지표(Perplexity, BLEU, ROUGE, MMLU 등) + 인간 평가(정확성, 유용성, 안전성) 병행  
   - 아래 항목을 평가에 포함: hallucination rate, factuality, toxicity

4. **배포·안전성**  
   - 안전 필터링, content policy, prompt-scoping  
   - 지속적 모니터링과 피드백 루프(사용자 로그/선호 수집)

5. **버전 관리**  
   - 모델 버전, 데이터 버전(데이터셋 해시), 학습 파라미터 기록

---

## 6. 참고 리소스 (강의에 언급된 주요 문헌/페이지)
- Scaling laws: Kaplan et al., Hoffmann et al.  
- GPT-3: "Language Models are Few-Shot Learners" (Brown et al.)  
- RLHF 관련: Ouyang et al., Stiennon et al.  
- Preference Opt.: DPO (Rafailov et al.), ORPO (Hong et al.)  
- LLaMA 관련/실무 가이드: LLaMA 논문 및 관련 깃허브  
- 강의 PDF (참조): Lecture 3 — Language Model Development Process. :contentReference[oaicite:2]{index=2}

---

### 맺음말
- Pre-training은 **범용 언어 지식**을 만들고, Fine-tuning은 **특정 목적/도메인**으로 세밀하게 조정합니다.  
- Preference tuning(RLHF/DPO 등)은 모델을 **사람 중심으로 정렬**하는 단계로, 실서비스의 품질·안전성 확보에 필수적입니다.  
- 각 단계는 데이터 품질, 비용(계산 자원), 라벨링·평가 인프라에 크게 의존하므로 실무 설계 시 균형 있게 고려해야 합니다.


# 1. Fine Tuning Dataset

## Fine-Tuning의 목적과 특징
- Pre-Training이 대규모 데이터로 **일반적인 언어 패턴(general things)** 을 학습했다면, Fine-Tuning은 **특정 작업(Task)** 수행 능력을 학습합니다.  
- Fine-Tuning은 언어모델의 **행동(Behavior)** 을 가르치는 과정으로, 다음과 같은 영역에서 조정이 이루어집니다.
  - Instruction (지시 이해)
  - Toxic Language (유해 언어 제어)
  - Hallucination (환각 현상 완화)
  - Social Stereotypes (사회적 편향 완화)

### Fine-Tuning vs Pre-Training
- **Pre-Training**: 웹 코퍼스에서 CLM(Causal Language Model) 방식으로 문맥 기반의 다음 토큰을 예측하는 학습  
- **Fine-Tuning**: 특정 Task에 대해 입력과 출력 관계를 학습 (예: QA, 요약, 번역 등)
- GPT 같은 모델은 Pre-Training 시 응답 요청을 학습하지 않기 때문에, Fine-Tuning을 통해 이러한 행동을 학습시킵니다.

### Fine-Tuning의 유형
1. **MLM 기반 Fine-Tuning**
   - 회귀/분류 예측과 같은 NLU 작업 중심 (예: BERT)
2. **Instruction Fine-Tuning (ITF)**
   - Input과 Output을 모두 사용하는 방식으로, **Alignment Fine-Tuning** 또는 **Instruction Tuning**이라고도 부름.

---

## Task-Specific Dataset 예시

### MRC (Machine Reading Comprehension)
- **Encoder 모델**
  - 정답의 위치(Span) 예측  
  - 예: `Answer start: 123, end: 126`
- **Decoder 모델**
  - 정답 텍스트를 직접 생성  
  - 예: `"한 달"`
- 인코더는 “정답이 어디 있는지”를, 디코더는 “정답이 무엇인지”를 학습합니다.

---

## Instruction Fine-Tuning의 효과
- 단순히 context를 복사해 붙여넣는 수준에서 벗어나, 질문의 의도에 맞는 **형식적이고 적절한 답변** 생성 가능.
- 예: “A, B, C로 답변하세요.” → 모델이 해당 형식을 학습하여 자연스럽게 대응 가능.

---

## Instruction Fine-Tuning의 유행
- **LLaMA**와 **Self-Instruct** 연구 이후, Instruction Tuning을 위한 데이터셋 생성 시도가 급격히 증가.
- Instruction Fine-Tuning만으로도 강력한 성능을 발휘할 수 있음이 입증됨.

---

# 2. Instruction Dataset

## Self-Instruct
> Self-Instruct: Aligning Language Models with Self-Generated Instructions (ACL 2023)

**목적:** 공개 데이터로부터 Instruction Dataset을 생성하는 프레임워크.

### 생성 단계
1. **Seed Dataset 설정**
   - 사람이 직접 작성한 소량의 고품질 데이터로 시작  
   - Few-shot ICL 형태의 QA 패턴 구성
2. **Instruction Generation**
   - 기존 데이터 기반으로 새로운 지시문 생성
3. **Input-Output Generation**
   - 각 지시문에 대해 Input-Output 쌍 생성
4. **Filtering Pipeline**
   - 중복 및 저품질 데이터 제거 후 남은 데이터로 재학습

---

## Stanford Alpaca

**특징**
- 52,000개의 Instruction-following 데이터셋 공개  
- LLaMA-7B 모델을 Alpaca 데이터셋으로 Fine-Tuning한 모델 공개  
- 전체 비용 약 **$500 미만 (OpenAI text-davinci-003 사용)**  

**데이터 생성 과정**
1. Self-Instruct 데이터 기반으로 Instruction 생성  
2. 각 Instruction에 Input, Output을 생성

---

## KoAlpaca
> 한국어 명령어를 이해하는 오픈소스 언어모델

### v1.0 데이터 생성 과정
1. Alpaca 52k 데이터를 DeepL API로 번역 (Instruction, Input만)
2. GPT-3.5-turbo를 사용하여 Output 생성

### v1.1 데이터 생성 과정
1. 네이버 지식인 베스트 질문 전체 수집  
2. 질문/본문/채택된 답변을 기반으로 ChatGPT를 통해 Instruction, Input, Output 생성  
3. 자연스러운 한국어 QA Pair 구성

**예시**
```json
{
  "instruction": "양파는 어떤 식물 부위인가요? 그리고 고구마는 뿌리인가요?",
  "input": "",
  "output": "양파는 식물의 줄기 부분이며, 고구마는 식물의 뿌리 부분입니다."
}

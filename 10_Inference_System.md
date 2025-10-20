# ⚙️ Lecture 10: Inference System

## 1. 기본 텍스트 생성 (Basic Text Generation)

### 🧠 LLM 텍스트 생성 (LLM Text Generation)
- LLM은 **다음 단어(토큰)** 을 예측하도록 학습된 모델입니다.
- **디코딩(Decoding)** 은 이러한 확률 분포에서 토큰을 선택해 텍스트를 생성하는 과정입니다.
- 디코딩 방식에 따라 결과의 다양성과 품질이 달라집니다.
  - **Greedy Decoding**: 확률이 가장 높은 토큰을 순차적으로 선택 (결정적이지만 다양성 부족)
  - **Top-k Sampling**: 확률이 높은 상위 k개 후보 중 하나를 무작위 선택 (창의적 텍스트 생성 가능)
  - **Top-p (Nucleus) Sampling**: 누적 확률 p 이하의 후보 중에서 샘플링 (자연스러운 생성에 효과적)

### 🔁 자기회귀적 생성 (Autoregressive Generation)
- **Autoregressive Decoding Algorithm**
  - 시점 *t*까지의 입력으로부터 *t+1*번째 토큰을 예측.
  - 예측된 토큰을 입력에 추가 → 반복 수행.
- 한 단어씩 순차적으로 예측하기 때문에 사람이 글을 쓰듯 문맥을 고려한 자연스러운 생성 가능.
- 그러나, **긴 텍스트 생성 시 속도 저하** 및 **연산 비용 증가** 문제가 발생.

---

## 2. 간단한 텍스트 생성 (Simple Text Generation)

### 🧩 Hugging Face Pipeline
- Hugging Face의 `pipeline()`은 **모델 로드 → 입력 처리 → 후처리**를 자동화한 고수준 인터페이스입니다.
- 다양한 태스크 지원: Text Generation, Summarization, Translation, Question Answering 등.
- 내부 동작:
  1. 입력 전처리  
  2. 모델 추론 (inference)  
  3. 결과 후처리 및 출력  

```python
from transformers import pipeline
generator = pipeline("text-generation", model="gpt2")
print(generator("AI will change the world", max_length=50))
```

### 🚧 기본 생성 방식의 한계 (Naive Decoding Limitation)
- 긴 텍스트나 대량 요청 시 **매우 느림**.
- 이유:
  - 순차적 토큰 생성으로 매번 모델 호출.
  - Attention 연산의 **O(n²)** 복잡도.
  - KV Cache 비효율적 관리 (CPU 기반, 독립 Kernel 연산 등).

---

## 3. 추론 최적화 (Inference Optimization)

### ⚡ FlashAttention
- **IO 병목**(HBM 메모리 접근 비용)을 줄이기 위해 고안된 효율적 Attention 연산.
- 메모리 사용량과 속도 모두 크게 개선 (최대 2배 이상 향상).
- v2 버전은 더 나은 병렬화 및 작업 분할 구조로 발전.

📄 Reference: *Dao et al., “FlashAttention” (NeurIPS 2022)*

---

### 🧱 Paged Attention & KV Caching
- **KV Cache**: 이전 토큰의 Key/Value를 저장해 중복 연산 방지.
- 하지만 Cache 크기가 커질수록 메모리 병목 발생.
- **Paged Attention**:
  - KV Cache를 **Block 단위로 관리**하고 **Dynamic Memory Allocation** 수행.
  - 대규모 LLM 서빙에서 효율적 메모리 사용 가능.

📄 Reference: *Kwon et al., “PagedAttention” (SOSP 2023)*

---

### 🔮 Speculative Decoding
- 효율적 모델(Mq)이 여러 후보 토큰(γ개)을 생성하고,
  더 정확한 모델(Mp)이 이를 한 번에 평가.
- 한 번의 Mp 호출로 **γ+1개의 토큰 생성** → 속도 대폭 향상.

📄 Reference: *Leviathan et al., “Fast Inference via Speculative Decoding” (ICML 2023)*

---

### 🔁 Continuous Batching
- 기존: 모든 시퀀스의 추론이 끝날 때까지 대기 (Batch 크기 고정).
- 개선: 한 시퀀스가 끝나면 새로운 시퀀스를 즉시 추가하여 **Batch를 지속적으로 갱신**.
- GPU 자원 효율 극대화, 처리량(Throughput) 증가.

📄 Reference: *Anyscale Blog, 2023*

---

### 🧩 Kernel Fusion
- 여러 연산(MatMul → Add 등)을 **하나의 커널**로 합쳐 실행.
- GPU Launch Overhead를 줄이고 속도 향상.

📄 Reference: NVIDIA Developer Blog, *“TensorRT Inference Optimization”*

---

## 4. 추론 시스템 (Inference Systems)

| 시스템 | 특징 |
|--------|------|
| **VLLM** | Python 기반, FlashAttention & PagedAttention 통합, Speculative Decoding, Continuous Batching 지원 |
| **Text-Generation-Inference (TGI)** | Rust 기반, GPU 병렬처리, 안정적 서빙 API 제공 |
| **SGLang** | LLM·VLM 서빙 지원, Structured Output 및 Function Calling 가능 |
| **OpenAI Compatible** | OpenAI API와 동일한 인터페이스 제공 (ChatCompletion 형식) |

---

## 5. 추론 시스템 사용 가이드 (Practical Guide)

### 🧠 VLLM
```bash
pip install vllm
sh vllm.sh  # 모델 서빙
python inference.py  # OpenAI-compatible inference
```
- **특징**: FlashAttention, PagedAttention, Continuous Batching 모두 지원.

### ⚙️ TGI (Text-Generation-Inference)
```bash
docker install
sh tgi.sh
python inference.py
```
- **특징**: Rust 기반, Tensor Parallelism으로 다중 GPU 효율적 활용.

### 🧩 SGLang
```bash
pip install sglang[all]
pip install flashinfer -i https://flashinfer.ai/whl/cu121/torch2.4/
sh sglang.sh
python inference.py
```
- **특징**: Multi-modal 모델 지원, Function Calling 가능.

---

## 6. 더 빠른 추론을 위한 팁 (Advanced Acceleration)

### 🚀 Medusa
- **Multiple Decoding Heads** 를 학습시켜 여러 미래 토큰을 동시에 생성.
- Parameter-efficient 구조로 기존 대비 빠른 추론 가능.

📄 Reference: *GitHub - FasterDecoding/Medusa*

---

### 🧮 Quantization
- 모델의 **Floating Point**를 **int8 / int4** 등으로 변환.
- 메모리 절약 및 속도 향상 가능 (약 2~4배).
- 주로 *LLM.int8()* 또는 *GPTQ* 방식 사용.

📄 Reference: *OpenMMLab (2023), Gaussian37 Blog*

---

## 📊 요약 (Summary)

| 구분 | 내용 |
|------|------|
| **기본 생성** | LLM의 자기회귀적 생성 방식, 디코딩 전략 (Greedy, Top-k, Top-p) |
| **한계** | 긴 텍스트 처리 시 속도 저하 및 메모리 병목 |
| **최적화 기법** | FlashAttention, PagedAttention, KV Caching, Speculative Decoding 등 |
| **추론 시스템** | VLLM, TGI, SGLang — 모두 OpenAI-Compatible 방식 지원 |
| **고속화 팁** | Medusa (병렬 디코딩), Quantization (모델 경량화) |

> 💡 다양한 시스템을 OpenAI API 형태로 바로 사용할 수 있으며,  
> Latency, Throughput, 초당 토큰 생성 수 등 다양한 메트릭으로 비교해  
> 프로젝트 환경에 맞는 최적의 추론 방식을 선택하는 것이 핵심입니다.

---

📚 **References**
- Dao et al., *FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness* (NeurIPS 2022)  
- Kwon et al., *PagedAttention: Efficient Memory Management for LLM Serving* (SOSP 2023)  
- Leviathan et al., *Fast Inference via Speculative Decoding* (ICML 2023)  
- NVIDIA, *TensorRT Inference Optimization*  
- [vLLM Docs](https://docs.vllm.ai/en/latest)  
- [Hugging Face TGI](https://huggingface.co/docs/text-generation-inference/index)  
- [SGLang GitHub](https://github.com/sgl-project/sglang)  
- [Medusa Project](https://github.com/FasterDecoding/Medusa)  
- [Quantization Guide](https://openmmlab.medium.com/faster-and-more-efficient-4-bit-quantized-llm-model-inference-a27d35a66c29)

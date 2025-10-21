# 🧠 Generation for NLP — Summary Notes


## 🧩 전체 흐름 요약
1️⃣ LLM 구조 이해 (Encoder–Decoder)
2️⃣ 코드 레벨 분석
3️⃣ 모델 개발 프로세스
4️⃣ 데이터 수집 및 정제
5️⃣ 미세 조정과 학습
6️⃣ 평가 및 벤치마크
7️⃣ 긴 문맥 처리 기술
8️⃣ 임베딩 이해 및 활용
9️⃣ AI Agent 구조와 응용
🔟 추론 시스템 및 최적화


> 대규모 언어모델(LLM) 학습부터 추론 시스템까지,  
> **Generation for NLP** 강의의 핵심 내용을 요약 정리한 문서입니다.  
> 각 강의별 md 파일로 세부 내용을 확인할 수 있습니다.

---

## 📘 1️⃣ Encoder–Decoder 구조
- **핵심 개념:** Transformer 구조 이해, Decoder-only 아키텍처의 등장  
- **내용 요약:**  
  Encoder–Decoder의 기본 구조를 복습하고, LLaMA와 같은 Decoder-only 모델의 구조와 장점을 코드 수준으로 분석.  
  RoPE(회전 위치 임베딩), RMSNorm, Attention Mask 등의 내부 작동 이해.  


---

## 📗 2️⃣ Decoder 모델 코드 분석
- **핵심 개념:** LLaMA 모델 코드, Attention 흐름, Token 생성 과정  
- **내용 요약:**  
  Transformer 블록 내부를 코드로 파헤치며, Embedding → Attention → Feed Forward → Output의 과정을 단계별 설명.  
  Rotary Embedding과 FlashAttention의 구현 원리 분석.  

---

## 📙 3️⃣ Language Model Development Process
- **핵심 개념:** LM의 생애주기 — Pretraining → Finetuning → Preference Tuning  
- **내용 요약:**  
  대규모 사전학습부터 RLHF(DPO 등)까지의 전체 개발 파이프라인.  
  모델이 “언어를 배우는” 과정의 단계별 목표와 데이터 구성 방식 이해.  

---

## 📒 4️⃣ Pre-Training Dataset
- **핵심 개념:** 데이터 정제와 품질 관리  
- **내용 요약:**  
  RefinedWeb, FineWeb, LLaMA3의 데이터 수집–정제–필터링 과정 비교.  
  웹 크롤링 → 중복 제거 → 품질 평가 → 샘플링 등 대규모 데이터셋 제작 프로세스.  


---

## 📕 5️⃣ Fine-Tuning Dataset
- **핵심 개념:** Instruction Tuning과 KoAlpaca 실습  
- **내용 요약:**  
  한국어 Alpaca 데이터 제작 실습 포함.  
  SFT(Supervised Fine-Tuning)과 RLHF의 차이, 데이터 포맷(Instruction–Input–Output 구조) 정리.  


---

## 🧾 6️⃣ Evaluation for LLM
- **핵심 개념:** 모델 평가 프레임워크  
- **내용 요약:**  
  정량적 평가(MMLU, HellaSwag) + 정성적 평가(Human·LLM-as-a-judge).  
  Chatbot Arena, AlpacaEval, Ko-LLM 등 최신 벤치마크 트렌드 소개.  


---

## 📘 7️⃣ Long Context
- **핵심 개념:** 장문 맥락 모델의 필요성과 기술적 도전  
- **내용 요약:**  
  Lost in the Middle 문제, RoPE·ALiBi·Ring Attention 기법 소개.  
  긴 문맥 입력에서의 품질 저하 문제와 위치 임베딩 확장법(Position Interpolation) 정리.  


---

## 📙 8️⃣ Embedding
- **핵심 개념:** 임베딩의 개념과 발전  
- **내용 요약:**  
  Word2Vec → SBERT → LLM 기반 임베딩 모델까지의 진화.  
  Contrastive Learning, MTEB 벤치마크, E5/BGE 모델 소개.  
  임베딩 품질이 RAG 성능에 미치는 영향 분석.  


---

## 🤖 9️⃣ AI Agent
- **핵심 개념:** LLM 기반 자율형 에이전트의 구조  
- **내용 요약:**  
  Planning, Memory, Tool-use 기능 중심의 AI Agent 개념 이해.  
  AutoGPT, GraphRAG, Apple Intent 등 산업 응용 사례 및 윤리적 이슈 포함.  


---

## ⚙️ 🔟 Inference System
- **핵심 개념:** 추론 시스템과 최적화 기법  
- **내용 요약:**  
  FlashAttention, KV Caching, Speculative Decoding 등 추론 최적화 기법.  
  VLLM, TGI, SGLang 등 주요 추론 프레임워크 비교 및 활용 가이드.  
  Quantization, Medusa 등 실전 속도 개선 기법 포함.  


---
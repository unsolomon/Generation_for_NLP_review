# 🤖 AI Agent: From Concept to Application

---

## 1. What is AI Agent?

### 🧩 1.1 AI 에이전트의 정의와 역사

AI Agent는 **환경과 상호작용**하며, **데이터를 수집·분석**하고 **자율적 의사결정**을 통해 주어진 목표를 달성하는 **지능형 소프트웨어 시스템**입니다.

- **과거 (1990s)**  
  - Microsoft Agent, Clippy 등 단순한 데스크톱 보조 프로그램  
  - 규칙 기반(rule-based) 반응만 수행하는 단순 구조  
  - Bill Gates의 *The Road Ahead (1995)* 에서 “개인 에이전트 시대”를 예견함

- **현재 (2020s 이후)**  
  - 대규모 언어 모델(LLM)과 결합되어 **지능적이고 자율적인 AI 에이전트**로 발전  
  - 단순 응답 생성에서 벗어나, **계획(Planning)**, **기억(Memory)**, **도구 사용(Tool Usage)** 을 수행함

---

### ⚡ 1.2 LLM의 출현과 창발적 능력 (Emergent Abilities)

LLM의 파라미터 수가 일정 **임계점(Phase Transition)** 을 넘어서면  
작은 모델에서는 나타나지 않던 능력이 “창발(Emergent)” 형태로 나타남.

- **예시:**  
  - Few-shot Prompting (Wei et al., 2022)  
  - Context 내 학습(In-Context Learning), 복잡한 추론 능력 등  
- **특징:**  
  - 단순히 데이터나 연산량을 늘린다고 생기지 않음  
  - 모델 규모, 데이터 품질, 학습 구조의 상호작용으로 나타남  
- **연구 방향:**  
  - Scaling, 구조 혁신, 데이터 정제, 계산언어학적 해석 등

---

### 🧠 1.3 The Era of LLM Agent

LLM Agent는 단순 언어 모델과 달리 **자율적 사고 및 행동 루프**를 가짐.  
주요 구성 요소는 다음과 같습니다.

#### 🧩 (1) Planning — 계획 수립
복잡한 Task를 세분화하고, 단계별로 해결책을 도출함.

- **Task Decomposition 기법**
  - **CoT (Chain of Thought)** — 단계적 추론 (Wei et al., 2022)  
  - **ToT (Tree of Thoughts)** — 다중 사고경로 탐색 (Yao et al., 2023)  
  - **LLM+P** — 외부 Plan Solver 결합 (Liu et al., 2023)
- **Self-Reflection 기법**
  - **ReAct (Reason + Act)** — 사고와 행동의 반복적 개선 (Yao et al., 2022)  
  - **Reflexion** — LLM이 스스로 Verbal Feedback으로 학습 (Shinn & Labash, 2023)

#### 🧩 (2) Memory — 기억 구조
정보를 저장·유지·검색하여 지속적인 맥락 유지 가능.

| Memory Type | Description | Example |
|--------------|-------------|----------|
| **STM (Short-Term Memory)** | 최근 대화나 문맥을 보존 | Context Window: GPT-4 32k, LLaMA 2 4k 등 |
| **LTM (Long-Term Memory)** | 대량 정보 저장, 외부 검색 가능 | ChromaDB, FAISS 등 Vector DB 사용 |

#### 🧩 (3) Tool Usage — 도구 활용
외부 시스템, API, 또는 기능(Function)을 호출하여 정보 검색 및 실행 수행.

- **Out-of-model Data**: 최신 정보 접근 (검색, API 호출)
- **Function Calling (예: OpenAI, Gemini API)**  
  LLM이 JSON 형태로 함수를 호출 → 특정 작업 자동 수행  
  → 신뢰성 높은 파싱과 자율적 툴 선택 가능

---

### 🤖 1.4 AutoGPT — 자율적 에이전트의 대표 사례
- GitHub: [Significant-Gravitas/AutoGPT](https://github.com/Significant-Gravitas/AutoGPT)  
- GPT 모델을 기반으로 **스스로 목표를 설정하고**,  
  **작업을 분해·수행·평가**하는 완전 자율형 AI Agent  
- 단, 실제 파일 접근이나 시스템 제어 권한은 제한됨

---

### 💬 1.5 Human-AI Interaction (HAI)

AI 에이전트는 인간 중심적이어야 함.  
목표는 “**User-Friendly, Trustworthy, Ethical, Beneficial**” AI.

- **연구 주제**
  - Explainable AI (XAI)  
  - 사용자 맞춤 적응형 AI 시스템  
  - 사회적 영향 평가 메트릭
- **주요 학회**
  - ACM CHI (Computer Human Interaction)

---

## 2. AI Agents in Trends

### 🔄 2.1 최신 에이전트 워크플로우

#### ⚙️ Mixture of Agents (Wang et al., 2024)
- 다층 구조의 협력형 에이전트 시스템  
- 각 레이어가 이전 결과를 정제 → 점진적으로 최종 답변 생성  
- 장점: 복잡한 문제에 대해 집단적 지능 발휘

#### 🔍 Retrieval-Augmented Generation (RAG; Lewis et al., 2021)
- 쿼리 인코딩 → 관련 문서 검색 → 문서 인덱싱 → 최종 생성  
- 외부 지식을 결합해 LLM의 환각(Hallucination) 감소

#### 🧭 Graph RAG (Edge et al., 2024)
- 텍스트를 그래프 구조로 변환하여 엔티티 관계 기반 질의응답 수행  
- 데이터 분할 → 엔티티 추출 → 클러스터링 → 요약 생성 → 글로벌 응답  
- 복잡한 정보 구조에 강점

---

### 🏭 2.2 산업별 AI 에이전트 응용 사례

| 분야 | 주요 서비스 | 특징 |
|------|--------------|------|
| **자동화 (Automation)** | MultiOn, Cassidy, Rabbit, Google CSA | 반복 업무 자동화, 개인 비서형 |
| **코딩 (Coding Assistant)** | Codeium, GitHub Copilot, AWS CodeWhisperer | 코드 추천, 버그 탐지, 최적화 |
| **데이터 분석 (Data Analytics)** | Polymer, BigQuery, Tableau AI | 데이터 인사이트 도출, 시각화 자동화 |
| **개인화 서비스 (Apple Intelligence)** | Apple Intent API | 사용자 의도 기반 개인 맞춤 서비스 |

---

### ⚖️ 2.3 윤리적·사회적 고려사항

#### 🧭 주요 이슈와 대응
| 이슈 | 설명 | 해결 방향 |
|------|------|------------|
| **Bias & Discrimination** | 편향된 데이터로 인한 불공정 결과 | 다양성 확보, 공정성 검증 |
| **Transparency & Accountability** | AI 판단의 불투명성 | XAI, 알고리즘 감사 체계 |
| **Creativity & Ownership** | AI 생성물의 저작권 문제 | 법·제도적 프레임워크 마련 |
| **Misinformation** | 딥페이크, 허위정보 확산 | 팩트체크 강화, 미디어 리터러시 |
| **Privacy & Surveillance** | 데이터 수집으로 인한 사생활 침해 | 암호화·익명화 기술, 법적 규제 |
| **Job Displacement** | AI로 인한 일자리 감소 | 재교육, 기본소득, 신직업 창출 |
| **Autonomous Weapons** | AI 무기 통제 문제 | 국제 협약 및 윤리 가이드라인 |

---

## 3. How to Build AI Agent?

### 🧠 실습 예제
> **A Simple Agent with Guidance & ReAct (Open-Source LLM)**  
> [Google Colab Link](https://colab.research.google.com/drive/13oUqf0ne90BRJNILE4U2RxfjnO6Pq8NO?usp=sharing)

#### 🧩 주요 구성요소
1. **Guidance** — 에이전트의 행동 규칙 및 목적 정의  
2. **ReAct** — Reason + Act 패턴으로 자율적 사고와 행동 반복  
3. **Memory** — 과거 상호작용 기반 적응형 응답  
4. **LLM** — 오픈소스 기반 언어 모델 (예: LLaMA, Mistral 등)

---

## 📚 Reference
- Lilian Weng, *LLM Powered Autonomous Agents*, 2023  
- Yao et al., *ReAct: Reasoning and Acting in Language Models*, 2022  
- Shinn & Labash, *Reflexion: Verbal Reinforcement in LLM Agents*, 2023  
- Lewis et al., *Retrieval-Augmented Generation (RAG)*, 2021  
- Edge et al., *Graph RAG: Graph-based Contextual QA*, 2024  
- Wang et al., *Mixture of Agents*, 2024  
- [AutoGPT GitHub](https://github.com/Significant-Gravitas/AutoGPT)  
- ACM CHI Conference on Human-AI Interaction  
- OpenAI & Google Gemini Function Calling Docs

---

> ✨ **정리 요약:**  
> AI Agent는 단순 LLM을 넘어, **계획(Planning)·기억(Memory)·도구 사용(Tool Use)** 을 기반으로  
> 스스로 사고하고 행동하는 자율 지능 시스템이다.  
> 미래 AI의 핵심은 ‘**자율성 + 인간 중심성 + 윤리성**’이다.

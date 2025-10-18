# 04_Pretraining-Data-Collection-and-Curation.md

## 📘 Lecture: Pre-Training Data — 수집 및 정제 방법
> 대규모 언어 모델(LLM)의 성능은 데이터에 의해 좌우됩니다. 이 문서는 Pre-training 데이터의 중요성, 오픈소스 코퍼스, 데이터 처리 도구와 전형적인 정제 파이프라인(예: RefinedWeb / FineWeb의 MDR)을 정리합니다.
[oaicite:1]{index=1}

---

## 1. 왜 Pre-Training Data가 중요한가
- LLM은 **autoregressive**(자기회귀) 확률모델로, 대규모 텍스트에서 통계적 패턴과 지식을 학습합니다.  
- 따라서 **고품질, 다양한 데이터**를 통해 올바른 지식과 언어 구조를 주입하는 것이 매우 중요합니다.
- 데이터 품질이 낮거나 중복/스팸이 많으면 모델이 비효율적으로 학습하거나 잘못된 출력을 생성(할루시네이션 등)할 수 있음.

---

## 2. 데이터 정제의 기본 원칙(핵심 처리 단계)
일반적으로 대규모 웹 코퍼스는 다음 단계로 정제됩니다.

1. **수집 (Collection)**  
   - CommonCrawl, 위키, 뉴스, 포럼, 코드 저장소, 책 등 다양한 소스에서 수집.

2. **텍스트 추출 (Text Extraction)**  
   - HTML → 본문 텍스트 추출 (메뉴/광고/헤더/푸터 제거). 예: Trafilatura 사용.

3. **언어 식별 (Language ID)**  
   - FastText 등으로 언어 감지. 신뢰도(score 기준) 미달 문서 제거.

4. **문서·라인 단위 정제 (Line / Document Cleaning)**  
   - 소셜 카운터, 내비게이션 텍스트, 매우 짧거나 비언어적 라인 제거.
   - 정규표현식으로 URL, 중복 줄바꿈 등 포맷 제거.

5. **반복/스팸 제거 (Repetition Removal)**  
   - 지나치게 반복되는 라인·문단·n-gram 제거(heuristic 사용).

6. **중복 제거 (Deduplication)**  
   - **Fuzzy Deduplication**: MinHash 등으로 유사 문서 제거.  
   - **Exact Deduplication**: 시퀀스(토큰) 레벨로 완전 일치 제거(예: suffix array, 연속 50토큰 기준).  
   - **URL Deduplication**: 동일 URL 재수집으로 인한 중복 제거.

7. **콘텐츠 필터링 (Content Filters)**  
   - 성인/유해 콘텐츠, 악성 도메인, 스팸 도메인 필터링(도메인 블랙리스트 · 점수화 시스템).

8. **프라이버시 / PII 제거 (Privacy Reduction)**  
   - 계좌번호, 전화번호 등 민감정보 마스킹/삭제.

9. **품질 평가 (Quality Filtering / Scoring)**  
   - 모델 기반 품질 분류기(quality classifier)로 점수 부여 → 저품질 문서 제거 또는 다운샘플링.

10. **후처리 및 메타데이터 유지**  
    - id, url, date, lang, token_count 등 메타 정보 저장 (데이터 믹스/분석에 필요).

---

## 3. 주요 오픈소스 데이터셋(요약)
- **RedPajama (TogetherComputer)**: LLaMA 학습데이터 재현, 다양한 소스 포함.  
- **The Pile (EleutherAI)**: 22개 하위셋으로 구성된 대규모 영어 코퍼스.  
- **RefinedWeb (Falcon)**: CommonCrawl 기반, 엄격한 필터링·대규모 중복 제거 → 고품질 웹 데이터셋.  
- **C4 (Google)**: CommonCrawl 정제본(중복/비영어·부적절 내용 제거).  
- **CulturaX (UONLP)**: 다국어(167개 언어) 대규모 코퍼스.  
- **DCLM-pool (DataCompLM)**: 연구용, 대규모 데이터풀.

---

## 4. 주요 오픈소스 데이터 처리 도구
- **CommonCrawl**: 공개 웹 크롤 데이터(원본).  
- **Trafilatura**: HTML → 본문 텍스트 추출(메타데이터 포함).  
- **FastText**: 언어 식별 및 텍스트 분류.  
- **DataTrove (Hugging Face)**: Rust 기반 대규모 텍스트 파이프라인(필터링·dedup 등).  
- **EleutherAI DPS**: PySpark 기반 분산 데이터 처리(대형 코퍼스에 적합, PII 처리 포함).

---

## 5. RefinedWeb / FineWeb 파이프라인 (MDR: Macro Data Refinement) — 상세
RefinedWeb의 MDR 파이프라인은 CommonCrawl에서 시작해 여러 단계로 정제하여 고품질 웹 코퍼스를 만듭니다.

**주요 단계 및 기법**
1. **URL 필터링 (Blocklist + Score system)**  
   - 악성 사이트·스팸 도메인 식별 → 차단 또는 가중치 낮춤.

2. **텍스트 추출 (Trafilatura)**  
   - 메뉴/광고 등 제거, 정규표현식으로 불필요 문자 제거.

3. **언어 식별 (FastText)**  
   - top language score 기준(예: 0.65 미만 제거).

4. **반복·문서 필터링 (Heuristics from Gopher / MassiveText)**  
   - 과도한 라인/문단·n-gram 반복 제거, symbol-to-word ratio 기준, 비정상 토큰 분포 필터링.

5. **라인 단위 보정 (Line-correction filters)**  
   - 소셜카운터, 네비게이션 텍스트 등 추가 제거 규칙 적용.

6. **퍼지 중복 제거 (MinHash 기반)**  
   - 유사 문서 클러스터링 후 대표 문서 선택/유사문서 제거.

7. **정확한 중복 제거 (Suffix-array 등)**  
   - 토큰 연속 일치(예: 50토큰 이상) 시 제거 및 해당 문단 제거.

8. **URL 기반 중복 제거**  
   - CommonCrawl 파티셔닝을 활용한 URL 재방문 제거.

9. **소규모 모델로 품질 검증**  
   - 정제된 데이터로 작은 모델 학습 → 여러 벤치마크(Zero-shot)로 평가(lm-eval 등).

---

## 6. FineWeb & FineWeb-EDU (Hugging Face) 요약
- **FineWeb**: RefinedWeb 아이디어 계승, CommonCrawl에서 엄격 정제·중복 제거한 대규모 영어 웹 코퍼스(트랜스페어런시 강조).  
- **FineWeb-EDU**: 교육적 가치가 높은 서브셋(예: MMLU, ARC 같은 지식/추론 벤치마크에서 유리).  
- **특징**: datatrove 등 도구로 파이프라인 구성, 메타데이터(id, url, date, language_score 등) 포함.

---

## 7. LLaMA 3.1 / LLaMA-3 Herd 데이터 전략 (요점)
- **규모**: ~15T(조) multilingual tokens(2023년까지 수집)  
- **엄격한 Curation**: PII·안전 필터, custom parser로 boilerplate 제거, human eval로 파서 품질 점검  
- **Deduplication**: heuristic + model-based filtering + sequence-level deduplication(토큰 일치 검사)  
- **도메인별 파이프라인**: 코드/수학 페이지는 별도 보존 정책(수식/코드 보전), 도메인 특화 청소  
- **데이터 믹스 (예시)**:
  - General knowledge: 50%  
  - Math & reasoning: 25%  
  - Code: 17%  
  - Multilingual: 8%  
- **데이터 어닐링(Annealing)**:
  - 특정 고품질 도메인 데이터의 빈도를 학습 중 조절(upsampling)하여 벤치마크 성능 개선 검증(GSM8K 등에서 유의미한 향상 관측).

---

## 8. 실무 체크리스트 (데이터 파이프라인 설계 시)
1. **데이터 소스 다양화**: 웹 + 도메인 특화(코드, 수학, 위키, 뉴스 등).  
2. **정책(Policy) 설계**: PII·저작권·안전 정책 명확화.  
3. **자동화 파이프라인**: 추출 → 언어검증 → 필터 → dedup → 품질평가 → 메타데이터 저장.  
4. **검증 루프**: 소규모 모델 학습 → 벤치마크(Zero-shot)로 품질 검증.  
5. **버전·재현성**: 데이터셋 버전·해시·파이프라인 코드(예: datatrove) 저장.  
6. **윤리·법률 검토**: 저작권·개인정보 영향 평가 및 대응 방안 마련.  
7. **평가 지표**: token distribution, symbol-to-word ratio, language confidence, duplicate rate 등 모니터링.

---

## 9. 한계 및 고려사항
- 대부분 공개 정제 코퍼스는 **영어 중심**(한국어 등 언어별 커버리지 한계).  
- 자동 필터는 완벽하지 않으므로 **human-in-the-loop** 검증 필요.  
- 저작권·프라이버시 이슈는 프로젝트별로 법적 검토 필수.

---

## 10. 참고 자료 / 링크
- RefinedWeb (Falcon LLM) — Penedo et al., arXiv:2306.01116.  
- FineWeb / FineWeb-EDU — Hugging Face datasets (HuggingFaceFW/fineweb).  
- CommonCrawl: https://commoncrawl.org/  
- Trafilatura: https://github.com/adbar/trafilatura  
- FastText: https://github.com/facebookresearch/fastText  
- DataTrove (Hugging Face)  

---

### 맺음말
- 고품질의 Pre-training 데이터셋은 모델 성능의 기초
- 단순히 데이터량이 아니라 **데이터 품질, 중복 제거, 도메인별 균형**이 더 큰 영향을 줌
- 파이프라인 자동화 + 주기적 인간 검수 + 철저한 메타데이터 관리가 필수


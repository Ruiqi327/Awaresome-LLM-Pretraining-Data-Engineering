<a id="top"></a>
<p align="center"><img src="asset/logo.png" alt="LLM Pretraining Data Engineering" width="900"></p>

<h1 align="center">Awesome Pretraining Data Engineering</h1>
<p align="center"><b>A curated reading list on the data that shapes language models.</b></p>
<p align="center">
  <img src="https://img.shields.io/badge/scope-text_pretraining-0f766e" alt="Scope: text pretraining">
  <img src="https://img.shields.io/badge/papers-145-2563eb" alt="145 papers">
</p>
<p align="center"><a href="#reading-guide">Reading guide</a> · <a href="#technical-reports">Technical reports</a> · <a href="asset/LLM_Data_Survey_Revised.pdf">Our survey (PDF)</a></p>

This collection follows **corpus construction → data utilization → pretraining evaluation**, informed by [*Data Engineering in Large Language Model Pretraining: A Survey*](asset/LLM_Data_Survey_Revised.pdf). Technical reports from major model developers are collected in one place; methodological papers are organized by a small number of practical themes.

**Scope:** text-based pretraining, including code and mathematical text. Document extraction is included when its output becomes text training data. Model architectures, instruction-tuning-only datasets, RLHF recipes, and multimodal alignment are outside the list's methodological scope.

**Last source check: 2026-10-13 · 145 papers.** This is a curated snapshot, not an exhaustive index.

> **How to read the tables.** Each literature table is sorted newest first, using first-submission dates within the same year. Years use the first submission date on the linked arXiv record, which can differ from the conference year or the digits in an arXiv identifier. Titles are taken from the current record. **Code** denotes a research implementation; **Model repo**, **Pipeline**, **Eval/data**, and **Cookbook** describe other official resources. A repository link does not imply that a complete pretraining pipeline or training data is released. **—** means no official repository was verified in this curation pass, not that none exists.

## Contents

- [Reading guide](#reading-guide)
- [Technical reports from major model developers](#technical-reports)
- [Corpus construction](#corpus-construction)
  - [Corpora, curation & extraction](#corpora)
  - [Deduplication, filtering & data quality](#quality)
  - [Synthetic data & rewriting](#synthetic)
- [Data utilization](#data-utilization)
  - [Data mixtures & budget allocation](#mixtures)
  - [Training schedules, context & supervision](#schedules)
- [Pretraining evaluation](#evaluation)
- [Acknowledgments](#acknowledgments)

<a id="reading-guide"></a>
## 🧭 Reading guide

| Your question | A useful starting point |
|:--|:--|
| How do I build a reproducible corpus? | [Dolma](https://arxiv.org/abs/2402.00159) → [FineWeb](https://arxiv.org/abs/2406.17557) → [FineWeb2](https://arxiv.org/abs/2506.20920) |
| Which documents should I retain? | [Deduplicating Training Data](https://arxiv.org/abs/2107.06499) → [QuRating](https://arxiv.org/abs/2402.09739) → [DataComp-LM](https://arxiv.org/abs/2406.11794) |
| How should I mix sources? | [DoReMi](https://arxiv.org/abs/2305.10429) → [RegMix](https://arxiv.org/abs/2407.01492) → [DataDecide](https://arxiv.org/abs/2504.11393) |
| How can existing text provide better training signals? | [In-context Pretraining](https://arxiv.org/abs/2310.10638) → [RHO-1](https://arxiv.org/abs/2404.07965) → [MeCo](https://arxiv.org/abs/2501.01956) |
| How do I compare data recipes fairly? | [OLMES](https://arxiv.org/abs/2406.08446), [DataComp-LM](https://arxiv.org/abs/2406.11794), and the [evaluation section](#evaluation) |

For industry practice, compare the data sections of **Llama 3**, **Qwen3**, **DeepSeek-V3**, **Kimi K2**, **GLM-5**, **Phi-4**, and **OLMo 3** below. These reports document recipes; they do not necessarily isolate the causal contribution of every data decision.

<a id="technical-reports"></a>
## 🏢 Technical reports from major model developers

Read these reports for corpus sources, filtering, synthesis, mixtures, and training stages. Companies and major open-model research teams are grouped together here; architecture and post-training details are not catalogued.

**44 papers**

| Year | Paper | Data-engineering focus | Code / official resource |
|:--:|:--|:--|:--|
| 2026 | [**GLM-5: from Vibe Coding to Agentic Engineering**](https://arxiv.org/abs/2602.15763) | **Z.ai / Tsinghua** · pretraining data and staged training disclosures | [Model repo](https://github.com/zai-org/GLM-5) |
| 2025 | [**Olmo 3**](https://arxiv.org/abs/2512.13961) | **Ai2** · document extraction and mid-training data | [Training code](https://github.com/allenai/Olmo-core) |
| 2025 | [**GLM-4.5: Agentic, Reasoning, and Coding (ARC) Foundation Models**](https://arxiv.org/abs/2508.06471) | **Z.ai / Tsinghua** · staged pretraining and domain emphasis | [Model repo](https://github.com/zai-org/GLM-4.5) |
| 2025 | [**Kimi K2: Open Agentic Intelligence**](https://arxiv.org/abs/2507.20534) | **Moonshot AI** · document rewriting and data quality | [Model repo](https://github.com/MoonshotAI/Kimi-K2) |
| 2025 | [**Qwen3 Technical Report**](https://arxiv.org/abs/2505.09388) | **Alibaba** · Qwen3 · annotation and staged pretraining | [Model repo](https://github.com/QwenLM/Qwen3) |
| 2025 | [**Gemma 3 Technical Report**](https://arxiv.org/abs/2503.19786) | **Google DeepMind** · text-data disclosures within a multimodal report | [Model repo](https://github.com/google-deepmind/gemma) |
| 2025 | [**SmolLM2: When Smol Goes Big -- Data-Centric Training of a Small Language Model**](https://arxiv.org/abs/2502.02737) | **Hugging Face** · SmolLM2 · data-centric small-model training and open recipes | [Model repo](https://github.com/huggingface/smollm) |
| 2024 | [**2 OLMo 2 Furious**](https://arxiv.org/abs/2501.00656) | **Ai2** · staged training and data annealing | [Training code](https://github.com/allenai/Olmo-core) |
| 2024 | [**DeepSeek-V3 Technical Report**](https://arxiv.org/abs/2412.19437) | **DeepSeek** · corpus expansion and context extension | [Model repo](https://github.com/deepseek-ai/DeepSeek-V3) |
| 2024 | [**Qwen2.5 Technical Report**](https://arxiv.org/abs/2412.15115) | **Alibaba** · Qwen2.5 · synthetic and domain data | [Model repo](https://github.com/QwenLM/Qwen3) |
| 2024 | [**Phi-4 Technical Report**](https://arxiv.org/abs/2412.08905) | **Microsoft** · synthetic-data-intensive pretraining | [Cookbook](https://github.com/microsoft/PhiCookBook) |
| 2024 | [**Qwen2.5-Coder Technical Report**](https://arxiv.org/abs/2409.12186) | **Alibaba** · code corpus and synthetic data | [Model repo](https://github.com/QwenLM/Qwen3-Coder) |
| 2024 | [**Qwen2.5-Math Technical Report: Toward Mathematical Expert Model via Self-Improvement**](https://arxiv.org/abs/2409.12122) | **Alibaba** · mathematical data construction | [Model repo](https://github.com/QwenLM/Qwen2.5-Math) |
| 2024 | [**Gemma 2: Improving Open Language Models at a Practical Size**](https://arxiv.org/abs/2408.00118) | **Google DeepMind** · teacher-distribution pretraining targets | [Model repo](https://github.com/google-deepmind/gemma) |
| 2024 | [**The Llama 3 Herd of Models**](https://arxiv.org/abs/2407.21783) | **Meta** · Llama 3 · filtering, mixtures, annealing | [Model repo](https://github.com/meta-llama/llama3) |
| 2024 | [**Qwen2 Technical Report**](https://arxiv.org/abs/2407.10671) | **Alibaba** · Qwen2 · quality and domain coverage | [Model repo](https://github.com/QwenLM/Qwen3) |
| 2024 | [**ChatGLM: A Family of Large Language Models from GLM-130B to GLM-4 All Tools**](https://arxiv.org/abs/2406.12793) | **Z.ai / Tsinghua** · multilingual pretraining corpus | [Model repo](https://github.com/zai-org/GLM-4) |
| 2024 | [**DeepSeek-Coder-V2: Breaking the Barrier of Closed-Source Models in Code Intelligence**](https://arxiv.org/abs/2406.11931) | **DeepSeek** · continued code pretraining and infilling | [Model repo](https://github.com/deepseek-ai/DeepSeek-Coder-V2) |
| 2024 | [**Nemotron-4 340B Technical Report**](https://arxiv.org/abs/2406.11704) | **NVIDIA** · corpus mixture and staged training | — |
| 2024 | [**DeepSeek-V2: A Strong, Economical, and Efficient Mixture-of-Experts Language Model**](https://arxiv.org/abs/2405.04434) | **DeepSeek** · cross-snapshot deduplication and filtering | [Model repo](https://github.com/deepseek-ai/DeepSeek-V2) |
| 2024 | [**Phi-3 Technical Report: A Highly Capable Language Model Locally on Your Phone**](https://arxiv.org/abs/2404.14219) | **Microsoft** · data quality and synthetic text | [Cookbook](https://github.com/microsoft/PhiCookBook) |
| 2024 | [**MiniCPM: Unveiling the Potential of Small Language Models with Scalable Training Strategies**](https://arxiv.org/abs/2404.06395) | **OpenBMB** · data schedules and warmup-stable-decay | [Model repo](https://github.com/OpenBMB/MiniCPM) |
| 2024 | [**InternLM2 Technical Report**](https://arxiv.org/abs/2403.17297) | **Shanghai AI Lab** · deduplication and continued pretraining | [Model repo](https://github.com/InternLM/InternLM) |
| 2024 | [**Gemma: Open Models Based on Gemini Research and Technology**](https://arxiv.org/abs/2403.08295) | **Google DeepMind** · filtering and data governance | [Model repo](https://github.com/google-deepmind/gemma) |
| 2024 | [**Yi: Open Foundation Models by 01.AI**](https://arxiv.org/abs/2403.04652) | **01.AI** · quality filtering and data concentration | [Model repo](https://github.com/01-ai/Yi) |
| 2024 | [**StarCoder 2 and The Stack v2: The Next Generation**](https://arxiv.org/abs/2402.19173) | **BigCode** · The Stack v2 and code-data provenance | [Model repo](https://github.com/bigcode-project/starcoder2) |
| 2024 | [**OLMo: Accelerating the Science of Language Models**](https://arxiv.org/abs/2402.00838) | **Ai2** · fully open training and corpus provenance | [Training code](https://github.com/allenai/OLMo) |
| 2024 | [**DeepSeek LLM: Scaling Open-Source Language Models with Longtermism**](https://arxiv.org/abs/2401.02954) | **DeepSeek** · early corpus and scaling recipe | [Model repo](https://github.com/deepseek-ai/DeepSeek-LLM) |
| 2023 | [**The Falcon Series of Open Language Models**](https://arxiv.org/abs/2311.16867) | **TII** · web-centered training recipe | — |
| 2023 | [**Qwen Technical Report**](https://arxiv.org/abs/2309.16609) | **Alibaba** · Qwen · multilingual corpus construction | [Model repo](https://github.com/QwenLM/Qwen) |
| 2023 | [**Baichuan 2: Open Large-scale Language Models**](https://arxiv.org/abs/2309.10305) | **Baichuan** · multilingual corpus and data processing | [Model repo](https://github.com/baichuan-inc/Baichuan2) |
| 2023 | [**Textbooks Are All You Need II: phi-1.5 technical report**](https://arxiv.org/abs/2309.05463) | **Microsoft** · Phi-1.5 · synthetic and web-text mixture | — |
| 2023 | [**Code Llama: Open Foundation Models for Code**](https://arxiv.org/abs/2308.12950) | **Meta** · code specialization and infilling | [Model repo](https://github.com/meta-llama/codellama) |
| 2023 | [**Llama 2: Open Foundation and Fine-Tuned Chat Models**](https://arxiv.org/abs/2307.09288) | **Meta** · Llama 2 · expanded pretraining data | [Model repo](https://github.com/meta-llama/llama) |
| 2023 | [**Textbooks Are All You Need**](https://arxiv.org/abs/2306.11644) | **Microsoft** · Phi-1 · textbook-style synthetic pretraining | — |
| 2023 | [**PaLM 2 Technical Report**](https://arxiv.org/abs/2305.10403) | **Google DeepMind** · PaLM 2 · data balance and multilingual coverage | — |
| 2023 | [**StarCoder: may the source be with you!**](https://arxiv.org/abs/2305.06161) | **BigCode** · code curation and fill-in-the-middle | [Model repo](https://github.com/bigcode-project/starcoder) |
| 2023 | [**GPT-4 Technical Report**](https://arxiv.org/abs/2303.08774) | **OpenAI** · GPT-4 · limited pretraining disclosure | — |
| 2023 | [**LLaMA: Open and Efficient Foundation Language Models**](https://arxiv.org/abs/2302.13971) | **Meta** · LLaMA · public-source corpus construction | [Model repo](https://github.com/meta-llama/llama) |
| 2022 | [**BLOOM: A 176B-Parameter Open-Access Multilingual Language Model**](https://arxiv.org/abs/2211.05100) | **BigScience** · multilingual corpus governance and mixture | [Training code](https://github.com/bigscience-workshop/bigscience) |
| 2022 | [**GLM-130B: An Open Bilingual Pre-trained Model**](https://arxiv.org/abs/2210.02414) | **Z.ai / Tsinghua** · bilingual data | [Model repo](https://github.com/zai-org/GLM-130B) |
| 2022 | [**PaLM: Scaling Language Modeling with Pathways**](https://arxiv.org/abs/2204.02311) | **Google DeepMind** · PaLM · multilingual and code mixtures | — |
| 2021 | [**Scaling Language Models: Methods, Analysis & Insights from Training Gopher**](https://arxiv.org/abs/2112.11446) | **Google DeepMind** · Gopher · heuristic quality filtering | — |
| 2020 | [**Language Models are Few-Shot Learners**](https://arxiv.org/abs/2005.14165) | **OpenAI** · GPT-3 · corpus filtering and mixture design | [Eval/data](https://github.com/openai/gpt-3) |

[↑ Back to top](#top)

<a id="corpus-construction"></a>
## 🧱 Corpus construction

<a id="corpora"></a>
### Corpora, curation & extraction

Open corpora, targeted web acquisition, data-processing systems, domain sources, and document-to-text extraction. FineWeb-Edu is covered by the FineWeb paper; The Stack v2 is covered by StarCoder 2 in the report section.

**19 papers**

| Year | Paper | Data-engineering focus | Code / official resource |
|:--:|:--|:--|:--|
| 2026 | [**Host-Aware Web Crawling for LLM Pretraining Data**](https://arxiv.org/abs/2610.13321) | Quality-aware allocation of crawl depth across web hosts | — |
| 2026 | [**ReScraper: Unified Scraping and Cleaning of Web Data for Effective LLM Pretraining**](https://arxiv.org/abs/2609.34287) | Unified learned extraction, cleaning and rewriting of web pages | [Code](https://github.com/cxcscmu/ReScraper) |
| 2026 | [**Data Scout: Targeted Web Crawling for Domain-Specific Pretraining Corpora**](https://arxiv.org/abs/2609.05766) | Targeted crawling for domain-specific pretraining corpora | [Code](https://github.com/IBM/data-scout) |
| 2025 | [**FineWeb2: One Pipeline to Scale Them All -- Adapting Pre-Training Data Processing to Every Language**](https://arxiv.org/abs/2506.20920) | Multilingual corpus curation across more than 1,000 languages | [Pipeline](https://github.com/huggingface/datatrove) |
| 2025 | [**The Common Pile v0.1: An 8TB Dataset of Public Domain and Openly Licensed Text**](https://arxiv.org/abs/2506.05209) | Public-domain and openly licensed corpus with reproducible processing and training mixtures | [Pipeline](https://github.com/r-three/common-pile) |
| 2025 | [**olmOCR: Unlocking Trillions of Tokens in PDFs with Vision Language Models**](https://arxiv.org/abs/2502.18443) | PDF extraction for text pretraining | [Code](https://github.com/allenai/olmocr) |
| 2024 | [**Nemotron-CC: Transforming Common Crawl into a Refined Long-Horizon Pretraining Dataset**](https://arxiv.org/abs/2412.02595) | Classifier ensembles and synthetic rewriting | [Pipeline](https://github.com/NVIDIA-NeMo/Curator) |
| 2024 | [**RedPajama: an Open Dataset for Training Large Language Models**](https://arxiv.org/abs/2411.12372) | Web corpus with reusable quality annotations | [Pipeline](https://github.com/togethercomputer/RedPajama-Data) |
| 2024 | [**MinerU: An Open-Source Solution for Precise Document Content Extraction**](https://arxiv.org/abs/2409.18839) | Document extraction and reading-order recovery | [Code](https://github.com/opendatalab/MinerU) |
| 2024 | [**The FineWeb Datasets: Decanting the Web for the Finest Text Data at Scale**](https://arxiv.org/abs/2406.17557) | FineWeb / FineWeb-Edu · web-text processing and educational filtering | [Pipeline](https://github.com/huggingface/datatrove) |
| 2024 | [**DataComp-LM: In search of the next generation of training sets for language models**](https://arxiv.org/abs/2406.11794) | Controlled data-selection benchmark and open corpus | [Code](https://github.com/mlfoundations/dclm) |
| 2024 | [**Dolma: an Open Corpus of Three Trillion Tokens for Language Model Pretraining Research**](https://arxiv.org/abs/2402.00159) | Open three-trillion-token corpus and processing pipeline | [Pipeline](https://github.com/allenai/dolma) |
| 2023 | [**Data-Juicer: A One-Stop Data Processing System for Large Language Models**](https://arxiv.org/abs/2309.02033) | Composable data processing and recipe analysis | [Pipeline](https://github.com/datajuicer/data-juicer) |
| 2023 | [**Nougat: Neural Optical Understanding for Academic Documents**](https://arxiv.org/abs/2308.13418) | Academic PDF-to-text extraction | [Code](https://github.com/facebookresearch/nougat) |
| 2023 | [**The RefinedWeb Dataset for Falcon LLM: Outperforming Curated Corpora with Web Data, and Web Data Only**](https://arxiv.org/abs/2306.01116) | Web extraction, filtering and deduplication | — |
| 2022 | [**The Stack: 3 TB of permissively licensed source code**](https://arxiv.org/abs/2211.15533) | Permissively licensed source-code corpus | — |
| 2020 | [**The Pile: An 800GB Dataset of Diverse Text for Language Modeling**](https://arxiv.org/abs/2101.00027) | Diverse multi-source text corpus | [Pipeline](https://github.com/EleutherAI/the-pile) |
| 2020 | [**CLUECorpus2020: A Large-scale Chinese Corpus for Pre-training Language Model**](https://arxiv.org/abs/2003.01355) | Chinese pretraining corpus | [Eval/data](https://github.com/CLUEbenchmark/CLUECorpus2020) |
| 2019 | [**Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer**](https://arxiv.org/abs/1910.10683) | C4 corpus construction; included for data, not architecture | [Code](https://github.com/google-research/text-to-text-transfer-transformer) |

[↑ Back to top](#top)

<a id="quality"></a>
### Deduplication, filtering & data quality

Duplicate control, multi-dimensional and multilingual filtering, adaptive curation, repeated-data trade-offs, and audits of source coverage. Selection changes the available corpus; repeated epochs change training exposure.

**18 papers**

| Year | Paper | Data-engineering focus | Code / official resource |
|:--:|:--|:--|:--|
| 2026 | [**Adapting English Quality Classifiers for Multilingual LLM Pretraining Data Selection**](https://arxiv.org/abs/2610.11585) | Transferring English quality scores to multilingual corpus selection | — |
| 2026 | [**Towards Scalable Data Diversification for Language Model Pretraining via Leverage Score Sampling**](https://arxiv.org/abs/2609.32484) | Embedding-space diversification through leverage score sampling | [Code](https://github.com/mazailin/LevDataDiversity) |
| 2026 | [**AutoData: Agentic Search for Pre-training Data Selection**](https://arxiv.org/abs/2609.19754) | Agentic search over executable data-selection programs | [Code](https://github.com/WecoAI/AutoData) |
| 2026 | [**Scaling Domain Data Repetition in LLM Pretraining**](https://arxiv.org/abs/2608.14071) | Scaling effects of repeated exposure to domain data | — |
| 2026 | [**DataOrchestra: Learning to Orchestrate Per-Example Curation of Pretraining Data**](https://arxiv.org/abs/2607.24717) | Per-example orchestration of retaining, cleaning and rewriting | [Code](https://github.com/GAIR-NLP/DataOrchestra) |
| 2025 | [**QuaDMix: Quality-Diversity Balanced Data Selection for Efficient LLM Pretraining**](https://arxiv.org/abs/2504.16511) | Joint quality filtering and domain-diversity balancing | — |
| 2025 | [**Meta-rater: A Multi-dimensional Data Selection Method for Pre-training Language Models**](https://arxiv.org/abs/2504.14194) | Multi-dimensional quality ratings for pretraining data selection | [Code](https://github.com/opendatalab/Meta-rater) |
| 2024 | [**Consent in Crisis: The Rapid Decline of the AI Data Commons**](https://arxiv.org/abs/2407.14933) | Web-data access and provenance | — |
| 2024 | [**A Survey on Data Selection for Language Models**](https://arxiv.org/abs/2402.16827) | Survey of data-selection methods | [Reading list](https://github.com/alon-albalak/data-selection-survey) |
| 2024 | [**QuRating: Selecting High-Quality Data for Training Language Models**](https://arxiv.org/abs/2402.09739) | Quality ratings for pretraining data selection | [Code](https://github.com/princeton-nlp/QuRating) |
| 2023 | [**Data Management For Training Large Language Models: A Survey**](https://arxiv.org/abs/2312.01700) | Survey of training-data management | [Reading list](https://github.com/ZigeW/data_management_LLM) |
| 2023 | [**Scaling Data-Constrained Language Models**](https://arxiv.org/abs/2305.16264) | Repeated epochs under limited fresh data | [Code](https://github.com/huggingface/datablations) |
| 2023 | [**SemDeDup: Data-efficient learning at web-scale through semantic deduplication**](https://arxiv.org/abs/2303.09540) | Semantic deduplication beyond lexical overlap | [Code](https://github.com/facebookresearch/SemDeDup) |
| 2023 | [**Data Selection for Language Models via Importance Resampling**](https://arxiv.org/abs/2302.03169) | Importance-resampling-based data selection | [Code](https://github.com/p-lambda/dsir) |
| 2022 | [**Scaling Laws and Interpretability of Learning from Repeated Data**](https://arxiv.org/abs/2205.10487) | Effects of repeated training data | — |
| 2022 | [**Quantifying Memorization Across Neural Language Models**](https://arxiv.org/abs/2202.07646) | Memorization and repeated sequences | — |
| 2021 | [**Deduplicating Training Data Makes Language Models Better**](https://arxiv.org/abs/2107.06499) | Exact and near-duplicate removal | [Code](https://github.com/google-research/deduplicate-text-datasets) |
| 2021 | [**Documenting Large Webtext Corpora: A Case Study on the Colossal Clean Crawled Corpus**](https://arxiv.org/abs/2104.08758) | C4 audit: content, bias and filtering effects | — |

[↑ Back to top](#top)

<a id="synthetic"></a>
### Synthetic data & rewriting

Generated text, source-conditioned rewriting, synthetic instructions and long sequences, and the limits of recursive or mixed synthetic training. For model recipes, see the Phi, Qwen, and Kimi reports above.

**10 papers**

| Year | Paper | Data-engineering focus | Code / official resource |
|:--:|:--|:--|:--|
| 2026 | [**Data-efficient pre-training by scaling synthetic megadocs**](https://arxiv.org/abs/2603.18534) | Long synthetic sequences built from rephrases and inserted rationales | [Code](https://github.com/marin-community/marin) |
| 2026 | [**FineInstructions: Scaling Synthetic Instructions to Pre-Training Scale**](https://arxiv.org/abs/2601.22146) | Document-grounded synthetic instructions for from-scratch pretraining | — |
| 2025 | [**Demystifying Synthetic Data in LLM Pre-training: A Systematic Study of Scaling Laws, Benefits, and Pitfalls**](https://arxiv.org/abs/2510.01631) | Controlled study of synthetic types, mixtures and scaling | — |
| 2025 | [**BeyondWeb: Lessons from Scaling Synthetic Data for Trillion-scale Pretraining**](https://arxiv.org/abs/2508.10975) | Source selection, rewriting style and generator choices at pretraining scale | — |
| 2025 | [**Rewriting Pre-Training Data Boosts LLM Performance in Math and Code**](https://arxiv.org/abs/2505.02881) | SwallowCode / SwallowMath: domain-specific rewriting for continued pretraining | [Code](https://github.com/rioyokotalab/swallow-code-math) |
| 2024 | [**Is Model Collapse Inevitable? Breaking the Curse of Recursion by Accumulating Real and Synthetic Data**](https://arxiv.org/abs/2404.01413) | Synthetic data accumulation and model collapse | — |
| 2024 | [**A Tale of Tails: Model Collapse as a Change of Scaling Laws**](https://arxiv.org/abs/2402.07043) | Scaling-law perspective on model collapse | — |
| 2024 | [**Rephrasing the Web: A Recipe for Compute and Data-Efficient Language Modeling**](https://arxiv.org/abs/2401.16380) | Source-conditioned rewriting for pretraining | — |
| 2023 | [**The Curse of Recursion: Training on Generated Data Makes Models Forget**](https://arxiv.org/abs/2305.17493) | Recursive synthetic training and model collapse | — |
| 2023 | [**TinyStories: How Small Can Language Models Be and Still Speak Coherent English?**](https://arxiv.org/abs/2305.07759) | Controlled synthetic stories for small-model pretraining | — |

[↑ Back to top](#top)

<a id="data-utilization"></a>
## ⚙️ Data utilization

<a id="mixtures"></a>
### Data mixtures & budget allocation

Domain construction, proxy experiments, data-mixing laws, online gradient-aware selection, and token/compute budgets. A proxy-optimal mixture is not automatically optimal at the target scale.

**20 papers**

| Year | Paper | Data-engineering focus | Code / official resource |
|:--:|:--|:--|:--|
| 2026 | [**OptiSelect: How does the Optimizer Shape Data Curriculum?**](https://arxiv.org/abs/2610.03432) | Optimizer-dependent geometry for online example selection | — |
| 2026 | [**Explaining Data Mixing Scaling Laws**](https://arxiv.org/abs/2606.08167) | Theory and empirical prediction of data-mixture effects | [Code](https://github.com/meiqwq/Explaining-Data-Mixing-Scaling-Laws) |
| 2026 | [**TANDEM: Bi-Level Data Mixture Optimization with Twin Networks**](https://arxiv.org/abs/2606.04401) | Bi-level mixture optimization with twin networks | — |
| 2025 | [**Language Models Improve When Pretraining Data Matches Target Tasks**](https://arxiv.org/abs/2507.12466) | Matching pretraining distributions to target tasks | — |
| 2025 | [**Scaling Laws for Optimal Data Mixtures**](https://arxiv.org/abs/2507.09404) | Scaling laws for predicting optimal data mixtures | — |
| 2025 | [**Nemotron-CLIMB: CLustering-based Iterative Data Mixture Bootstrapping for Language Model Pre-training**](https://arxiv.org/abs/2504.13161) | Semantic clustering and iterative proxy-based mixture optimization | — |
| 2025 | [**DataDecide: How to Predict Best Pretraining Data with Small Experiments**](https://arxiv.org/abs/2504.11393) | Small-scale experiments for selecting pretraining data | [Code](https://github.com/allenai/DataDecide) |
| 2025 | [**ToReMi: Topic-Aware Data Reweighting for Dynamic Pre-Training Data Selection**](https://arxiv.org/abs/2504.00695) | Topic-aware reweighting using observed learning dynamics | [Code](https://github.com/zxx000728/ToReMi) |
| 2025 | [**MixMin: Finding Data Mixtures via Convex Minimization**](https://arxiv.org/abs/2502.10510) | Convex optimization of data-mixture objectives | — |
| 2025 | [**Organize the Web: Constructing Domains Enhances Pre-Training Data Curation**](https://arxiv.org/abs/2502.10341) | WebOrganizer: topic and format annotations for domain-aware curation | [Code](https://github.com/CodeCreator/WebOrganizer) |
| 2025 | [**PiKE: Adaptive Data Mixing for Large-Scale Multi-Task Learning Under Low Gradient Conflicts**](https://arxiv.org/abs/2502.06244) | Online mixture adaptation using gradient statistics | — |
| 2025 | [**Optimizing Pretraining Data Mixtures with LLM-Estimated Utility**](https://arxiv.org/abs/2501.11747) | LLM-estimated utility for mixture design | — |
| 2024 | [**AutoScale: Scale-Aware Data Mixing for Pre-Training LLMs**](https://arxiv.org/abs/2407.20177) | Scale-dependent mixture selection | [Code](https://github.com/feiyang-k/AutoScale) |
| 2024 | [**RegMix: Data Mixture as Regression for Language Model Pre-training**](https://arxiv.org/abs/2407.01492) | Proxy regression for mixture optimization | [Code](https://github.com/sail-sg/regmix) |
| 2024 | [**BiMix: A Bivariate Data Mixing Law for Language Model Pretraining**](https://arxiv.org/abs/2405.14908) | Joint scaling of mixture and training volume | — |
| 2024 | [**Data Mixing Laws: Optimizing Data Mixtures by Predicting Language Modeling Performance**](https://arxiv.org/abs/2403.16952) | Predicting performance across domain mixtures | [Code](https://github.com/yegcjs/mixinglaws) |
| 2023 | [**Beyond Chinchilla-Optimal: Accounting for Inference in Language Model Scaling Laws**](https://arxiv.org/abs/2401.00448) | Training-inference trade-offs in data budgets | — |
| 2023 | [**DoReMi: Optimizing Data Mixtures Speeds Up Language Model Pretraining**](https://arxiv.org/abs/2305.10429) | Distributionally robust domain reweighting | [Code](https://github.com/sangmichaelxie/doremi) |
| 2022 | [**Training Compute-Optimal Large Language Models**](https://arxiv.org/abs/2203.15556) | Chinchilla · token and compute budgeting | — |
| 2020 | [**Scaling Laws for Neural Language Models**](https://arxiv.org/abs/2001.08361) | Model, data and compute allocation | — |

[↑ Back to top](#top)

<a id="schedules"></a>
### Training schedules, context & supervision

Dynamic curricula and replay, capability stages, learning-rate/data interactions, long-context selection, document packing, selective token loss, infilling, and metadata conditioning. These are grouped as choices about how existing data becomes training signals.

**13 papers**

| Year | Paper | Data-engineering focus | Code / official resource |
|:--:|:--|:--|:--|
| 2026 | [**Replay on Demand: An Emergent Curriculum for Balancing Adaptation and Forgetting in Continued Pretraining**](https://arxiv.org/abs/2609.40089) | Adaptive replay to balance learning and forgetting during continued pretraining | [Code](https://github.com/bethgelab/replay-on-demand) |
| 2026 | [**Holistic Data Scheduler for LLM Pre-training via Multi-Objective Reinforcement Learning**](https://arxiv.org/abs/2606.24133) | Multi-objective reinforcement learning for pretraining data scheduling | [Code](https://github.com/DANG-ai/LLM-Training-Holistic-Data-Schedule) |
| 2026 | [**RegMix-D: Dynamic Data Mixing via Proxy Training Trajectories**](https://arxiv.org/abs/2606.18663) | Dynamic mixture schedules from proxy training trajectories | — |
| 2026 | [**How Should LLMs Consume High-Quality Data? Optimal Data Scheduling via Quality-Aware Functional Scaling Laws**](https://arxiv.org/abs/2605.25698) | Quality-aware functional scaling laws for training-data schedules | — |
| 2026 | [**KoCo: Conditioning Language Model Pre-training on Knowledge Coordinates**](https://arxiv.org/abs/2604.12397) | Knowledge-coordinate metadata conditioning | — |
| 2025 | [**Beyond URLs: Metadata Diversity and Position for Efficient LLM Pretraining**](https://arxiv.org/abs/2511.21613) | Metadata diversity and placement | — |
| 2025 | [**How Learning Rate Decay Wastes Your Best Data in Curriculum-Based LLM Pretraining**](https://arxiv.org/abs/2511.18903) | Interaction of data quality and learning-rate schedules | — |
| 2025 | [**LADM: Long-context Training Data Selection with Attention-based Dependency Measurement for LLMs**](https://arxiv.org/abs/2503.02502) | Attention-based dependency measures for long-context data selection | [Code](https://github.com/ZNLP/LADM) |
| 2025 | [**Metadata Conditioning Accelerates Language Model Pre-training**](https://arxiv.org/abs/2501.01956) | Metadata prefixes for more efficient pretraining | [Code](https://github.com/princeton-pli/MeCo) |
| 2024 | [**Fewer Truncations Improve Language Modeling**](https://arxiv.org/abs/2404.10830) | Best-fit packing and reduced truncation | — |
| 2024 | [**Rho-1: Not All Tokens Are What You Need**](https://arxiv.org/abs/2404.07965) | Selective token supervision | [Code](https://github.com/microsoft/rho) |
| 2023 | [**In-context Pretraining: Language Modeling Beyond Document Boundaries**](https://arxiv.org/abs/2310.10638) | Retrieval-based document packing | [Code](https://github.com/swj0419/in-context-pretraining) |
| 2022 | [**Efficient Training of Language Models to Fill in the Middle**](https://arxiv.org/abs/2207.14255) | Fill-in-the-middle sequence transformations | — |

[↑ Back to top](#top)

<a id="evaluation"></a>
## 📏 Pretraining evaluation

Evaluation protocols, representative benchmarks, data attribution, corpus auditing, contamination checks, and proxy-transfer experiments. Compare data interventions at matched budgets and fixed prompting/scoring; the benchmark name alone is not a complete protocol.

**21 papers**

| Year | Paper | Data-engineering focus | Code / official resource |
|:--:|:--|:--|:--|
| 2026 | [**FineWeb-CLaR: Culture, Language, and Region Annotations for Benchmark-Aligned Corpus Auditing**](https://arxiv.org/abs/2609.25298) | Culture, language and regional annotations for corpus auditing | [Eval/data](https://github.com/Yusser96/FineWeb-CLaR) |
| 2026 | [**SynthSentry: Detecting Synthetic Data Contamination in Language Model Training Data**](https://arxiv.org/abs/2609.12353) | Corpus-level screening for synthetic-origin signals | — |
| 2026 | [**Measuring Task-Agnostic Training Data Influence Across Language Model Pretraining**](https://arxiv.org/abs/2608.13515) | Task-agnostic data influence measured across training trajectories | — |
| 2026 | [**GRASP: Geometry-aware Residual Alignment for Scalable Pretraining Data Attribution**](https://arxiv.org/abs/2606.06892) | Residual alignment and geometry for pretraining data attribution | [Code](https://github.com/MoonLight-Sherry/GRASP) |
| 2025 | [**Can Small Training Runs Reliably Guide Data Curation? Rethinking Proxy-Model Practice**](https://arxiv.org/abs/2512.24503) | Reliability and transfer limits of small-run data-curation decisions | — |
| 2025 | [**DyePack: Provably Flagging Test Set Contamination in LLMs Using Backdoors**](https://arxiv.org/abs/2505.23001) | Backdoor-based tests for detecting benchmark contamination | [Code](https://github.com/chengez/DyePack) |
| 2025 | [**The Emperor's New Clothes in Benchmarking? A Rigorous Examination of Mitigation Strategies for LLM Benchmark Data Contamination**](https://arxiv.org/abs/2503.16402) | Controlled assessment of benchmark contamination mitigation strategies | [Code](https://github.com/ASTRAL-Group/BDC-mitigation-assessment) |
| 2024 | [**OLMES: A Standard for Language Model Evaluations**](https://arxiv.org/abs/2406.08446) | Standardized base-model evaluation | [Eval/data](https://github.com/allenai/olmes) |
| 2024 | [**MMLU-Pro: A More Robust and Challenging Multi-Task Language Understanding Benchmark**](https://arxiv.org/abs/2406.01574) | More robust multitask evaluation | [Eval/data](https://github.com/TIGER-AI-Lab/MMLU-Pro) |
| 2024 | [**Language models scale reliably with over-training and on downstream tasks**](https://arxiv.org/abs/2403.08540) | Continuous losses and downstream scaling predictions | [Eval/data](https://github.com/mlfoundations/scaling) |
| 2023 | [**GPQA: A Graduate-Level Google-Proof Q&A Benchmark**](https://arxiv.org/abs/2311.12022) | Graduate-level question answering | [Eval/data](https://github.com/idavidrein/gpqa) |
| 2023 | [**Rethinking Benchmark and Contamination for Language Models with Rephrased Samples**](https://arxiv.org/abs/2311.04850) | Benchmark decontamination beyond exact matching | [Eval/data](https://github.com/lm-sys/llm-decontaminator) |
| 2023 | [**Detecting Pretraining Data from Large Language Models**](https://arxiv.org/abs/2310.16789) | Membership inference for detecting pretraining data | [Eval/data](https://github.com/swj0419/detect-pretrain-code) |
| 2023 | [**C-Eval: A Multi-Level Multi-Discipline Chinese Evaluation Suite for Foundation Models**](https://arxiv.org/abs/2305.08322) | Chinese multitask evaluation | [Eval/data](https://github.com/hkust-nlp/ceval) |
| 2023 | [**Is Your Code Generated by ChatGPT Really Correct? Rigorous Evaluation of Large Language Models for Code Generation**](https://arxiv.org/abs/2305.01210) | EvalPlus · stronger code correctness tests | [Eval/data](https://github.com/evalplus/evalplus) |
| 2023 | [**Are Emergent Abilities of Large Language Models a Mirage?**](https://arxiv.org/abs/2304.15004) | Metric sensitivity and apparent capability transitions | — |
| 2023 | [**Pythia: A Suite for Analyzing Large Language Models Across Training and Scaling**](https://arxiv.org/abs/2304.01373) | Controlled checkpoints for studying training-data effects | [Eval/data](https://github.com/EleutherAI/pythia) |
| 2021 | [**Training Verifiers to Solve Math Word Problems**](https://arxiv.org/abs/2110.14168) | GSM8K · mathematical reasoning evaluation | [Eval/data](https://github.com/openai/grade-school-math) |
| 2021 | [**Evaluating Large Language Models Trained on Code**](https://arxiv.org/abs/2107.03374) | HumanEval · functional correctness of code | [Eval/data](https://github.com/openai/human-eval) |
| 2021 | [**Measuring Mathematical Problem Solving With the MATH Dataset**](https://arxiv.org/abs/2103.03874) | MATH · mathematical problem solving | [Eval/data](https://github.com/hendrycks/math) |
| 2020 | [**Measuring Massive Multitask Language Understanding**](https://arxiv.org/abs/2009.03300) | MMLU · knowledge and reasoning evaluation | [Eval/data](https://github.com/hendrycks/test) |

[↑ Back to top](#top)

<a id="acknowledgments"></a>
## Acknowledgments

This collection is organized around [*Data Engineering in Large Language Model Pretraining: A Survey*](asset/LLM_Data_Survey_Revised.pdf) by Yudong Li, Ruiqi Zhou, Peiru Yang, Shiyi Zheng, Linlin Shen, Xintian Li, and Yongfeng Huang.

The presentation is inspired by [Awesome Multimodal Large Language Models](https://github.com/BradyFU/Awesome-Multimodal-Large-Language-Models). Suggestions for relevant papers and corrections are welcome through issues or pull requests.

[↑ Back to top](#top)

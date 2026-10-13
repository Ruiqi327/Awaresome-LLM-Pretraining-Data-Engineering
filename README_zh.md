<a id="top"></a>
<p align="center"><img src="asset/logo.png" alt="LLM Pretraining Data Engineering" width="900"></p>

<h1 align="center">Awesome Pretraining Data Engineering</h1>
<p align="center"><a href="README.md">English</a> · <b>简体中文</b></p>
<p align="center"><b>精选文献，关注塑造语言模型能力的数据。</b></p>
<p align="center">
  <img src="https://img.shields.io/badge/scope-text_pretraining-0f766e" alt="范围：文本预训练">
  <img src="https://img.shields.io/badge/papers-200-2563eb" alt="200 篇文献">
</p>
<p align="center"><a href="#reading-guide">阅读指南</a> · <a href="#technical-reports">技术报告</a> · <a href="asset/LLM_Data_Survey_Revised.pdf">我们的综述（PDF）</a></p>

**收录范围：** 以文本为主的预训练，包括代码与数学文本。当文档提取的结果用于文本训练时，也纳入相关工作。方法类目录不涵盖模型结构、仅用于 instruction tuning 的数据集、RLHF 配方及多模态对齐。

> **表格阅读说明。** 每张文献表均按时间倒序排列，同年内按首次提交日期排序。年份采用所链接 arXiv 记录的首次提交日期，可能不同于会议年份或 arXiv 编号中的年份。论文标题采用当前记录中的原文。**代码**表示研究实现；**代码/数据**包含配套数据；**数据集**链接指向发布的数据；**模型仓库**、**模型集合**、**处理流程**、**评估/数据**及 **Cookbook** 表示其他官方资源。仓库链接不意味着完整预训练流程或训练数据已经公开。**—** 表示本次整理未确认官方仓库，并不表示一定不存在。

## 目录

- [阅读指南与分类](#reading-guide)
- [1. 主要模型开发机构的技术报告](#technical-reports)
- [2. 语料构建](#corpus-construction)
  - [2.1 数据来源、采集与提取](#corpora)
  - [2.2 Deduplication、过滤与文档选择](#quality)
  - [2.3 合成数据与改写](#synthetic)
- [3. 数据利用](#data-utilization)
  - [3.1 领域划分、混合配比与预算](#mixtures)
  - [3.2 Curricula、训练调度与重复使用](#schedules)
  - [3.3 上下文构建与训练信号](#training-signals)
- [4. 预训练评估](#evaluation)
  - [4.1 Benchmark、评估协议与配方评估](#benchmarks)
  - [4.2 Data attribution、数据审计与污染检测](#auditing)
- [致谢](#acknowledgments)

<a id="reading-guide"></a>
## 🧭 阅读指南与分类

三个方法类主题对应**综述第 III–V 节**。Deduplication 与质量过滤合并为一张表，long-context 构建与训练信号合并为另一张表。模型系列技术报告单独集中列出。

| 类别 | 核心问题 | 收录内容 |
|:--|:--|:--|
| **[1. 技术报告](#technical-reports)** | 模型开发机构采用了怎样的数据配方？ | 模型系列的整体技术报告，即使涉及多个流程阶段，也集中归入本类。 |
| **[2. 语料构建](#corpus-construction)** | 采集、保留或生成哪些文本？ | 语料发布与提取；离线 deduplication、过滤与选择；合成与改写。 |
| **[3. 数据利用](#data-utilization)** | 如何将可用语料转化为训练暴露与训练信号？ | 领域混合与预算；排序、自适应采样与重复使用；上下文组织与 token 监督。 |
| **[4. 预训练评估](#evaluation)** | 如何比较数据选择，并验证其效果？ | Benchmark 与受控配方比较；attribution、provenance、memorization 及污染审计。 |

**归类规则：** 每篇论文按照主要贡献归入一个主要条目，相关主题通过交叉链接关联。离线文档过滤归入 **2.2**；领域层面的权重归入 **3.1**；训练时的样本选择与数据使用调度归入 **3.2**；样本/token 的 loss 加权与目标设计归入 **3.3**；对已有语料的审计归入 **4.2**。综合性综述作为入门阅读单独列于下方，不混入方法类表格。

**相关综述**

- **2026** · [**Data Mixing for Large Language Models Pretraining: A Survey and Outlook**](https://arxiv.org/abs/2604.16380) · —
- **2024** · [**A Survey on Data Selection for Language Models**](https://arxiv.org/abs/2402.16827) · [文献列表](https://github.com/alon-albalak/data-selection-survey)
- **2023** · [**Data Management For Training Large Language Models: A Survey**](https://arxiv.org/abs/2312.01700) · [文献列表](https://github.com/ZigeW/data_management_LLM)

| 你的问题 | 建议从这里开始 |
|:--|:--|
| 如何构建可复现的语料？ | [来源与提取](#corpora)：Dolma → FineWeb → FineWeb2 |
| 应该保留哪些文档？ | [过滤与选择](#quality)：Deduplicating Training Data → QuRating → BETR |
| 如何在不同来源之间分配 token？ | [混合配比与预算](#mixtures)：DoReMi → RegMix → 数据混合的 scaling laws |
| 何时使用或重复使用各类来源？ | [Curricula 与调度](#schedules)：重复数据的 scaling → RegMix-D → Replay on Demand |
| 如何组织文档并对 token 施加监督？ | [上下文与训练信号](#training-signals)：In-context Pretraining → Rho-1 → MeCo |
| 如何公平比较数据配方？ | [Benchmark 与评估协议](#benchmarks)：OLMES、DataComp-LM 和 DataDecide |
| 如何检查 provenance 与数据污染？ | [数据审计](#auditing)：C4 审计、membership inference 与污染检测 |

了解工业界实践时，可对比下方 **Llama 3**、**Qwen3**、**DeepSeek-V4 / V4.1-Flash**、**Kimi K2**、**GLM-5**、**Phi-4** 和 **OLMo 3** 报告中的数据章节。这些报告记录了实际配方，但不一定能单独识别每项数据决策的因果贡献。

<a id="technical-reports"></a>
## 🏢 1. 主要模型开发机构的技术报告

集中收录对语料来源、过滤、合成、混合配比与训练阶段的整体介绍。企业与主要开放模型研究团队的报告统一放在这里；模型结构与后训练方法不属于本目录的方法类收录范围。

**DeepSeek V4 收录说明：** V4 报告同时覆盖 **V4-Pro 和 V4-Flash**；**V4.1-Flash** 有独立报告。V4-Pro-Max 是 V4 共用报告中介绍的一种 reasoning-effort 模式。对于 V4.1-Flash，本目录关注文本/代码语料整理；报告中的 45T token 总量包含多模态数据。

**50 篇文献**

| 年份 | 论文 | 数据工程重点 | 代码 / 官方资源 |
|:--:|:--|:--|:--|
| 2026 | [**DeepSeek-V4.1-Flash: Pushing the Limits of KV Cache Compression**](https://arxiv.org/abs/2609.19969) | **DeepSeek** · **V4.1-Flash** · 多模态报告中披露的文本/代码语料整理更新与训练阶段 | [模型仓库](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash) · [官方 PDF](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash/blob/main/DeepSeek_V41_Tech_Report.pdf) |
| 2026 | [**Solar Open 2 Technical Report**](https://arxiv.org/abs/2607.20062) | **Upstage** · Solar Open 2 · 根据质量与稀缺程度整理数据，将 20T token 数据池精选为 10T token 混合语料 | — |
| 2026 | [**Nemotron 3 Ultra: Open, Efficient Mixture-of-Experts Hybrid Mamba-Transformer Model for Agentic Reasoning**](https://arxiv.org/abs/2606.15007) | **NVIDIA** · Nemotron 3 Ultra · 20T token 文本预训练、数据配方与 context extension | [模型仓库](https://github.com/NVIDIA-NeMo/Nemotron) |
| 2026 | [**DeepSeek-V4: Towards Highly Efficient Million-Token Context Intelligence**](https://arxiv.org/abs/2606.19348) | **DeepSeek** · **V4-Pro / V4-Flash** · 共用报告；32T+ 预训练 token、语料整理与 long-context training | [模型集合](https://huggingface.co/collections/deepseek-ai/deepseek-v4) · [官方 PDF](https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro/blob/main/DeepSeek_V4.pdf) |
| 2026 | [**Nemotron 3 Super: Open, Efficient Mixture-of-Experts Hybrid Mamba-Transformer Model for Agentic Reasoning**](https://arxiv.org/abs/2604.12374) | **NVIDIA** · Nemotron 3 Super · 25T token 预训练、公开数据集与训练阶段 | [模型仓库](https://github.com/NVIDIA-NeMo/Nemotron) |
| 2026 | [**GLM-5: from Vibe Coding to Agentic Engineering**](https://arxiv.org/abs/2602.15763) | **Z.ai / Tsinghua** · 预训练数据与分阶段训练的信息披露 | [模型仓库](https://github.com/zai-org/GLM-5) |
| 2025 | [**Nemotron 3 Nano: Open, Efficient Mixture-of-Experts Hybrid Mamba-Transformer Model for Agentic Reasoning**](https://arxiv.org/abs/2512.20848) | **NVIDIA** · Nemotron 3 Nano · 25T token 文本预训练，扩大不重复 token 的覆盖范围 | [模型仓库](https://github.com/NVIDIA-NeMo/Nemotron) |
| 2025 | [**Olmo 3**](https://arxiv.org/abs/2512.13961) | **Ai2** · 文档提取与 mid-training 数据 | [训练代码](https://github.com/allenai/Olmo-core) |
| 2025 | [**GLM-4.5: Agentic, Reasoning, and Coding (ARC) Foundation Models**](https://arxiv.org/abs/2508.06471) | **Z.ai / Tsinghua** · 分阶段预训练与重点领域配置 | [模型仓库](https://github.com/zai-org/GLM-4.5) |
| 2025 | [**Kimi K2: Open Agentic Intelligence**](https://arxiv.org/abs/2507.20534) | **Moonshot AI** · 文档改写与数据质量 | [模型仓库](https://github.com/MoonshotAI/Kimi-K2) |
| 2025 | [**Qwen3 Technical Report**](https://arxiv.org/abs/2505.09388) | **Alibaba** · Qwen3 · 数据标注与分阶段预训练 | [模型仓库](https://github.com/QwenLM/Qwen3) |
| 2025 | [**Gemma 3 Technical Report**](https://arxiv.org/abs/2503.19786) | **Google DeepMind** · 多模态报告中披露的文本数据信息 | [模型仓库](https://github.com/google-deepmind/gemma) |
| 2025 | [**SmolLM2: When Smol Goes Big -- Data-Centric Training of a Small Language Model**](https://arxiv.org/abs/2502.02737) | **Hugging Face** · SmolLM2 · 以数据为中心的小模型训练与公开配方 | [模型仓库](https://github.com/huggingface/smollm) |
| 2024 | [**2 OLMo 2 Furious**](https://arxiv.org/abs/2501.00656) | **Ai2** · 分阶段训练与 data annealing | [训练代码](https://github.com/allenai/Olmo-core) |
| 2024 | [**DeepSeek-V3 Technical Report**](https://arxiv.org/abs/2412.19437) | **DeepSeek** · 语料扩展与 context extension | [模型仓库](https://github.com/deepseek-ai/DeepSeek-V3) |
| 2024 | [**Qwen2.5 Technical Report**](https://arxiv.org/abs/2412.15115) | **Alibaba** · Qwen2.5 · 合成数据与领域数据 | [模型仓库](https://github.com/QwenLM/Qwen3) |
| 2024 | [**Phi-4 Technical Report**](https://arxiv.org/abs/2412.08905) | **Microsoft** · 大量使用合成数据的预训练 | [Cookbook](https://github.com/microsoft/PhiCookBook) |
| 2024 | [**Qwen2.5-Coder Technical Report**](https://arxiv.org/abs/2409.12186) | **Alibaba** · 代码语料与合成数据 | [模型仓库](https://github.com/QwenLM/Qwen3-Coder) |
| 2024 | [**Qwen2.5-Math Technical Report: Toward Mathematical Expert Model via Self-Improvement**](https://arxiv.org/abs/2409.12122) | **Alibaba** · 数学数据构建 | [模型仓库](https://github.com/QwenLM/Qwen2.5-Math) |
| 2024 | [**Gemma 2: Improving Open Language Models at a Practical Size**](https://arxiv.org/abs/2408.00118) | **Google DeepMind** · 基于 teacher distribution 的预训练目标 | [模型仓库](https://github.com/google-deepmind/gemma) |
| 2024 | [**The Llama 3 Herd of Models**](https://arxiv.org/abs/2407.21783) | **Meta** · Llama 3 · 数据过滤、混合配比与 annealing | [模型仓库](https://github.com/meta-llama/llama3) |
| 2024 | [**Qwen2 Technical Report**](https://arxiv.org/abs/2407.10671) | **Alibaba** · Qwen2 · 数据质量与领域覆盖 | [模型仓库](https://github.com/QwenLM/Qwen3) |
| 2024 | [**ChatGLM: A Family of Large Language Models from GLM-130B to GLM-4 All Tools**](https://arxiv.org/abs/2406.12793) | **Z.ai / Tsinghua** · 多语言预训练语料 | [模型仓库](https://github.com/zai-org/GLM-4) |
| 2024 | [**DeepSeek-Coder-V2: Breaking the Barrier of Closed-Source Models in Code Intelligence**](https://arxiv.org/abs/2406.11931) | **DeepSeek** · 代码 continued pretraining 与 infilling | [模型仓库](https://github.com/deepseek-ai/DeepSeek-Coder-V2) |
| 2024 | [**Nemotron-4 340B Technical Report**](https://arxiv.org/abs/2406.11704) | **NVIDIA** · 语料混合与分阶段训练 | — |
| 2024 | [**DeepSeek-V2: A Strong, Economical, and Efficient Mixture-of-Experts Language Model**](https://arxiv.org/abs/2405.04434) | **DeepSeek** · 跨快照 deduplication 与过滤 | [模型仓库](https://github.com/deepseek-ai/DeepSeek-V2) |
| 2024 | [**Phi-3 Technical Report: A Highly Capable Language Model Locally on Your Phone**](https://arxiv.org/abs/2404.14219) | **Microsoft** · 数据质量与合成文本 | [Cookbook](https://github.com/microsoft/PhiCookBook) |
| 2024 | [**MiniCPM: Unveiling the Potential of Small Language Models with Scalable Training Strategies**](https://arxiv.org/abs/2404.06395) | **OpenBMB** · 数据调度与 warmup-stable-decay | [模型仓库](https://github.com/OpenBMB/MiniCPM) |
| 2024 | [**InternLM2 Technical Report**](https://arxiv.org/abs/2403.17297) | **Shanghai AI Lab** · deduplication 与 continued pretraining | [模型仓库](https://github.com/InternLM/InternLM) |
| 2024 | [**Gemma: Open Models Based on Gemini Research and Technology**](https://arxiv.org/abs/2403.08295) | **Google DeepMind** · 数据过滤与治理 | [模型仓库](https://github.com/google-deepmind/gemma) |
| 2024 | [**Yi: Open Foundation Models by 01.AI**](https://arxiv.org/abs/2403.04652) | **01.AI** · 质量过滤与数据集中程度 | [模型仓库](https://github.com/01-ai/Yi) |
| 2024 | [**StarCoder 2 and The Stack v2: The Next Generation**](https://arxiv.org/abs/2402.19173) | **BigCode** · The Stack v2 与代码数据 provenance | [模型仓库](https://github.com/bigcode-project/starcoder2) |
| 2024 | [**OLMo: Accelerating the Science of Language Models**](https://arxiv.org/abs/2402.00838) | **Ai2** · 完全开放的训练过程与语料 provenance | [训练代码](https://github.com/allenai/OLMo) |
| 2024 | [**DeepSeek LLM: Scaling Open-Source Language Models with Longtermism**](https://arxiv.org/abs/2401.02954) | **DeepSeek** · 早期语料与 scaling 配方 | [模型仓库](https://github.com/deepseek-ai/DeepSeek-LLM) |
| 2023 | [**The Falcon Series of Open Language Models**](https://arxiv.org/abs/2311.16867) | **TII** · 以网页数据为主的训练配方 | — |
| 2023 | [**Qwen Technical Report**](https://arxiv.org/abs/2309.16609) | **Alibaba** · Qwen · 多语言语料构建 | [模型仓库](https://github.com/QwenLM/Qwen) |
| 2023 | [**Baichuan 2: Open Large-scale Language Models**](https://arxiv.org/abs/2309.10305) | **Baichuan** · 多语言语料与数据处理 | [模型仓库](https://github.com/baichuan-inc/Baichuan2) |
| 2023 | [**Textbooks Are All You Need II: phi-1.5 technical report**](https://arxiv.org/abs/2309.05463) | **Microsoft** · Phi-1.5 · 合成文本与网页文本的混合 | — |
| 2023 | [**Code Llama: Open Foundation Models for Code**](https://arxiv.org/abs/2308.12950) | **Meta** · 代码专门化训练与 infilling | [模型仓库](https://github.com/meta-llama/codellama) |
| 2023 | [**Llama 2: Open Foundation and Fine-Tuned Chat Models**](https://arxiv.org/abs/2307.09288) | **Meta** · Llama 2 · 扩充预训练数据 | [模型仓库](https://github.com/meta-llama/llama) |
| 2023 | [**Textbooks Are All You Need**](https://arxiv.org/abs/2306.11644) | **Microsoft** · Phi-1 · 基于教科书风格合成文本的预训练 | — |
| 2023 | [**PaLM 2 Technical Report**](https://arxiv.org/abs/2305.10403) | **Google DeepMind** · PaLM 2 · 数据平衡与多语言覆盖 | — |
| 2023 | [**StarCoder: may the source be with you!**](https://arxiv.org/abs/2305.06161) | **BigCode** · 代码数据整理与 fill-in-the-middle | [模型仓库](https://github.com/bigcode-project/starcoder) |
| 2023 | [**GPT-4 Technical Report**](https://arxiv.org/abs/2303.08774) | **OpenAI** · GPT-4 · 有限的预训练信息披露 | — |
| 2023 | [**LLaMA: Open and Efficient Foundation Language Models**](https://arxiv.org/abs/2302.13971) | **Meta** · LLaMA · 基于公开来源的语料构建 | [模型仓库](https://github.com/meta-llama/llama) |
| 2022 | [**BLOOM: A 176B-Parameter Open-Access Multilingual Language Model**](https://arxiv.org/abs/2211.05100) | **BigScience** · 多语言语料治理与混合配比 | [训练代码](https://github.com/bigscience-workshop/bigscience) |
| 2022 | [**GLM-130B: An Open Bilingual Pre-trained Model**](https://arxiv.org/abs/2210.02414) | **Z.ai / Tsinghua** · 双语数据 | [模型仓库](https://github.com/zai-org/GLM-130B) |
| 2022 | [**PaLM: Scaling Language Modeling with Pathways**](https://arxiv.org/abs/2204.02311) | **Google DeepMind** · PaLM · 多语言与代码数据混合 | — |
| 2021 | [**Scaling Language Models: Methods, Analysis & Insights from Training Gopher**](https://arxiv.org/abs/2112.11446) | **Google DeepMind** · Gopher · 启发式质量过滤 | — |
| 2020 | [**Language Models are Few-Shot Learners**](https://arxiv.org/abs/2005.14165) | **OpenAI** · GPT-3 · 语料过滤与混合配比设计 | [评估/数据](https://github.com/openai/gpt-3) |

[↑ 返回顶部](#top)

<a id="corpus-construction"></a>
## 🧱 2. 语料构建

构建可用语料：采集和提取文本，决定保留哪些文档，并生成补充文本。本类对应**综述第 III 节**。

<a id="corpora"></a>
### 2.1 数据来源、采集与提取

语料发布、定向网页采集、处理流程及文档到文本的提取。以语料为主要贡献的论文归入本类，即使其流程也包含过滤或改写。FineWeb-Edu 由 FineWeb 论文覆盖；The Stack v2 由[技术报告](#technical-reports)中的 StarCoder 2 覆盖。DataComp-LM 归入[配方评估](#benchmarks)，以体现其受控 benchmark 的主要贡献。

**24 篇文献**

| 年份 | 论文 | 数据工程重点 | 代码 / 官方资源 |
|:--:|:--|:--|:--|
| 2026 | [**Host-Aware Web Crawling for LLM Pretraining Data**](https://arxiv.org/abs/2610.13321) | 根据数据质量分配不同网页主机的抓取深度 | — |
| 2026 | [**ReScraper: Unified Scraping and Cleaning of Web Data for Effective LLM Pretraining**](https://arxiv.org/abs/2609.34287) | 通过统一的学习式方法完成网页提取、清洗与改写 | [代码](https://github.com/cxcscmu/ReScraper) |
| 2026 | [**Data Scout: Targeted Web Crawling for Domain-Specific Pretraining Corpora**](https://arxiv.org/abs/2609.05766) | 面向领域预训练语料的定向网页抓取 | [代码](https://github.com/IBM/data-scout) |
| 2026 | [**SPT: Skills as Pre-Training Data for Agentic Language Models**](https://arxiv.org/abs/2608.26563) | SkillCorpus / SPT：将公开 skill 包用作 causal-LM 的 mid-training 数据，并按引用关系组织文件 | — |
| 2026 | [**GPT-NL Public Corpus: A Permissively Licensed, Dutch-First Dataset for LLM Pre-training**](https://arxiv.org/abs/2604.00920) | GPT-NL：采用宽松许可、以荷兰语为主的数据集，结合整理后的多语言与代码来源 | [处理流程](https://github.com/GPT-NL/data-curation-pipeline) |
| 2025 | [**Mangosteen: An Open Thai Corpus for Language Model Pretraining**](https://arxiv.org/abs/2507.14664) | Mangosteen：针对泰语的网页过滤、非网页数据来源与可复现语料处理流程 | [处理流程](https://github.com/vistec-AI/Mangosteen) |
| 2025 | [**FineWeb2: One Pipeline to Scale Them All -- Adapting Pre-Training Data Processing to Every Language**](https://arxiv.org/abs/2506.20920) | 覆盖 1,000 多种语言的多语言语料整理 | [处理流程](https://github.com/huggingface/datatrove) |
| 2025 | [**The Common Pile v0.1: An 8TB Dataset of Public Domain and Openly Licensed Text**](https://arxiv.org/abs/2506.05209) | 来自公有领域及开放许可来源的语料，提供可复现的数据处理与训练混合配比 | [处理流程](https://github.com/r-three/common-pile) |
| 2025 | [**Ultra-FineWeb: Efficient Data Filtering and Verification for High-Quality LLM Training Data**](https://arxiv.org/abs/2505.05427) | Ultra-FineWeb：结合高效种子数据选择、过滤与数据验证的中英文网页语料 | [数据集](https://huggingface.co/datasets/openbmb/UltraFineWeb) |
| 2025 | [**olmOCR: Unlocking Trillions of Tokens in PDFs with Vision Language Models**](https://arxiv.org/abs/2502.18443) | 用于文本预训练的 PDF 内容提取 | [代码](https://github.com/allenai/olmocr) |
| 2024 | [**Nemotron-CC: Transforming Common Crawl into a Refined Long-Horizon Pretraining Dataset**](https://arxiv.org/abs/2412.02595) | Classifier ensemble 与合成改写 | [处理流程](https://github.com/NVIDIA-NeMo/Curator) |
| 2024 | [**RedPajama: an Open Dataset for Training Large Language Models**](https://arxiv.org/abs/2411.12372) | 带有可复用质量标注的网页语料 | [处理流程](https://github.com/togethercomputer/RedPajama-Data) |
| 2024 | [**MinerU: An Open-Source Solution for Precise Document Content Extraction**](https://arxiv.org/abs/2409.18839) | 文档内容提取与阅读顺序恢复 | [代码](https://github.com/opendatalab/MinerU) |
| 2024 | [**The FineWeb Datasets: Decanting the Web for the Finest Text Data at Scale**](https://arxiv.org/abs/2406.17557) | FineWeb / FineWeb-Edu · 网页文本处理与教育价值过滤 | [处理流程](https://github.com/huggingface/datatrove) |
| 2024 | [**Cleaner Pretraining Corpus Curation with Neural Web Scraping**](https://arxiv.org/abs/2402.14652) | NeuScraper：学习式网页正文提取，获得更干净的预训练文本 | [代码](https://github.com/OpenMatch/NeuScraper) |
| 2024 | [**Dolma: an Open Corpus of Three Trillion Tokens for Language Model Pretraining Research**](https://arxiv.org/abs/2402.00159) | 开放的 3T token 语料与处理流程 | [处理流程](https://github.com/allenai/dolma) |
| 2023 | [**MathPile: A Billion-Token-Scale Pretraining Corpus for Math**](https://arxiv.org/abs/2312.17120) | MathPile：面向数学的数据采集、清洗、deduplication 与 benchmark decontamination | [代码/数据](https://github.com/GAIR-NLP/MathPile) |
| 2023 | [**Data-Juicer: A One-Stop Data Processing System for Large Language Models**](https://arxiv.org/abs/2309.02033) | 可组合的数据处理与配方分析 | [处理流程](https://github.com/datajuicer/data-juicer) |
| 2023 | [**Nougat: Neural Optical Understanding for Academic Documents**](https://arxiv.org/abs/2308.13418) | 学术 PDF 到文本的提取 | [代码](https://github.com/facebookresearch/nougat) |
| 2023 | [**The RefinedWeb Dataset for Falcon LLM: Outperforming Curated Corpora with Web Data, and Web Data Only**](https://arxiv.org/abs/2306.01116) | 网页提取、过滤与 deduplication | — |
| 2022 | [**The Stack: 3 TB of permissively licensed source code**](https://arxiv.org/abs/2211.15533) | 采用宽松许可的源代码语料 | — |
| 2020 | [**The Pile: An 800GB Dataset of Diverse Text for Language Modeling**](https://arxiv.org/abs/2101.00027) | 多样化的多来源文本语料 | [处理流程](https://github.com/EleutherAI/the-pile) |
| 2020 | [**CLUECorpus2020: A Large-scale Chinese Corpus for Pre-training Language Model**](https://arxiv.org/abs/2003.01355) | 中文预训练语料 | [评估/数据](https://github.com/CLUEbenchmark/CLUECorpus2020) |
| 2019 | [**Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer**](https://arxiv.org/abs/1910.10683) | C4 语料构建；收录其数据工作，不涉及模型结构 | [代码](https://github.com/google-research/text-to-text-transfer-transformer) |

[↑ 返回顶部](#top)

<a id="quality"></a>
### 2.2 Deduplication、过滤与文档选择

决定哪些文档进入语料的方法：重复内容移除、质量评分、考虑多样性的选择及目标分布匹配。重复训练暴露归入[训练调度](#schedules)；provenance 与 memorization 研究归入[数据审计](#auditing)。

**24 篇文献**

| 年份 | 论文 | 数据工程重点 | 代码 / 官方资源 |
|:--:|:--|:--|:--|
| 2026 | [**Adapting English Quality Classifiers for Multilingual LLM Pretraining Data Selection**](https://arxiv.org/abs/2610.11585) | 将英语质量评分迁移到多语言语料选择 | — |
| 2026 | [**Fisher-Guided Submodular Data Selection for Continual Pre-Training of Large Language Models**](https://arxiv.org/abs/2610.02593) | 面向 continued pretraining 的 Fisher-aware submodular 文档选择，以限制遗忘 | — |
| 2026 | [**Towards Scalable Data Diversification for Language Model Pretraining via Leverage Score Sampling**](https://arxiv.org/abs/2609.32484) | 通过 leverage score sampling 提升 embedding 空间中的数据多样性 | [代码](https://github.com/mazailin/LevDataDiversity) |
| 2026 | [**AutoData: Agentic Search for Pre-training Data Selection**](https://arxiv.org/abs/2609.19754) | 通过 agentic search 搜索可执行的数据选择程序 | [代码](https://github.com/WecoAI/AutoData) |
| 2026 | [**Edu-QuRating: Multi-Dimensional Educational Data Curation with Distilled Pairwise Judgements**](https://arxiv.org/abs/2609.09425) | 将教育场景的成对评价标准蒸馏为文档质量评分器 | [代码](https://github.com/AI-for-Education/edu-qurating) |
| 2026 | [**Scalable Frequency- and Length-Aware Subdocument Deduplication for Large Language Model Pretraining**](https://arxiv.org/abs/2608.03089) | 全局 subdocument 重复检测，根据频次与长度设置副本保留预算 | — |
| 2026 | [**PUFFER: Incremental Fuzzy Deduplication for Continuously Evolving Corpora**](https://arxiv.org/abs/2608.28622) | PUFFER：增量 MinHash-LSH deduplication，支持有界内存使用与数据集撤回 | [代码](https://github.com/Zyphra/puffer) |
| 2026 | [**DataOrchestra: Learning to Orchestrate Per-Example Curation of Pretraining Data**](https://arxiv.org/abs/2607.24717) | 逐样本协调保留、清洗与改写操作 | [代码](https://github.com/GAIR-NLP/DataOrchestra) |
| 2026 | [**Spokes: Optimizing for Diverse Pretraining Data Selection**](https://arxiv.org/abs/2606.15216) | SPOKES：集合层面的多样性优化，可结合文档质量评分 | — |
| 2026 | [**FOLD: Fuzzy Online Deduplication for Very Large Evolving Datasets via Approximate Nearest Neighbor Search**](https://arxiv.org/abs/2606.03001) | FOLD：利用增量更新的 approximate-neighbor 索引进行在线 fuzzy deduplication | — |
| 2026 | [**MIRA: Mid-training Rubric Anchoring for Source-Aware Data Selection**](https://arxiv.org/abs/2605.30288) | MIRA：针对数据来源发现评价标准，并蒸馏评分器，用于面向代码的 mid-training | — |
| 2026 | [**Toward Cross-Lingual Quality Classifiers for Multilingual Pretraining Data Selection**](https://arxiv.org/abs/2604.20549) | 多语言数据汇集、cross-lingual quality classifier 与保留率校准 | — |
| 2026 | [**Target-Oriented Pretraining Data Selection via Neuron-Activated Graph**](https://arxiv.org/abs/2604.15706) | NAG：利用稀疏 neuron-activation graph 进行面向目标的文档排序 | [代码](https://github.com/asillycat/NAG) |
| 2026 | [**An Empirical Study on Influence-Based Pretraining Data Selection for Code Large Language Models**](https://arxiv.org/abs/2604.07769) | 基于 influence 的代码数据过滤，结合任务专属 validation loss 与阶段相关效用 | [代码](https://github.com/ZZR0/DIScore) |
| 2026 | [**Aligning the Query Space: Greedy Information Projection for Language Model Data Selection**](https://arxiv.org/abs/2603.13790) | GIP：与查询对齐的子集选择，平衡质量与多样性；包含预训练实验 | — |
| 2025 | [**BLISS: A Lightweight Bilevel Influence Scoring Method for Data Selection in Language Model Pretraining**](https://arxiv.org/abs/2510.06048) | BLISS：利用从头训练的 proxy model 进行轻量 bilevel influence scoring | [代码](https://github.com/MingruiLiu-ML-Lab/BLISS-Bilevel-Data-Selection) |
| 2025 | [**Removing Noise, not Finding Gold: Quality Filtering for Large-Scale Pretraining**](https://arxiv.org/abs/2510.00866) | 对 classifier-based quality filtering 及基于优化的数据质量估计进行受控分析 | — |
| 2025 | [**Language Models Improve When Pretraining Data Matches Target Tasks**](https://arxiv.org/abs/2507.12466) | 使预训练数据分布与目标任务匹配 | — |
| 2025 | [**QuaDMix: Quality-Diversity Balanced Data Selection for Efficient LLM Pretraining**](https://arxiv.org/abs/2504.16511) | 联合优化质量过滤与领域多样性平衡 | — |
| 2025 | [**Meta-rater: A Multi-dimensional Data Selection Method for Pre-training Language Models**](https://arxiv.org/abs/2504.14194) | 用于预训练数据选择的多维质量评分 | [代码](https://github.com/opendatalab/Meta-rater) |
| 2024 | [**QuRating: Selecting High-Quality Data for Training Language Models**](https://arxiv.org/abs/2402.09739) | 用于预训练数据选择的质量评分 | [代码](https://github.com/princeton-nlp/QuRating) |
| 2023 | [**SemDeDup: Data-efficient learning at web-scale through semantic deduplication**](https://arxiv.org/abs/2303.09540) | 超越词面重叠的 semantic deduplication | [代码](https://github.com/facebookresearch/SemDeDup) |
| 2023 | [**Data Selection for Language Models via Importance Resampling**](https://arxiv.org/abs/2302.03169) | 基于 importance resampling 的数据选择 | [代码](https://github.com/p-lambda/dsir) |
| 2021 | [**Deduplicating Training Data Makes Language Models Better**](https://arxiv.org/abs/2107.06499) | 移除完全重复与近似重复数据 | [代码](https://github.com/google-research/deduplicate-text-datasets) |

[↑ 返回顶部](#top)

<a id="synthetic"></a>
### 2.3 合成数据与改写

生成或转换训练文本的方法，包括以源文本为条件的改写、长文档合成，以及 synthetic-data scaling 和 model collapse 研究。将已有文档组织成序列的工作归入[上下文构建](#training-signals)；特定模型的数据配方仍归入[技术报告](#technical-reports)。

**17 篇文献**

| 年份 | 论文 | 数据工程重点 | 代码 / 官方资源 |
|:--:|:--|:--|:--|
| 2026 | [**How Much Is an AI Token Worth? Scaling Laws for Wild AI-Generated Web Text**](https://arxiv.org/abs/2609.40295) | WildAI：在受控混合配比下研究自然出现的 AI 生成网页文本的 scaling 效应 | [代码/数据](https://github.com/pangramlabs/WildAI) |
| 2026 | [**Synthetic Pre-pretraining Survives Scale, but Not as a Grammatical Prior**](https://arxiv.org/abs/2609.39827) | 在 500M–7B 模型上研究合成 pre-pretraining；检验 retrieval transfer 与 grammatical transfer | [代码](https://github.com/gucci-j/verify-ppt-at-scale) |
| 2026 | [**It's All Training: A Fully Synthetic Single-Stage Recipe for LLMs**](https://arxiv.org/abs/2609.37891) | SYNTH：以 Wikipedia 种子内容为依据进行合成扩充，用于单阶段语言模型训练 | [数据集](https://huggingface.co/datasets/PleIAs/SYNTH) |
| 2026 | [**Self-Play Pretraining with Zero Data**](https://arxiv.org/abs/2609.30063) | 概念验证：通过 self-play 生成可执行程序与字节序列，用于从头预训练 | — |
| 2026 | [**REER-PT: Reverse-Engineered Reasoning for Perplexity-Guided Pre-training Data Augmentation**](https://arxiv.org/abs/2608.30627) | REER-PT：利用 perplexity 与泄漏控制，在原始文本中插入稀疏推理标注 | — |
| 2026 | [**Unfolding Scientific Papers into Multi-Turn Generation Trajectories for Continued Pre-Training**](https://arxiv.org/abs/2608.25826) | 由 teacher 重建写作计划与思考过程，增强保留原文的科学论文，用于 continued pretraining | — |
| 2026 | [**UltraX: Refining Pre-Training Data at Scale with Adaptive Programmatic Editing**](https://arxiv.org/abs/2607.08646) | UltraX：通过经验证的插入、删除与修改程序，实现可扩展的文本精炼 | [代码](https://github.com/OpenBMB/UltraX) |
| 2026 | [**Data-efficient pre-training by scaling synthetic megadocs**](https://arxiv.org/abs/2603.18534) | 通过改写与插入推理说明构建长合成序列 | [代码](https://github.com/marin-community/marin) |
| 2026 | [**FineInstructions: Scaling Synthetic Instructions to Pre-Training Scale**](https://arxiv.org/abs/2601.22146) | 以文档为依据生成合成指令，用于从头预训练 | — |
| 2025 | [**Demystifying Synthetic Data in LLM Pre-training: A Systematic Study of Scaling Laws, Benefits, and Pitfalls**](https://arxiv.org/abs/2510.01631) | 关于合成数据类型、混合配比与 scaling 的受控研究 | — |
| 2025 | [**BeyondWeb: Lessons from Scaling Synthetic Data for Trillion-scale Pretraining**](https://arxiv.org/abs/2508.10975) | 在预训练规模下研究来源选择、改写风格与生成器选择 | — |
| 2025 | [**Rewriting Pre-Training Data Boosts LLM Performance in Math and Code**](https://arxiv.org/abs/2505.02881) | SwallowCode / SwallowMath：面向特定领域的改写，用于 continued pretraining | [代码](https://github.com/rioyokotalab/swallow-code-math) |
| 2024 | [**Is Model Collapse Inevitable? Breaking the Curse of Recursion by Accumulating Real and Synthetic Data**](https://arxiv.org/abs/2404.01413) | 合成数据累积与 model collapse | — |
| 2024 | [**A Tale of Tails: Model Collapse as a Change of Scaling Laws**](https://arxiv.org/abs/2402.07043) | 从 scaling law 角度研究 model collapse | — |
| 2024 | [**Rephrasing the Web: A Recipe for Compute and Data-Efficient Language Modeling**](https://arxiv.org/abs/2401.16380) | 以源文本为条件进行改写，用于预训练 | — |
| 2023 | [**The Curse of Recursion: Training on Generated Data Makes Models Forget**](https://arxiv.org/abs/2305.17493) | 递归合成数据训练与 model collapse | — |
| 2023 | [**TinyStories: How Small Can Language Models Be and Still Speak Coherent English?**](https://arxiv.org/abs/2305.07759) | 用于小模型预训练的受控合成故事 | — |

[↑ 返回顶部](#top)

<a id="data-utilization"></a>
## ⚙️ 3. 数据利用

将可用语料转化为训练暴露与预测目标。本类对应**综述第 IV 节**。

<a id="mixtures"></a>
### 3.1 领域划分、混合配比与预算

定义来源/主题领域，选择采样权重，并分配 token 或计算预算。影响多语言数据表示和压缩效率的 tokenizer 训练数据配比也纳入本类。包括静态与自适应 domain reweighting，例如 DoReMi、PiKE 和 ToReMi。以数据使用顺序或训练轨迹为核心的方法归入[训练调度](#schedules)；检验小规模实验结论能否迁移的研究归入[配方评估](#benchmarks)。

**23 篇文献**

| 年份 | 论文 | 数据工程重点 | 代码 / 官方资源 |
|:--:|:--|:--|:--|
| 2026 | [**Hubs or Fringes: Pretraining Data Selection via Web Graph Centrality**](https://arxiv.org/abs/2606.11499) | WebGraphMix：利用图结构与质量评分，混合中心和边缘网页主机的数据 | [代码](https://github.com/princeton-pli/WebGraphMix) |
| 2026 | [**Explaining Data Mixing Scaling Laws**](https://arxiv.org/abs/2606.08167) | 数据混合效果的理论分析与经验预测 | [代码](https://github.com/meiqwq/Explaining-Data-Mixing-Scaling-Laws) |
| 2026 | [**TANDEM: Bi-Level Data Mixture Optimization with Twin Networks**](https://arxiv.org/abs/2606.04401) | 基于 twin networks 的 bilevel 混合配比优化 | — |
| 2026 | [**Always Learning, Always Mixing: Efficient and Simple Data Mixing All The Time**](https://arxiv.org/abs/2605.15220) | OP-Mix：在当前模型上通过 low-rank adapter 插值进行 on-policy 混合配比搜索 | — |
| 2026 | [**Capacity-Aware Mixture Law Enables Efficient LLM Data Optimization**](https://arxiv.org/abs/2603.08022) | CAMEL：结合 capacity-aware mixture law 与 loss-to-benchmark 预测，实现跨规模数据优化 | — |
| 2026 | [**Decouple Searching from Training: Scaling Data Mixing via Model Merging for Large Language Model Pre-training**](https://arxiv.org/abs/2602.00747) | DeMix：使用 model-merging proxy 搜索混合配比，并公开 22T token 语料 | [代码/数据](https://github.com/Lucius-lsr/DeMix) |
| 2026 | [**MergeMix: Optimizing Mid-Training Data Mixtures via Learnable Model Merging**](https://arxiv.org/abs/2601.17858) | MergeMix：以可学习的模型合并权重作为 mid-training 数据比例的 proxy | — |
| 2026 | [**TREX: Tokenizer Regression for Optimal Data Mixture**](https://arxiv.org/abs/2601.13588) | TREX：通过 proxy-tokenizer regression 优化多语言 tokenizer 训练数据配比 | [代码](https://github.com/HanGyeol-Yoo/TReX) |
| 2025 | [**Scaling Laws for Optimal Data Mixtures**](https://arxiv.org/abs/2507.09404) | 预测最优数据混合配比的 scaling laws | — |
| 2025 | [**Nemotron-CLIMB: CLustering-based Iterative Data Mixture Bootstrapping for Language Model Pre-training**](https://arxiv.org/abs/2504.13161) | Semantic clustering 与基于 proxy 的迭代混合配比优化 | — |
| 2025 | [**ToReMi: Topic-Aware Data Reweighting for Dynamic Pre-Training Data Selection**](https://arxiv.org/abs/2504.00695) | 根据观测到的学习动态进行 topic-aware reweighting | [代码](https://github.com/zxx000728/ToReMi) |
| 2025 | [**MixMin: Finding Data Mixtures via Convex Minimization**](https://arxiv.org/abs/2502.10510) | 数据混合目标的 convex optimization | — |
| 2025 | [**Organize the Web: Constructing Domains Enhances Pre-Training Data Curation**](https://arxiv.org/abs/2502.10341) | WebOrganizer：利用主题与格式标注进行 domain-aware 数据整理 | [代码](https://github.com/CodeCreator/WebOrganizer) |
| 2025 | [**PiKE: Adaptive Data Mixing for Large-Scale Multi-Task Learning Under Low Gradient Conflicts**](https://arxiv.org/abs/2502.06244) | 利用 gradient statistics 在线调整混合配比 | — |
| 2025 | [**Optimizing Pretraining Data Mixtures with LLM-Estimated Utility**](https://arxiv.org/abs/2501.11747) | 利用 LLM 估计的效用设计混合配比 | — |
| 2024 | [**AutoScale: Scale-Aware Data Mixing for Pre-Training LLMs**](https://arxiv.org/abs/2407.20177) | 随训练规模变化的数据混合配比选择 | [代码](https://github.com/feiyang-k/AutoScale) |
| 2024 | [**RegMix: Data Mixture as Regression for Language Model Pre-training**](https://arxiv.org/abs/2407.01492) | 通过 proxy regression 优化混合配比 | [代码](https://github.com/sail-sg/regmix) |
| 2024 | [**BiMix: A Bivariate Data Mixing Law for Language Model Pretraining**](https://arxiv.org/abs/2405.14908) | 混合配比与训练数据量的联合 scaling | — |
| 2024 | [**Data Mixing Laws: Optimizing Data Mixtures by Predicting Language Modeling Performance**](https://arxiv.org/abs/2403.16952) | 预测不同领域混合配比下的模型表现 | [代码](https://github.com/yegcjs/mixinglaws) |
| 2023 | [**Beyond Chinchilla-Optimal: Accounting for Inference in Language Model Scaling Laws**](https://arxiv.org/abs/2401.00448) | 数据预算中的训练与推理成本权衡 | — |
| 2023 | [**DoReMi: Optimizing Data Mixtures Speeds Up Language Model Pretraining**](https://arxiv.org/abs/2305.10429) | 采用 distributionally robust 方法进行 domain reweighting | [代码](https://github.com/sangmichaelxie/doremi) |
| 2022 | [**Training Compute-Optimal Large Language Models**](https://arxiv.org/abs/2203.15556) | Chinchilla · token 与计算预算分配 | — |
| 2020 | [**Scaling Laws for Neural Language Models**](https://arxiv.org/abs/2001.08361) | 模型、数据与计算资源的分配 | — |

[↑ 返回顶部](#top)

<a id="schedules"></a>
### 3.2 Curricula、训练调度与重复使用

决定何时使用数据以及使用多少次：阶段切换、动态混合轨迹、在线逐样本选择、replay 与多轮 epoch。RegMix-D 优化训练轨迹，因此归入本类；RegMix 预测混合配比的质量，因此归入[混合配比](#mixtures)。

**16 篇文献**

| 年份 | 论文 | 数据工程重点 | 代码 / 官方资源 |
|:--:|:--|:--|:--|
| 2026 | [**Specialists Learn Faster: Knowledge Organization in Pre-training Shapes Knowledge Update Efficiency**](https://arxiv.org/abs/2610.13835) | 预训练知识的时间顺序组织与后续知识更新成本 | — |
| 2026 | [**What Is a Repeated Token Worth? The Scaling Geometry of Multi-Epoch Pretraining**](https://arxiv.org/abs/2610.05591) | 利用 multi-epoch scaling laws 区分重复 token 的价值与新增数据的机会成本 | — |
| 2026 | [**Selecting Repetition Counts Across Model Scales in Data-Constrained Pretraining**](https://arxiv.org/abs/2610.05126) | 利用小模型 loss 曲线，为更大规模的数据受限训练筛选候选重复次数 | — |
| 2026 | [**OptiSelect: How does the Optimizer Shape Data Curriculum?**](https://arxiv.org/abs/2610.03432) | 基于 optimizer 相关几何结构的在线样本选择 | — |
| 2026 | [**Replay on Demand: An Emergent Curriculum for Balancing Adaptation and Forgetting in Continued Pretraining**](https://arxiv.org/abs/2609.40089) | 在 continued pretraining 中通过 adaptive replay 平衡学习与遗忘 | [代码](https://github.com/bethgelab/replay-on-demand) |
| 2026 | [**Scaling Domain Data Repetition in LLM Pretraining**](https://arxiv.org/abs/2608.14071) | 重复使用领域数据所产生的 scaling 效应 | — |
| 2026 | [**Internal Data Repetition Destroys Language Models**](https://arxiv.org/abs/2606.24998) | 通过受控重复数据暴露，量化重复结构造成的预训练计算浪费 | — |
| 2026 | [**Holistic Data Scheduler for LLM Pre-training via Multi-Objective Reinforcement Learning**](https://arxiv.org/abs/2606.24133) | 利用 multi-objective reinforcement learning 调度预训练数据 | [代码](https://github.com/DANG-ai/LLM-Training-Holistic-Data-Schedule) |
| 2026 | [**RegMix-D: Dynamic Data Mixing via Proxy Training Trajectories**](https://arxiv.org/abs/2606.18663) | 根据 proxy training trajectories 设计动态混合调度 | — |
| 2026 | [**Demystifying Data Organization for Enhanced LLM Training**](https://arxiv.org/abs/2605.30334) | STR / SAW：基于样本评分的数据排序、循环调度与局部多样性 | [代码](https://github.com/microsoft/data-efficacy) |
| 2026 | [**How Should LLMs Consume High-Quality Data? Optimal Data Scheduling via Quality-Aware Functional Scaling Laws**](https://arxiv.org/abs/2605.25698) | 用于训练数据调度的 quality-aware functional scaling laws | — |
| 2026 | [**Cram Less to Fit More: Training Data Pruning Improves Memorization of Facts**](https://arxiv.org/abs/2604.08519) | 基于 loss 的选择性训练，在事实覆盖、重复使用与模型记忆容量之间取得平衡 | — |
| 2026 | [**DataFlex: A Unified Framework for Data-Centric Dynamic Training of Large Language Models**](https://arxiv.org/abs/2603.26164) | DataFlex：统一支持动态样本选择、领域混合与样本 reweighting 的基础设施 | — |
| 2025 | [**How Learning Rate Decay Wastes Your Best Data in Curriculum-Based LLM Pretraining**](https://arxiv.org/abs/2511.18903) | 数据质量与 learning-rate schedules 的相互作用 | — |
| 2023 | [**Scaling Data-Constrained Language Models**](https://arxiv.org/abs/2305.16264) | 新增数据有限时的多轮 epoch 训练 | [代码](https://github.com/huggingface/datablations) |
| 2022 | [**Scaling Laws and Interpretability of Learning from Repeated Data**](https://arxiv.org/abs/2205.10487) | 重复训练数据的影响 | — |

[↑ 返回顶部](#top)

<a id="training-signals"></a>
### 3.3 上下文构建与训练信号

决定模型同时看到哪些内容，以及哪些 token 提供监督：long-context 依赖选择、document packing、截断、infilling、metadata conditioning、自适应预测目标及样本/token 的 loss 加权。本类合并**综述第 IV-C 和 IV-D 节**，不另设模型结构主题。

**11 篇文献**

| 年份 | 论文 | 数据工程重点 | 代码 / 官方资源 |
|:--:|:--|:--|:--|
| 2026 | [**Rethinking Data Curation in LLM Training: Online Reweighting Offers Better Generalization than Offline Methods**](https://arxiv.org/abs/2605.05227) | ADAPT：不移除样本，在预训练期间自适应调整逐样本 loss 权重 | [代码](https://github.com/Ryan0v0/ADAPT) |
| 2026 | [**KoCo: Conditioning Language Model Pre-training on Knowledge Coordinates**](https://arxiv.org/abs/2604.12397) | 基于 knowledge coordinates 的 metadata conditioning | — |
| 2026 | [**Learning What to Predict: Downstream-Guided Task Design for Continued Pretraining**](https://arxiv.org/abs/2601.22108) | V-pretraining：在 continued pretraining 中利用下游反馈引导自监督预测目标 | — |
| 2025 | [**Beyond URLs: Metadata Diversity and Position for Efficient LLM Pretraining**](https://arxiv.org/abs/2511.21613) | Metadata 的多样性与放置位置 | — |
| 2025 | [**Beyond Length: Quantifying Long-Range Information for Long-Context LLM Pretraining Data**](https://arxiv.org/abs/2510.25804) | LongFilter：比较长短 context 的 information gain，选择具有丰富长程依赖的训练序列 | [代码](https://github.com/HaoranDeng/LongFilter) |
| 2025 | [**LADM: Long-context Training Data Selection with Attention-based Dependency Measurement for LLMs**](https://arxiv.org/abs/2503.02502) | 基于 attention 的依赖度量，用于 long-context 数据选择 | [代码](https://github.com/ZNLP/LADM) |
| 2025 | [**Metadata Conditioning Accelerates Language Model Pre-training**](https://arxiv.org/abs/2501.01956) | 利用 metadata 前缀提高预训练效率 | [代码](https://github.com/princeton-pli/MeCo) |
| 2024 | [**Fewer Truncations Improve Language Modeling**](https://arxiv.org/abs/2404.10830) | Best-fit packing 与截断减少 | — |
| 2024 | [**Rho-1: Not All Tokens Are What You Need**](https://arxiv.org/abs/2404.07965) | 选择性的 token 监督 | [代码](https://github.com/microsoft/rho) |
| 2023 | [**In-context Pretraining: Language Modeling Beyond Document Boundaries**](https://arxiv.org/abs/2310.10638) | 基于检索的 document packing | [代码](https://github.com/swj0419/in-context-pretraining) |
| 2022 | [**Efficient Training of Language Models to Fill in the Middle**](https://arxiv.org/abs/2207.14255) | Fill-in-the-middle 序列变换 | — |

[↑ 返回顶部](#top)

<a id="evaluation"></a>
## 📏 4. 预训练评估

衡量数据决策的效果，并检查这些测量的有效性。本类对应**综述第 V 节**。

<a id="benchmarks"></a>
### 4.1 Benchmark、评估协议与配方评估

下游能力 benchmark、标准化评分协议、受控数据配方比较及 proxy-to-target 迁移研究。DataComp-LM 和 DataDecide 提供了比较数据配方的实验框架。比较不同数据干预时，应匹配预算并固定 prompting 与评分方式。

**18 篇文献**

| 年份 | 论文 | 数据工程重点 | 代码 / 官方资源 |
|:--:|:--|:--|:--|
| 2026 | [**Conditional Transfer from Controlled Pretraining Mixtures to Code**](https://arxiv.org/abs/2610.11548) | 通过受控代码混合实验，区分任务 loss 下降与下游迁移效果 | — |
| 2026 | [**Forecasting Downstream Performance of LLMs With Proxy Metrics**](https://arxiv.org/abs/2605.18607) | 利用 token 分布的 proxy metrics 对候选语料排序，并预测下游表现 | [代码](https://github.com/McGill-NLP/proxy-metrics) |
| 2026 | [**What do Language Models Learn and When? The Implicit Curriculum Hypothesis**](https://arxiv.org/abs/2604.08510) | 通过基础任务，测量不同预训练 checkpoint 与混合配比下的技能习得顺序 | [评估/数据](https://github.com/KaiserWhoLearns/ElementalTask) |
| 2025 | [**Can Small Training Runs Reliably Guide Data Curation? Rethinking Proxy-Model Practice**](https://arxiv.org/abs/2512.24503) | 小规模训练所得数据整理决策的可靠性与迁移边界 | — |
| 2025 | [**DataDecide: How to Predict Best Pretraining Data with Small Experiments**](https://arxiv.org/abs/2504.11393) | 通过小规模实验选择预训练数据 | [代码](https://github.com/allenai/DataDecide) |
| 2024 | [**DataComp-LM: In search of the next generation of training sets for language models**](https://arxiv.org/abs/2406.11794) | 受控的数据选择 benchmark 与开放语料 | [代码](https://github.com/mlfoundations/dclm) |
| 2024 | [**OLMES: A Standard for Language Model Evaluations**](https://arxiv.org/abs/2406.08446) | 标准化的 base-model 评估 | [评估/数据](https://github.com/allenai/olmes) |
| 2024 | [**MMLU-Pro: A More Robust and Challenging Multi-Task Language Understanding Benchmark**](https://arxiv.org/abs/2406.01574) | 更稳健的多任务评估 | [评估/数据](https://github.com/TIGER-AI-Lab/MMLU-Pro) |
| 2024 | [**Language models scale reliably with over-training and on downstream tasks**](https://arxiv.org/abs/2403.08540) | 连续 loss 指标与下游 scaling 预测 | [评估/数据](https://github.com/mlfoundations/scaling) |
| 2023 | [**GPQA: A Graduate-Level Google-Proof Q&A Benchmark**](https://arxiv.org/abs/2311.12022) | 研究生水平的问答评估 | [评估/数据](https://github.com/idavidrein/gpqa) |
| 2023 | [**C-Eval: A Multi-Level Multi-Discipline Chinese Evaluation Suite for Foundation Models**](https://arxiv.org/abs/2305.08322) | 中文多任务评估 | [评估/数据](https://github.com/hkust-nlp/ceval) |
| 2023 | [**Is Your Code Generated by ChatGPT Really Correct? Rigorous Evaluation of Large Language Models for Code Generation**](https://arxiv.org/abs/2305.01210) | EvalPlus · 更严格的代码正确性测试 | [评估/数据](https://github.com/evalplus/evalplus) |
| 2023 | [**Are Emergent Abilities of Large Language Models a Mirage?**](https://arxiv.org/abs/2304.15004) | 指标敏感性与表观能力跃迁 | — |
| 2023 | [**Pythia: A Suite for Analyzing Large Language Models Across Training and Scaling**](https://arxiv.org/abs/2304.01373) | 用于研究训练数据影响的受控 checkpoint | [评估/数据](https://github.com/EleutherAI/pythia) |
| 2021 | [**Training Verifiers to Solve Math Word Problems**](https://arxiv.org/abs/2110.14168) | GSM8K · 数学推理评估 | [评估/数据](https://github.com/openai/grade-school-math) |
| 2021 | [**Evaluating Large Language Models Trained on Code**](https://arxiv.org/abs/2107.03374) | HumanEval · 代码功能正确性 | [评估/数据](https://github.com/openai/human-eval) |
| 2021 | [**Measuring Mathematical Problem Solving With the MATH Dataset**](https://arxiv.org/abs/2103.03874) | MATH · 数学问题求解 | [评估/数据](https://github.com/hendrycks/math) |
| 2020 | [**Measuring Massive Multitask Language Understanding**](https://arxiv.org/abs/2009.03300) | MMLU · 知识与推理评估 | [评估/数据](https://github.com/hendrycks/test) |

[↑ 返回顶部](#top)

<a id="auditing"></a>
### 4.2 Data attribution、数据审计与污染检测

衡量哪些数据影响模型，检查来源覆盖与访问条件，并检测 memorization、benchmark contamination 或合成数据污染。这些研究用于评估数据与评估结果的有效性；构建更干净语料的方法见[过滤与选择](#quality)。

**14 篇文献**

| 年份 | 论文 | 数据工程重点 | 代码 / 官方资源 |
|:--:|:--|:--|:--|
| 2026 | [**OLMo-Detect: A Multi-Stage, Confounder-Controlled Benchmark for Membership Inference on Large Language Models**](https://arxiv.org/abs/2610.02986) | OLMo-Detect：控制 distribution shift 与训练阶段的 membership inference | [评估/数据](https://github.com/LuckyDaydreamer/OLMo-Detect) |
| 2026 | [**FineWeb-CLaR: Culture, Language, and Region Annotations for Benchmark-Aligned Corpus Auditing**](https://arxiv.org/abs/2609.25298) | 用于语料审计的文化、语言与地区标注 | [评估/数据](https://github.com/Yusser96/FineWeb-CLaR) |
| 2026 | [**SynthSentry: Detecting Synthetic Data Contamination in Language Model Training Data**](https://arxiv.org/abs/2609.12353) | 在语料层面筛查合成来源信号 | — |
| 2026 | [**Measuring Task-Agnostic Training Data Influence Across Language Model Pretraining**](https://arxiv.org/abs/2608.13515) | 沿训练轨迹测量 task-agnostic 数据影响 | — |
| 2026 | [**GRASP: Geometry-aware Residual Alignment for Scalable Pretraining Data Attribution**](https://arxiv.org/abs/2606.06892) | 利用 residual alignment 与几何信息进行预训练 data attribution | [代码](https://github.com/MoonLight-Sherry/GRASP) |
| 2026 | [**LLMSurgeon: Diagnosing Data Mixture of Large Language Models**](https://arxiv.org/abs/2605.30348) | LLMSurgeon / LLMScan：从生成文本推断领域层面的预训练数据配比 | [代码](https://github.com/Yaxin9Luo/LLMSurgeon) |
| 2026 | [**Sketching the Readout of Large Language Models for Scalable Data Attribution and Valuation**](https://arxiv.org/abs/2604.16197) | RISE：压缩输出层 influence 表征，用于 attribution 与候选数据估值 | — |
| 2025 | [**DyePack: Provably Flagging Test Set Contamination in LLMs Using Backdoors**](https://arxiv.org/abs/2505.23001) | 利用 backdoor 测试检测 benchmark contamination | [代码](https://github.com/chengez/DyePack) |
| 2025 | [**The Emperor's New Clothes in Benchmarking? A Rigorous Examination of Mitigation Strategies for LLM Benchmark Data Contamination**](https://arxiv.org/abs/2503.16402) | 对 benchmark contamination 缓解策略进行受控评估 | [代码](https://github.com/ASTRAL-Group/BDC-mitigation-assessment) |
| 2024 | [**Consent in Crisis: The Rapid Decline of the AI Data Commons**](https://arxiv.org/abs/2407.14933) | 网页数据的访问条件与 provenance | — |
| 2023 | [**Rethinking Benchmark and Contamination for Language Models with Rephrased Samples**](https://arxiv.org/abs/2311.04850) | 超越 exact matching 的 benchmark decontamination | [评估/数据](https://github.com/lm-sys/llm-decontaminator) |
| 2023 | [**Detecting Pretraining Data from Large Language Models**](https://arxiv.org/abs/2310.16789) | 利用 membership inference 检测预训练数据 | [评估/数据](https://github.com/swj0419/detect-pretrain-code) |
| 2022 | [**Quantifying Memorization Across Neural Language Models**](https://arxiv.org/abs/2202.07646) | Memorization 与重复序列 | — |
| 2021 | [**Documenting Large Webtext Corpora: A Case Study on the Colossal Clean Crawled Corpus**](https://arxiv.org/abs/2104.08758) | C4 审计：内容、偏差与过滤效果 | — |

[↑ 返回顶部](#top)

<a id="acknowledgments"></a>
## 致谢

本目录依据 Yudong Li、Ruiqi Zhou、Peiru Yang、Shiyi Zheng、Linlin Shen、Xintian Li 和 Yongfeng Huang 撰写的 [*Data Engineering in Large Language Model Pretraining: A Survey*](asset/LLM_Data_Survey_Revised.pdf) 组织。

展示形式参考了 [Awesome Multimodal Large Language Models](https://github.com/BradyFU/Awesome-Multimodal-Large-Language-Models)。欢迎通过 issue 或 pull request 推荐相关论文、提出修正。

[↑ 返回顶部](#top)

---
title: MIXTURE-OF-EXPERTS CAN SURPASS DENSE LLMS UNDER STRICTLY EQUAL RESOURCE
type: paper
paper_level: A
venue: ICLR
year: 2026
pdf_ref: paperPDFs/ICLR_2026/MIXTURE-OF-EXPERTS_CAN_SURPASS_DENSE_LLMS_UNDER_STRICTLY_EQUAL_RESOURCE.pdf
aliases:
- SERMCFOAMDR
- MECSDLUSER
acceptance: Oral
tags:
- topic/iclr_2026
openreview_forum_id: oIdzliJAeA
core_operator: 激活率 (activation rate, r_a)，即激活参数占非词汇表参数的比例。
primary_logic: 通过优化的架构设计（1dense+SE 层布局、形状比率 ζ≈88、μ≈22），MoE 模型在其激活率处于特定最优区间（约 15%–48%，最优值约 20%）内时，能够在严格相等的总参数量 N、训练计算量 C 和数据量 D 下超越稠密模型，且该最优激活率在不同模型规模（2B、3B、7B）上保持一致。额外的数据需求可通过数据复用策略有效缓解。
claims:
- 在固定 N 和 C 下，2B 最优 MoE (r_a=20%) 的 BPC 比同等计算的稠密基线低 0.0064，且接近两倍计算的稠密模型性能。
- 最优激活率 r_a** = 20% 在 2B、3B 和 7B 模型上均保持一致（如 7B MoE 在固定 C 下于 r_a=20.07% 获得最低 BPC=0.4543，显著优于稠密基线 0.4736）。
- 在严格数据复用方案下，MoE 模型仍能维持对稠密基线的优势，且最优激活率不变（如 7B strict reuse 在多数基准上表现与独特数据训练相仿）。
- 在 7B 规模上，SFT 后的 MoE (r_a=20.07%) 在多个下游综合基准（如 MUSR 48.94 vs 35.98, CMMLU 32.11 vs 31.23）和数学/代码任务上优于稠密基线。
paradigm: 通过优化的架构设计（1dense+SE 层布局、形状比率 ζ≈88、μ≈22），MoE 模型在其激活率处于特定最优区间（约 15%–48%，最优值约 20%）内时，能够在严格相等的总参数量 N、训练计算量 C 和数据量 D 下超越稠密模型，且该最优激活率在不同模型规模（2B、3B、7B）上保持一致。额外的数据需求可通过数据复用策略有效缓解。
---

# MIXTURE-OF-EXPERTS CAN SURPASS DENSE LLMS UNDER STRICTLY EQUAL RESOURCE

> [!tip] 核心洞察
> 通过优化的架构设计（1dense+SE 层布局、形状比率 ζ≈88、μ≈22），MoE 模型在其激活率处于特定最优区间（约 15%–48%，最优值约 20%）内时，能够在严格相等的总参数量 N、训练计算量 C 和数据量 D 下超越稠密模型，且该最优激活率在不同模型规模（2B、3B、7B）上保持一致。额外的数据需求可通过数据复用策略有效缓解。

| 字段 | 内容 |
|------|------|
| 中文题名 | 在严格相等资源下混合专家模型可超越稠密大型语言模型 |
| 英文题名 | MIXTURE-OF-EXPERTS CAN SURPASS DENSE LLMS UNDER STRICTLY EQUAL RESOURCE |
| 会议/期刊 | ICLR 2026 (Oral) |
| Links | [paper](https://openreview.net/forum?id=oIdzliJAeA) |
| Topic | #topic/iclr_2026 |
| Method | Strictly Equal-Resource MoE Comparison Framework (Optimal AR MoE with Data Reuse) |
| Dataset | In-house validation set (7B upstream), MUSR (Reasoning), CMMLU (Knowledge) |

> [!tip] 效果简介
> - In-house validation set (7B upstream) 上，BPC 为 0.4543 (MoE r_a=20.07%, C=2.86e21, D=316B)，对比 0.4736 (7B Dense, C=2.86e21, D=68B)，变化 -0.0193。
> - MUSR (Reasoning) 上，Accuracy 为 48.94 (SFT MoE r_a=20.07%, strict data reuse)，对比 35.98 (SFT Dense baseline)，变化 +12.96。
> - CMMLU (Knowledge) 上，Accuracy 为 32.11 (SFT MoE strict reuse)，对比 31.23 (Dense baseline)。

## 概述

当前关于混合专家（Mixture-of-Experts, MoE）与稠密大语言模型（Dense LLM）优劣的争论，存在一个关键瓶颈：现有对比往往仅关注数据量或计算量的单一维度，而忽视了在**总参数量 N、训练计算量 C 和数据量 D 三者严格相等**的约束下进行公平评估。这导致一个核心问题悬而未决——在真实资源约束下，MoE 架构是否真的能超越稠密架构？

本文的核心洞见是：通过优化的架构设计，MoE 模型在其**激活率**（activation rate, $r_{\mathrm{a}}$，即激活参数占非词汇表参数的比例）处于特定最优区间时，能够在严格相等的 N、C、D 约束下稳定超越稠密模型。决定性证据表明，该最优激活率 $r_{\mathrm{a}}^{**} \approx 20\%$ 在 2B、3B 和 7B 三种模型规模上保持高度一致，且 MoE 模型在固定 N 和 C 下的性能不仅显著优于同等计算的稠密基线，甚至接近两倍计算量稠密模型的水平。对于 MoE 因稀疏激活而带来的额外数据需求，研究进一步验证了数据复用策略可有效缓解该问题，且不改变最优激活率的取值。

在方法定位上，本研究构建了一个严格相等资源的 MoE 对比框架，核心调控变量即为激活率 $r_{\mathrm{a}}$。方法谱系上，该工作区别于仅关注模型容量或训练效率的既有 MoE 研究，通过统一参数化框架将稠密与 MoE 架构纳入同一分析体系，并采用“架构贪婪搜索—激活率分析—数据复用验证”的三步实验方法论，确保对比的公平性与结论的可靠性。

## 背景与动机

### 混合专家模型与稠密模型的效率之争

随着大型语言模型（LLM）的规模持续增长，训练和部署成本已成为制约其发展的核心瓶颈。混合专家模型（Mixture-of-Experts, MoE）通过稀疏激活机制——每个 token 仅激活部分参数——在降低计算开销的同时保持较大的总参数量，被视为突破稠密模型效率极限的关键路径。然而，一个根本性问题始终悬而未决：**在严格相等的资源约束下，MoE 是否真的能超越稠密架构？**

### 现有研究的单一维度局限

当前 MoE 与稠密模型的比较研究存在一个共同的方法论缺陷：它们往往只关注数据量或计算量等单一维度，而忽略了**总参数量 N、训练计算量 C 和数据量 D 三者之间的严格相等约束**。这种不完整的控制条件使得现有结论难以支撑真实的资源分配决策——在实际部署中，内存约束（固定 N）、训练预算（固定 C）和可用数据（固定 D）往往同时起作用，任何维度的放松都可能导致对 MoE 优势的误判。

### 本文的核心问题与动机

本文从一个更具说服力的视角重新审视这一争论，提出以下核心问题：

> **在总参数量、训练计算量和数据量三者严格相等的约束下，混合专家模型能否超越稠密大型语言模型？**

这一问题的实际价值在于：如果 MoE 在严格等资源条件下确实优于稠密模型，那么它将为资源受限场景下的架构选择提供明确指导；反之，如果 MoE 的优势依赖于某一维度的资源放松，那么其实际部署价值就需要重新评估。为系统回答这一问题，本文引入统一的参数化框架，并通过三阶段实验方法论——架构优化、最优激活率搜索、数据复用策略验证——展开大规模实证研究，累计训练近 200 个 2B 级模型和超过 50 个 7B 级模型，处理 50 万亿 tokens。

## 核心创新

本研究的核心创新在于构建了一套**严格资源相等下的 MoE 与稠密模型公平对比框架**，并通过系统性的架构优化，发现了一个**跨模型规模稳定的最优激活率区间**，使得 MoE 模型能够在总参数量 N、训练计算量 C 和数据量 D 三者完全相等的约束下超越稠密基线。

### 关键机制：激活率作为因果调节变量

研究将**激活率** $r_{\mathrm{a}}$（激活参数占非词汇表参数的比例）确立为决定 MoE 性能的核心调节变量。稠密模型可视为 $r_{\mathrm{a}}=100\%$ 的特例，而 MoE 模型通过稀疏激活降低了每 token 的计算开销。关键发现在于：$r_{\mathrm{a}}$ 并非越低越好，而是存在一个明确的最优区间 **15%–48%**，最优值约为 **20%**。这一最优值在 2B、3B 和 7B 三个模型规模上保持高度一致（Figure 1, Figure 2, Table 12），表明它是一个架构层面的稳定特性，而非特定规模的偶然结果。

### 架构设计层面的 changed slots

为实现上述性能，研究对 MoE 架构进行了多项关键优化，构成了相对于稠密基线的核心设计变更：

| 设计维度 | 稠密基线 | 本文 MoE 方案 | 证据锚点 |
|---------|---------|-------------|---------|
| 层布局 | 纯稠密层 | **1dense+SE**（首层稠密前置，其余为带共享专家的 MoE 层） | Table 5 |
| 形状比率 ζ | 约 70（LLaMA 风格） | **≈88** | §4, Figure 4 |
| 形状比率 μ | 不适用 | **≈22** | §4, Figure 4 |
| 激活率 $r_{\mathrm{a}}$ | 100% | **20%**（最优区间 15%–48%） | Figure 1b, Table 10, Table 12 |
| Top-K 设置 | 不适用 | **K > 1 且避免过大**，不使用 K=1 | Table 7 |
| 数据使用 | 单轮训练 | **多 epoch 数据复用**（严格或宽松方案） | §6, Figure 2b |

其中，**1dense+SE 层布局**被证明是最优的，可能因为前置稠密层有助于训练稳定性（Table 5）。**形状比率 ζ≈88 和 μ≈22** 是基于系统性超参数搜索得到的经验最优值，反映了 MoE 架构在参数分配上需要不同于稠密模型的宽高比（Figure 4）。**Top-K 设置**需避免 K=1（专家利用不均）和 K 过大（路由开销增加），Table 7 显示适中的 K 值效果最佳。

### 数据复用策略：解决数据需求瓶颈

MoE 模型参数总量更大，在严格相等的总数据量 D 约束下，每个 token 被“看到”的次数更少。研究提出两种数据复用方案来弥合这一差距：
- **严格方案**：保持 N、D、C 三者完全相等，通过多 epoch 训练复用数据
- **宽松方案**：固定训练 epoch 数，允许 D 随 $r_{\mathrm{a}}$ 变化

关键结论是：在严格数据复用下，MoE 模型仍能维持对稠密基线的优势，且最优激活率不变（Figure 2b, Table 2）。例如，7B SFT MoE（$r_{\mathrm{a}}=20.07\%$，严格复用）在 MUSR 推理基准上达到 48.94，显著优于稠密基线的 35.98（Table 2）。但需注意，多 epoch 训练超过两次时会持续退化（Figure 5），数据复用对知识类基准的影响大于推理类基准。

### 核心洞察的可迁移性

最优激活率约 20% 这一发现的关键价值在于其**跨规模稳定性**——从 2B 到 7B 模型，最优 $r_{\mathrm{a}}$ 几乎不变。这意味着小规模实验的架构搜索结论可以直接指导更大规模模型的 MoE 设计，大幅降低了实际部署中的调参成本。此外，在固定 N 和 C 的条件下，2B 最优 MoE（$r_{\mathrm{a}}=20\%$）的 BPC 比同等计算稠密基线低 0.0064，且性能接近两倍计算量的稠密模型（Figure 1b），说明 MoE 在资源约束下具有明确的效率优势。

## 整体框架

本研究提出了一套在严格相等资源约束下公平比较混合专家（MoE）与稠密大型语言模型（LLM）的完整实验框架。该框架的核心目标在于回答一个被现有文献忽视的关键问题：当总参数量 N、训练计算量 C 和数据量 D 三者完全相同时，MoE 架构能否在性能上超越稠密架构。

### 框架的因果逻辑

现有研究在比较两类架构时，通常只固定 N 或 C 中的某一个维度，忽略了多维度资源约束的联合效应，导致无法在真实部署场景下得出可靠结论。本工作的核心因果杠杆是**激活率**（activation rate, $r_{\mathrm{a}} = N_{\mathrm{a}} / N$），即激活的非词汇表参数占总非词汇表参数的比例。通过精确调控 $r_{\mathrm{a}}$，可以在固定 N 和 C 的前提下，系统性地探索 MoE 相对于稠密基线的性能增益空间。

### 三阶段实验方法论

整个实验流程由三个递进的阶段构成：

1.  **架构贪婪搜索**：在统一参数化框架下（§3），首先对 MoE 架构的微观设计进行优化，包括层排列方式、形状比率、Top-K 设置等，以确保每个候选模型在给定 N 下达到近似最优性能。这一阶段隔离了架构选择对后续分析的干扰。
2.  **激活率分析**：在优化后的架构骨架上，固定 N 和 C，系统性地改变激活率 $r_{\mathrm{a}}$，观察上游验证损失（BPC）的变化，从而定位最优激活率区间 $r_{\mathrm{a}}^{**}$（§5）。
3.  **数据复用策略**：由于最优 MoE 模型在固定 C 下可能需要更多数据才能充分收敛，框架引入了严格数据复用方案，使 MoE 模型在 D 与稠密基线完全相等的条件下完成训练，以验证性能优势的稳健性（§6）。

### 统一参数化与模块构成

为在 N 和 C 两个维度上实现精确的等资源对比，框架建立了一个统一参数化体系，将稠密模型和 MoE 模型的非词汇表参数量与每 token 前向计算量表达为模型宽度 $D_{\mathrm{m}}$、层数 L、FFN 比率等变量的函数。在此体系下，MoE 模型由以下核心模块构成：

-   **Router / Gating Network**：由线性层 $W_g$、Softmax 和 Top-K 操作组成，负责为每个 token 计算专家分数并选择激活专家。
-   **Expert FFNs**：每个专家为标准前馈网络，处理分配给它的 token。
-   **Shared Expert**：一个始终激活的共享 FFN，为所有 token 贡献统一表示，其大小满足 $D_{\mathrm{se}} = K D_{\mathrm{e}}$。
-   **Multi-Head Attention with ALiBi**：注意力层，使用 ALiBi 位置编码处理长序列。
-   **RMSNorm**：预归一化层。
-   **Load Balancing Loss**：标准辅助损失，用于鼓励所有专家被均匀利用。

### 关键架构决策

在贪婪搜索阶段，框架确定了若干对最终性能有决定性影响的架构选择：

| 设计维度 | 基线值 | 最优选择 | 证据锚点 |
| :--- | :--- | :--- | :--- |
| 层排列 | 纯稠密或全 MoE 层 | **1dense+SE**（一层前置稠密层，其余为带共享专家的 MoE 层） | Table 5 |
| 形状比率 $\zeta$ | 由标准稠密设计决定 | **≈88** | §4, Figure 4 |
| 形状比率 $\mu$ | 不适用 | **≈22** | §4, Figure 4 |
| 激活率 $r_{\mathrm{a}}$ | 100%（稠密） | **约 20%**（最优区间 15%–48%） | Figure 1b, Figure 2b |
| Top-K 设置 | 不适用 | **K > 1 且避免过大**（如 6），不使用 K=1 | Table 7 |
| 数据使用 | 单轮唯一数据 | **多 epoch 数据复用**（严格或宽松方案） | §6, Figure 2b |

上述架构决策共同构成了后续激活率分析与数据复用实验的模型基础。整个框架的输入为统一的预训练语料与资源约束（N, C, D），输出为在严格相等条件下 MoE 与稠密模型的性能对比结论。

## 核心模块与公式推导

### 统一参数化框架

为在严格相等资源约束下公平比较稠密与 MoE 架构，论文首先引入一套统一参数化框架，将模型总参数量 $N$、每 token 前向计算量 $M$ 以及激活率 $r_a$ 纳入同一表述体系。

对于稠密模型，非词表参数量 $N$ 近似为：

$$N \approx (4 + 3\alpha) D_m^2 L = (4 + 3\alpha) \zeta^2 L^3$$

其中 $D_m$ 为模型宽度，$L$ 为层数，$\alpha$ 为 FFN 中间层宽度与 $D_m$ 的比值，形状比率 $\zeta = D_m / L$ 表征模型宽深比。每 token 前向计算量 $M$ 近似为：

$$M \approx 2N + 4 D_m S L = 2N + 4 \zeta^2 \gamma L^3$$

其中 $S$ 为序列长度，$\gamma = S / L$。

对于 MoE 模型，总非词表参数量 $N$ 近似为：

$$N \approx (4 + 3\mu) D_m^2 L_e + (4 + 3\alpha) D_m^2 L_d$$

其中 $L_e$ 为 MoE 层数，$L_d$ 为稠密层数。$\mu$ 为 MoE 层中总 FFN 参数量与模型宽度的比率，定义为：

$$\mu = (D_{se} + E D_e) / D_m$$

此处 $D_{se}$ 为共享专家宽度，$E$ 为专家总数，$D_e$ 为单个专家宽度。

激活率 $r_a$ 是控制 MoE 实际计算开销的核心旋钮，定义为激活的非词表参数占总非词表参数的比例：

$$r_a = N_a / N$$

MoE 层中激活 FFN 与模型宽度的比率 $\beta$ 为：

$$\beta = (D_{se} + K D_e) / D_m$$

其中 $K$ 为 Top-K 路由选择的专家数量。论文指出，在固定 $N$ 的前提下，$r_a$ 是决定每 token FLOPs 比率的主要因素，因此成为后续实验的核心控制变量。

### 关键架构模块

基于统一框架，论文确定了 MoE 模型的关键架构模块：

- **Router / Gating Network**：由线性层 $W_g$、Softmax 和 Top-K 操作组成，负责为每个 token 计算专家分数并选择激活专家。
- **Expert FFNs**：每个专家实现为标准前馈网络，仅处理分配给它的 token。
- **Shared Expert**：一个始终激活的共享 FFN，为所有 token 贡献统一表示，其大小满足 $D_{se} = K D_e$。
- **Multi-Head Attention with ALiBi**：注意力层，使用 ALiBi 位置编码处理长序列。
- **RMSNorm**：预归一化层。
- **Load Balancing Loss**：标准辅助负载均衡损失，鼓励均匀利用所有专家。

### 架构搜索结论

通过贪婪搜索，论文确定了最优架构配置：层布局采用 **1dense+SE**（一层前置稠密层，其余为带共享专家的 MoE 层），共享专家大小比率对性能影响甚微，门控分数归一化虽可降低均衡损失但因需 $K>1$ 而未采用。形状比率方面，$\zeta \approx 88$、$\mu \approx 22$ 随模型宽度增大分别呈上升和下降趋势。Top-K 设置中，$K=1$ 和过大的 $K$（如 6）均为次优选择。

## 实验与分析

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/001_Table_1.jpg]]
*Table 1: Notation*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/007_Table_2.jpg]]
*Table 2: Accuracy of 7B SFT-ed models across different benchmarks*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/008_Table_3.jpg]]
*Table 3: Pretraining data recipe compared with the LLaMA-1 recipe*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/010_Table_4.jpg]]
*Table 4: Common training recipe*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/011_Table_5.jpg]]
*Table 5: Experimental settings and results of MoE layer arrangement and shared expert. Hyperparameters shared by all experiments: D _ { \mathrm { m } } = 1 4 0 8 ， D _ { \mathrm { f f n } } = 3 9 0 4 ， \mathbf { \bar { N o r m } } = \mathbf { T r u e }*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/012_Table_6.jpg]]
*Table 6: Experimental settings and results of gate score normalization. Hyperparameters shared by all experiments: Scheme = 1dense, L = 1 7 D _ { \mathrm { m } } = 1 4 0 8 D _ { \mathrm { f f n } } = 3 9 0 4 , H = 22, D _ { \mathrm { h } } = 6 4*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/013_Table_7.jpg]]
*Table 7: Experimental settings and results of top-K setting. Hyperparameters shared by all experiments: Scheme = { \mathrm { 1 d e n s e } } L = 1 6 D _ { \mathrm { m } } = 1 4 0 8 ， D _ { \mathrm { f f n } } = 3 9 0 4 , H = 11, D _ { \mathrm { h } } = 1 2 8 , Norm = False*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/014_Table_8.jpg]]
*Table 8: Experimental settings and results of model shape ratios. Hyperparameters shared by all experiments: Scheme = 1dense, S = 16384, D _ { \mathrm { h } } = 1 2 8*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/015_Table_9.jpg]]
*Table 9: Experimental settings and results of optimal ARs for MoE models with N = 2.15B and fixed ra. Hyperparameters shared by all experiments: L = 16, S = 2048, Dm = 1408, D _ { \mathrm { f f n } } = 3904, H = 11, Dh = 128, ζ = 88*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/016_Table_10.jpg]]
*Table 10: Experimental settings and results of optimal ARs for MoE models N = 2.15B with fixed C. Hyperparameters shared by all experiments: L = 1 6 , S = 2 0 4 8 , D _ { \mathrm { m } } = 1 4 0 8 , D _ { \mathrm { f f n } } = 3 9 0 4 , H = 11, D _ { \mathrm { h } } = 1 2 8 , \zeta = 8 8 . The green row corresponds to the MoE model with the lowest BPC on the validation set*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/017_Table_11.jpg]]
*Table 11: Experimental settings and results of optimal ARs for MoE models with N = 6 . 5 2 \mathrm { B } with fixed D. Hyperparameters shared by all experiments: L = 2 4 , S = 2 0 4 8 , D _ { \mathrm { m } } = 2 0 4 8 , D _ { \mathrm { f f n } } = 5 4 6 4 , H = \bar { 1 6 } , \bar { D } _ { \mathrm { h } } = 1 2 8 , \zeta = 8 5 . 3*

![[assets/figures/papers/paper_list_l4_https_openreview_net_forum_id_oIdzliJAeA/figures/018_Table_12.jpg]]
*Table 12: Experimental settings and results of optimal ARs for MoE models with N = 6 . 5 2 \mathrm { B } with fixed C. Hyperparameters shared by all experiments: L = 2 4 , S = 2 0 4 8 , D _ { \mathrm { m } } = 2 0 4 8 , D _ { \mathrm { f f n } } = 5464, H = \bar { 1 6 } , \bar { D } _ { \mathrm { h } } = 1 2 8 , \zeta = 8 5 . 3 . The green row corresponds to the MoE model with the lowest BPC on the validation set*

### 核心发现：严格资源约束下的最优激活率

本文的核心实验围绕一个中心问题展开：**在严格相等的总参数量 N、训练计算量 C 和数据量 D 约束下，MoE 能否超越稠密 LLM？** 为回答这一问题，研究者首先通过架构搜索确定了 MoE 的最优设计（1dense+SE 层布局、形状比率 ζ≈88、μ≈22），随后系统性地探索了激活率 $r_a$ 对性能的影响。

**最优激活率 $r_a^{**}$ 的发现**是本文最关键的实验结论。在 2B 参数规模下（Figure 1），当固定总参数量 N 和训练计算量 C 时，MoE 模型的性能随激活率 $r_a$ 呈现非单调变化：激活率过低会导致每个专家训练不充分，过高则稀释了 MoE 的稀疏计算优势。最优激活率出现在 **$r_a^{**} \approx 20\%$**，此时 MoE 模型的 BPC 比同等计算预算的稠密基线低 0.0064，且接近两倍计算量稠密模型的性能（Figure 1b）。

这一最优激活率在更大规模上得到了验证。**在 7B 参数规模下，$r_a^{**} = 20\%$ 仍然成立**（Figure 2）。具体而言，在固定 C=2.86e21 的条件下，$r_a=20.07\%$ 的 MoE 模型在内部验证集上取得 BPC=0.4543，显著优于同等计算的稠密基线 BPC=0.4736（Table 12 vs Table 13），差距达 -0.0193。这表明 MoE 的稀疏激活机制在严格计算约束下确实提供了实质性的性能增益。

### 数据复用策略的影响

MoE 模型由于激活参数较少，在相同计算预算下需要处理更多 token，这意味着其对数据量的需求更高。为在严格数据约束下进行公平比较，本文探索了两种数据复用方案：

- **严格复用方案**：MoE 和稠密模型使用完全相同的数据集 D，MoE 模型通过多 epoch 训练来消耗其额外的计算预算。
- **宽松复用方案**：固定训练 epoch 数为 2，允许不同 $r_a$ 的模型使用不同大小的数据集。

实验结果表明，**在严格数据复用方案下，MoE 模型仍能维持对稠密基线的优势，且最优激活率 $r_a^{**} = 20\%$ 保持不变**（Figure 2b）。7B 规模的 SFT 模型在严格复用条件下，MoE（$r_a=20.07\%$）在多个下游基准上优于稠密基线：MUSR 推理基准 48.94 vs 35.98（+12.96），CMMLU 知识基准 32.11 vs 31.23（Table 2）。值得注意的是，数据复用对推理类任务影响较小，但对知识密集型任务造成较明显的性能退化（Table 2），这提示在实际部署中需根据任务类型权衡数据复用策略。

此外，**多 epoch 训练（超过两个 epoch）在 7B 规模上持续导致性能下降**（Figure 5），这表明简单的数据重复并非无代价，过度的数据复用可能引入过拟合风险。

### 架构消融实验

在确定最优激活率之前，本文通过一系列消融实验确定了 MoE 的优化架构设计：

- **层布局**：1dense+SE（一层前置稠密层，其余为带共享专家的 MoE 层）在所有候选布局中表现最优（Table 5），可能因为前置稠密层有助于训练稳定性。
- **Top-K 设置**：K 值过小（如 K=1）或过大（如 K=6）均导致次优性能（Table 7），适当的多专家激活是必要的。
- **门控分数归一化**：虽然能降低负载均衡损失，但在 K=1 时会导致零梯度问题，因此在部分实验中未采用（Table 6）。
- **共享专家大小比率**：共享专家占总专家大小的比例对性能影响极小（§4），表明该超参数具有较强的鲁棒性。

### 下游任务泛化验证

为验证上游预训练发现的泛化性，本文在 7B 规模上对预训练和 SFT 后的模型进行了全面的下游基准评估（Figure 3, Table 2）。**在预训练阶段，更稠密的 MoE（$r_a > r_a^{**}$）在所有领域上均优于或匹配更稀疏的 MoE（$r_a < r_a^{**}$）**（Figure 2b）。经过 SFT 后，$r_a=20\%$ 的 MoE 模型在推理、知识和综合基准上全面超越稠密基线，且其性能甚至优于使用两倍计算量训练的稠密模型（Figure 3），这进一步证实了 MoE 在严格资源约束下的架构优势。

## 方法谱系与知识库定位

### 1. 核心问题与定位

本研究针对一个长期悬而未决的争论：**在严格相等的资源约束下，混合专家（MoE）架构能否真正超越稠密大型语言模型？** 现有比较研究存在系统性缺陷——它们通常只在数据量或计算量某一单一维度上对齐，而忽略了总参数量 $N$、训练计算量 $C$ 和数据量 $D$ 三者的同时相等约束。这使得“MoE 是否优于稠密架构”的结论缺乏可信的实证基础。

本工作的核心贡献在于提出了一个**严格相等资源的 MoE 比较框架**，其核心因果调节变量是**激活率（activation rate, $r_a$）**，即激活参数占非词汇表参数的比例。通过系统性地控制 $r_a$ 并优化架构设计，研究发现 MoE 在其激活率处于特定最优区间（约 15%–48%，最优值约 20%）时，能够在 $N$、$C$、$D$ 三者严格相等的前提下超越稠密基线。该最优激活率在 2B、3B 和 7B 模型规模上表现出令人惊讶的一致性，表明这一规律具有跨规模的泛化能力。

### 2. 方法谱系中的位置

#### 2.1 与现有 MoE 研究的区别

现有 MoE 研究可大致分为两类：一类关注训练效率（如通过专家并行降低通信开销），另一类关注模型容量扩展（如在固定计算预算下增加总参数量）。这两类工作均未在 $N$、$C$、$D$ 三者同时相等的约束下进行公平比较。本工作首次将比较条件收紧至三者严格相等，从而排除了“MoE 的优势仅来自更多参数或更多计算”的替代解释。

在具体技术选择上，本工作与以下基线方法形成对比：

- **稠密 Transformer（等 $N$ 优化版）**：作为唯一对比基准，该基线在相同 $N$/$C$/$D$ 约束下经过形状超参数调优。本工作未引入其他 MoE 变体作为对比基线，而是聚焦于 MoE 与稠密架构的根本性差异。
- **全 MoE 层架构**：实验表明，纯 MoE 层设计（无前置稠密层）性能劣于 **1dense+SE** 布局（Table 5），后者在首层使用稠密 FFN、其余层使用带共享专家的 MoE 层。这一发现与近期关于“早期层需要更稳定的表示学习”的直觉一致，但本工作未将其与特定引用工作直接关联。
- **K=1 路由**：实验明确否定了 K=1 的设置（Table 7），因其性能显著劣于 K>1 方案。这与部分早期 MoE 工作中使用 top-1 路由的做法形成对比，但文中未点名具体工作。

#### 2.2 架构设计空间的系统探索

本工作在 MoE 架构设计上的贡献并非提出全新的组件，而是通过系统消融实验确定了在严格资源约束下最优的架构配置：

| 设计槽位 | 基线/常见做法 | 本工作最优选择 | 证据锚点 |
|---------|-------------|--------------|---------|
| 层布局 | 纯稠密或全 MoE | 1dense+SE（首层稠密，其余为带共享专家的 MoE） | Table 5 |
| 形状比率 $\zeta$ | 约 70（如 LLaMA 系列） | ≈88 | §4, Figure 4 |
| 形状比率 $\mu$ | 不适用（稠密无专家） | ≈22 | §4, Figure 4 |
| 激活率 $r_a$ | 100%（稠密全激活） | 20%（最优区间 15%–48%） | Figure 1b, Figure 2b, Table 10, Table 12 |
| Top-K | 不适用 | K>1 且避免过大（如 6） | Table 7 |
| 数据使用 | 单轮唯一数据 | 多 epoch 数据复用（严格或宽松方案） | §6, Figure 2b, Table 14–17 |

其中，形状比率 $\zeta = D_m / L$（模型宽度与深度之比）和 $\mu = (D_{se} + E D_e) / D_m$（MoE 层中总 FFN 参数量与模型宽度之比）的选择并非通过穷举搜索获得，而是基于对性能趋势的观测。Figure 4 显示 $\zeta$ 随 $D_m$ 增大呈上升趋势，$\mu$ 则呈下降趋势，本工作据此设定了 ≈88 和 ≈22 的经验值。这些值的普适性需要在更大规模模型上进一步验证。

#### 2.3 与 Scaling Law 研究的关系

本工作与 Scaling Law 文献（如 Hoffmann et al., 2022）存在方法论上的继承与扩展。Hoffmann 等人建立了稠密模型的 $N$–$D$–$C$ 关系，但未涉及 MoE 架构。本工作在以下方面进行了扩展：

- **激活率作为新维度**：将 $r_a$ 引入为 MoE 模型的关键调节变量，揭示了在固定 $N$ 和 $C$ 下，$r_a$ 对性能的非线性影响（Figure 1b）。
- **充分训练约束**：遵循 Hoffmann 等人的 $D/N \geq 20$ 准则，确保所有关键模型均在充分训练状态下进行比较（§2.2, §5）。
- **数据复用策略**：当 MoE 需要更多数据以达到最优性能时，本工作提出了严格和宽松两种数据复用方案，而非简单地增加唯一数据量。这一策略在 Scaling Law 框架中尚无先例。

### 3. 适用边界与约束条件

#### 3.1 已验证的适用范围

本工作的核心结论（最优 $r_a \approx 20\%$）在以下条件下得到验证：

- **模型规模**：2B、3B 和 7B 非词汇表参数量（Figure 1, Figure 2, Figure 5, Table 12, Table 13）
- **训练数据量**：$D/N \geq 20$，确保充分训练
- **架构配置**：1dense+SE 布局，$\zeta \approx 88$，$\mu \approx 22$，RMSNorm 预归一化，ALiBi 位置编码
- **训练配方**：基于 LLaMA-1 配方的改进数据混合（Table 3），通用训练超参数（Table 4）
- **评估维度**：上游验证 BPC，下游综合基准（MUSR、CMMLU、MMLU、DROP、BBH 等 29 个基准）

#### 3.2 已知局限与未验证假设

本工作未明确列出“局限性”章节，但基于实验结果可推断以下边界：

1. **规模上限未验证**：最优 $r_a \approx 20\%$ 在 2B–7B 范围内一致，但能否外推至 70B 或更大规模尚无证据。MoE 的专家数量 $E$ 随规模增大而增加，可能改变最优激活率的位置。

2. **数据复用上限**：在 7B 规模上，超过两个 epoch 的多轮训练会持续降低性能（Figure 5）。这意味着数据复用策略存在效益上限，且该上限可能随模型规模和数据质量变化。

3. **架构搜索的完备性**：$\zeta$ 和 $\mu$ 的选择基于趋势观测而非穷举搜索，不能排除存在更优配置的可能性。Table 8 的搜索范围有限（$\zeta \in \{64, 88, 112\}$，$\mu \in \{16, 22, 28\}$），更细粒度的搜索可能发现更优组合。

4. **专家专业化机制未解**：论文明确指出“最优激活率区域与专家专业化程度之间的关系留待未来工作”（§7）。这意味着目前无法从机制层面解释为什么 20% 是最优的，也无法预测何时该规律会失效。

5. **训练方法的局限性**：所有实验基于从零开始的预训练（train from scratch），未涉及 upcycling（从稠密模型初始化 MoE）或 MoEfication（将稠密模型转换为 MoE）。这些方法是否遵循相同的最优激活率规律仍是开放问题。

### 4. 开放问题与未来方向

论文明确提出了以下开放问题（§7, §8）：

1. **最优激活率如何增强模型能力？** 这是一个机制层面的问题——20% 的激活率是否恰好平衡了专家专业化与表示共享之间的权衡？还是与训练动力学（如梯度噪声、负载均衡）有关？

2. **结论是否适用于 Upcycling 和 MoEfication？** 这两种方法在工业界广泛使用（如从 LLaMA 稠密检查点初始化 MoE），但其最优激活率可能因初始化权重的质量而不同于从零训练。

3. **更大规模上的验证**：7B 模型的计算量已达 $2.86 \times 10^{21}$ FLOPs，进一步扩展到 70B 或更大规模需要显著的计算资源，但这是验证结论普适性的必要步骤。

4. **数据复用策略的改进**：当前严格复用方案在知识密集型任务上仍表现出一定退化（Table 2），如何设计更智能的数据复用策略（如基于课程学习的复用）值得探索。

### 5. 知识库定位总结

本工作在 LLM 架构研究知识库中的定位可概括为：

- **问题层面**：填补了“严格相等资源下 MoE vs 稠密”这一基准比较的空白
- **方法层面**：提供了系统性的 MoE 架构优化方案（1dense+SE、$\zeta \approx 88$、$\mu \approx 22$、$r_a \approx 20\%$）
- **实证层面**：以近 200 个 2B 模型和 50+ 个 7B 模型、累计 50 万亿 token 的训练量，建立了当前最全面的 MoE 激活率–性能关系曲线
- **理论层面**：揭示了激活率作为 MoE 性能调节变量的跨规模一致性，但尚未建立理论解释

对于后续研究而言，本工作提供了一个严格的比较基准和可复现的架构配置，但其结论的规模外推性和机制解释仍是需要审慎对待的开放问题。

## 原文 PDF

![[paperPDFs/ICLR_2026/MIXTURE-OF-EXPERTS_CAN_SURPASS_DENSE_LLMS_UNDER_STRICTLY_EQUAL_RESOURCE.pdf]]
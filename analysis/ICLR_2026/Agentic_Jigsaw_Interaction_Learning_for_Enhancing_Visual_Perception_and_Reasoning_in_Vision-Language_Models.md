---
title: Agentic Jigsaw Interaction Learning for Enhancing Visual Perception and Reasoning in Vision-Language Models
type: paper
paper_level: A
venue: ICLR
year: 2026
pdf_ref: paperPDFs/ICLR_2026/Agentic_Jigsaw_Interaction_Learning_for_Enhancing_Visual_Perception_and_Reasoning_in_Vision-Language_Models.pdf
aliases:
- AAJILEVPRV
- AJILEVPRVLM
acceptance: accepted
paradigm: 利用拼图任务的结构化特性作为代理任务，通过代码和规则生成可扩展、难度可控的高质量多模态强化学习数据，结合冷启动（专家轨迹）和GRPO强化学习训练，使模型在交互式求解过程中学会捕捉视觉组件间的结构关系，从而显著提升感知与推理能力，并泛化到多种通用视觉任务。
tags:
- topic/vision_multimodal_applications
- topic/vision_multimodal_applications/vision_models_multimodal
---

# Agentic Jigsaw Interaction Learning for Enhancing Visual Perception and Reasoning in Vision-Language Models

> [!tip] 核心洞察
> 利用拼图任务的结构化特性作为代理任务，通过代码和规则生成可扩展、难度可控的高质量多模态强化学习数据，结合冷启动（专家轨迹）和GRPO强化学习训练，使模型在交互式求解过程中学会捕捉视觉组件间的结构关系，从而显著提升感知与推理能力，并泛化到多种通用视觉任务。

| 字段 | 内容 |
|------|------|
| 中文题名 | 基于智能拼图交互学习的视觉语言模型感知与推理增强 |
| 英文题名 | Agentic Jigsaw Interaction Learning for Enhancing Visual Perception and Reasoning in Vision-Language Models |
| 会议/期刊 | ICLR 2026 (accepted) |
| Links | [paper](https://openreview.net/forum?id=3kouij8BWi) |
| Topic | #topic/vision_multimodal_applications #topic/vision_multimodal_applications/vision_models_multimodal |
| Method | AGILE (Agentic jiGsaw Interaction Learning for Enhancing visual perception and reasoning in VLMs) |
| Dataset | 2×2 Jigsaw (Avg), 3×3 Jigsaw (Avg), MME-RealWorld-Lite, RealWorldQA |

> [!tip] 效果简介
> - 2×2 Jigsaw (Avg) 上，Accuracy 为 82.8，对比 9.5，变化 +73.3。
> - 3×3 Jigsaw (Avg) 上，Accuracy 为 20.8，对比 0.4，变化 +20.4。
> - MME-RealWorld-Lite 上，Accuracy 为 48.4，对比 44.6，变化 +3.8。

## 概述

本文提出 **AGILE (Agentic jiGsaw Interaction Learning for Enhancing visual perception and reasoning in VLMs)** 框架，旨在通过智能拼图交互学习显著提升视觉语言模型（VLM）的感知与推理能力。核心思路是将拼图求解建模为模型与环境之间的多步交互过程，模型通过生成可执行Python代码调用预定义API（Swap、Observe、Crop、Zoom），环境返回细粒度视觉反馈，驱动模型在探索与反馈中迭代改进。实验表明，AGILE在2×2拼图任务上将准确率从9.5%提升至82.8%，在9个通用视觉基准上平均提升3.1%，甚至超越GPT-4o和Gemini-2.5-Pro等闭源模型。

## 背景与动机

当前视觉语言模型（VLM）在基础感知与推理能力上存在严重不足。即使在简单的2×2拼图任务上，基线模型Qwen2.5-VL-7B的准确率仅为9.5%，近乎随机表现。高质量视觉语言强化学习数据稀缺且难以扩展，人工标注成本高昂，闭源模型自动合成质量有限且API成本高，这从根本上限制了VLM感知与推理能力的提升。

现有方法如Jigsaw-R1（Wang et al., 2025e）尝试将拼图作为代理任务，但性能不佳。同时，通用QA数据的RL训练虽然能带来一定提升，但缺乏结构化感知任务的针对性训练。因此，需要一种可扩展、难度可控且能提供细粒度反馈的训练范式。

## 核心创新

AGILE的核心创新在于：

1. **交互式任务建模**：将拼图求解建模为模型与环境之间的逐步交互过程，模型每一步生成可执行代码，环境返回视觉反馈，驱动模型在探索中迭代改进。

2. **基于代码和规则的数据生成**：利用拼图任务的结构化特性，通过代码和规则生成可扩展、难度可控的高质量多模态强化学习数据，天然具有真实标签，可扩展至任意规模。

3. **两阶段训练范式**：冷启动（SFT on 1.6K专家轨迹）→ 强化学习（GRPO on 15.6K拼图数据），结合专家轨迹初始化与交互式RL优化。

4. **多组件奖励设计**：包含准确率奖励（R_acc）、格式奖励（R_format）和步数奖励（R_step），鼓励高效正确的交互。

## 整体框架

![[assets/figures/papers/iclr26_vision_multimodal_applications__vision_models_multimodal__b001_3kouij8BWi_Agentic_Jigsaw_/figures/001_Figure_1.jpg]]
*Figure 1: Description of the action space. (a) illustrates swapping two jigsaw pieces and observing the updated jigsaw state; (b) shows cropping a specific region of the jigsaw for closer inspection; and (c) depicts zooming into a selected area to examine fine-grained details.*

AGILE框架的整体流程如图2所示，包含三个主要阶段：

**阶段一：冷启动数据收集**：使用结构化提示引导Gemini 2.5 Pro与环境交互完成拼图，收集高质量专家轨迹，经筛选和平衡后得到1.6K条轨迹。

**阶段二：冷启动监督微调（SFT）**：在1.6K专家轨迹上对Qwen2.5-VL-7B进行全参数微调，使其具备基本的指令遵循和Python代码生成能力。

**阶段三：GRPO强化学习**：在15.6K拼图数据上使用GRPO算法优化策略模型，奖励函数包含准确率、格式和步数三个部分，通过与环境交互采样轨迹并计算组内相对优势。

![Figure 2]()

## 核心模块与公式推导

### 5.1 拼图环境与交互API

给定输入图像，将其划分为m×m网格的拼图块，随机打乱后得到打乱配置：

$$I_{Shuffle} = {I_1, I_2, \dots, I_{m^2}}$$

真实布局为：

$$I_{GT} = {I_{\pi(1)}, I_{\pi(2)}, \ldots, I_{\pi(m^2)}}$$

求解过程中模型维护当前状态：

$$I_{State} = {I_{\pi^*(1)}, I_{\pi^*(2)}, \dots, I_{\pi^*(m^2)}}$$

模型通过预定义Python API与环境交互：
- **Swap**：交换两个拼图块
- **Observe**：观察当前拼图状态
- **Crop**：裁剪特定区域进行细粒度观察
- **Zoom**：放大选定区域以检查细节

![Figure 1]()

### 5.2 GRPO强化学习目标

AGILE采用组相对策略优化（GRPO），目标函数为：

$$\mathcal{I}_{GRPO}(\theta) = \mathbb{E}_{x \sim \mathcal{D}, \{y_i\}_{i=1}^G \sim \pi_{\text{old}}(\cdot|x; \mathcal{V})} [\frac{1}{G} \sum_{i=1}^G \frac{1}{\sum_{t=1}^{|y_i|} I(y_{i,t})} \sum_{t=1: I(y_{i,t})=1}^{|y_i|} \min(\frac{\pi_\theta(y_{i,t}|x,y_{i,<t};\mathcal{V})}{\pi_{\text{old}}(y_{i,t}|x,y_{i,<t};\mathcal{V})} \hat{A}_{i,t}, \text{clip}(\frac{\pi_\theta(y_{i,t}|x,y_{i,<t};\mathcal{V})}{\pi_{\text{old}}(y_{i,t}|x,y_{i,<t};\mathcal{V})}, 1-\epsilon, 1+\epsilon) \hat{A}_{i,t})] - \beta \mathbb{D}_{KL}(\pi_\theta \| \pi_{\text{ref}})$$

### 5.3 奖励设计

奖励系统包含三个组件：

**准确率奖励**：所有拼图块正确放置时为1，否则为0。

**格式奖励**：输出遵循要求的结构化格式（包含`<think>`、`<code>`和`<answer>`标签）时为1。

**步数奖励**：

$$R_{\text{step}} = \lambda \cdot (\mathbb{I}_{\{R_{\text{acc}}=1\}} \cdot \text{step}_{\text{num}} + \mathbb{I}_{\{R_{\text{acc}}=0\}} \cdot \text{step}_{\text{max}})$$

其中λ = -0.05，仅在拼图正确完成时应用步数奖励，否则施加最大步数惩罚。

**最终奖励**：

$$R = \alpha \cdot R_{\text{acc}} + \beta \cdot R_{\text{format}} + \gamma \cdot R_{\text{step}}$$

其中α=0.8, β=0.2, γ=1.0。

## 实验与分析

![[assets/figures/papers/iclr26_vision_multimodal_applications__vision_models_multimodal__b001_3kouij8BWi_Agentic_Jigsaw_/figures/004_Table_1.jpg]]
*Table 1: Jigsaw Acc result. LN indicates the difficulty level, where N denotes the initial number of correct pieces. A smaller N corresponds to a more scrambled jigsaw and higher difficulty. The best results are highlighted in bold, and the second-best results are underlined.*

![[assets/figures/papers/iclr26_vision_multimodal_applications__vision_models_multimodal__b001_3kouij8BWi_Agentic_Jigsaw_/figures/005_Table_2.jpg]]
*Table 2: Jigsaw Score result. LN indicates the difficulty level, where N denotes the initial number of correct pieces. A smaller N corresponds to a more scrambled jigsaw and higher difficulty. The best results are highlighted in bold, and the second-best results are underlined.*

![[assets/figures/papers/iclr26_vision_multimodal_applications__vision_models_multimodal__b001_3kouij8BWi_Agentic_Jigsaw_/figures/006_Table_3.jpg]]
*Table 3: Main results. Performance comparison of different models on the 9 benchmarks. Abbreviations: MME-RW (MME-RealWorld-Lite), RWQA (RealWorldQA), HRB4K (HRBench4K), HRB8K (HRBench8K), HalBench (HallusionBench), MMMU (MMMU VAL), Avg. denotes the average performance across all 9 benchmarks. ∆ represents the relative performance gain achieved by RL compared to the base model Qwen2.5-VL-7B. The best results are highlighted in bold, and the second-best results are underlined.*

![[assets/figures/papers/iclr26_vision_multimodal_applications__vision_models_multimodal__b001_3kouij8BWi_Agentic_Jigsaw_/figures/015_Table_4.jpg]]
*Table 4: Key hyperparameters for SFT.*

![[assets/figures/papers/iclr26_vision_multimodal_applications__vision_models_multimodal__b001_3kouij8BWi_Agentic_Jigsaw_/figures/016_Table_5.jpg]]
*Table 5: Key hyperparameters for RL.*

### 6.1 拼图任务结果

Table 1展示了2×2和3×3拼图任务的准确率结果。AGILE（Qwen2.5-VL-7B + RL）在2×2拼图上平均准确率82.8%，远超GPT-4o（41.1%）和Gemini-2.5-Pro（46.4%）。在3×3拼图上，AGILE达到20.8%，同样优于Gemini-2.5-Pro（14.6%）和GPT-4o（4.9%）。

![Table 1]()

### 6.2 通用视觉基准泛化

Table 3展示了在9个通用视觉基准上的结果。AGILE在所有基准上均取得提升，平均提升3.1%（从62.1%到65.2%）。其中HRBench8K提升最大（+5.2%），HRBench4K和VStarBench各提升+4.2%。

![Table 3]()

### 6.3 数据规模影响

Figure 3显示，随着训练数据从少量扩展到15.6K，拼图准确率从22.0%提升至82.8%，同时HRBench4K提升+2.0%，RealWorldQA提升+1.8%。

![Figure 3]()

### 6.4 与通用QA数据对比

Table 9显示，在相同训练预算（20K）下，纯拼图RL平均64.9%，优于通用QA RL的64.7%。10K拼图+10K QA组合达到65.5%，优于纯QA的64.7%。

![Table 9]()

### 6.5 消融实验

**奖励系数消融**（Table 6）：去除步数奖励（γ=0）导致性能明显下降，尤其在VStarBench（78.5 vs 80.6）和MMVP（75.3 vs 78.0）上。

**动作空间消融**（Table 8）：去除Crop/Zoom操作导致性能下降（平均63.5 vs 完整动作空间63.9）。

**冷启动规模消融**（Table 10）：扩大SFT数据集（1.6K→2.4K→3.2K）仅带来微小差异，主要性能提升来自RL阶段。

**3×3拼图扩展**（Table 7）：在2×2 RL基础上增加3×3拼图RL训练，9个基准平均进一步提升至65.6。

### 6.6 注意力分析

Figure 12显示，AGILE训练后模型的注意力显著集中在关键视觉元素上（小物体、文本区域、结构重要区域），而原始模型的注意力分散且不稳定。

![Figure 12]()

### 6.7 训练超参数

SFT和RL的关键超参数分别见Table 4和Table 5。

![Table 4]() ![Table 5]()

## 方法谱系与知识库定位

AGILE属于**基于代理任务的视觉强化学习**方法谱系。与现有方法的关键区别在于：

| 维度 | 现有方法 | AGILE |
|------|---------|-------|
| 任务建模 | 静态问答或单步预测 | 多步交互式求解 |
| 数据生成 | 人工标注或闭源模型合成 | 代码和规则生成，可扩展 |
| 动作空间 | 无交互动作或仅文本输出 | Python API（Swap/Observe/Crop/Zoom） |
| 奖励设计 | 仅准确率奖励 | 准确率+格式+步数三组件 |
| 训练范式 | 直接应用RL | 冷启动SFT → GRPO RL |

与Jigsaw-R1（Wang et al., 2025e）相比，AGILE通过交互式环境设计、更丰富的动作空间和更精细的奖励设计，实现了从9.5%到82.8%的质的飞跃。与通用QA RL方法（如R1-V, Chen et al., 2025b）相比，AGILE在相同训练预算下表现更优，证明了结构化感知任务作为代理任务的有效性。

**局限性**：当前实验仅在Qwen2.5-VL-7B上进行；3×3拼图准确率（20.8%）仍有较大提升空间；冷启动阶段依赖Gemini 2.5 Pro收集专家轨迹。

**开放问题**：AGILE能否扩展到更大网格尺寸（4×4、5×5）？能否与其他代理任务（如逻辑谜题、代码生成）结合？冷启动阶段是否可以完全去除？

## 原文 PDF

![[paperPDFs/ICLR_2026/Agentic_Jigsaw_Interaction_Learning_for_Enhancing_Visual_Perception_and_Reasoning_in_Vision-Language_Models.pdf]]

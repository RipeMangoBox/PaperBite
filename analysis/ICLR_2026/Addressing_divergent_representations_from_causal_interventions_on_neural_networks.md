---
title: Addressing divergent representations from causal interventions on neural networks
type: paper
paper_level: A
venue: ICLR
year: 2026
pdf_ref: paperPDFs/ICLR_2026/Addressing_divergent_representations_from_causal_interventions_on_neural_networks.pdf
aliases:
- CLCL
- ADRFCINN
acceptance: accepted
paradigm: 干预表征的发散并不总是有害的：如果发散位于行为零空间中，则对整体功能声明无害；但如果发散激活了隐藏通路或跨越了潜在决策边界，则可能产生误导性确认或潜在行为变化。通过最小化所有干预发散（尤其是因果维度上的发散），可以降低有害发散的风险，并提高干预在分布外场景中的泛化能力。
tags:
- topic/representation_self_supervised_transfer
- topic/representation_self_supervised_transfer/representation_learning
---

# Addressing divergent representations from causal interventions on neural networks

> [!tip] 核心洞察
> 干预表征的发散并不总是有害的：如果发散位于行为零空间中，则对整体功能声明无害；但如果发散激活了隐藏通路或跨越了潜在决策边界，则可能产生误导性确认或潜在行为变化。通过最小化所有干预发散（尤其是因果维度上的发散），可以降低有害发散的风险，并提高干预在分布外场景中的泛化能力。

| 字段 | 内容 |
|------|------|
| 中文题名 | 解决神经网络因果干预中的表征发散问题 |
| 英文题名 | Addressing divergent representations from causal interventions on neural networks |
| 会议/期刊 | ICLR 2026 (accepted) |
| Links | [paper](https://openreview.net/forum?id=cZrTMqYVL6) |
| Topic | #topic/representation_self_supervised_transfer #topic/representation_self_supervised_transfer/representation_learning |
| Method | Counterfactual Latent (CL) loss 及其针对因果子空间的改进版本 |
| Dataset | Boundless DAS (Wu et al., 2023) on 7B LLM, Boundless DAS on 7B LLM, 合成数据集（默认任务）, 合成数据集（默认任务） |

> [!tip] 效果简介
> - Boundless DAS (Wu et al., 2023) on 7B LLM 上，IIA (Interchange Intervention Accuracy) 为 与基线相当或略有提升，对比 基线IIA，变化 对于小ε，IIA保持不变或略有提升。
> - Boundless DAS on 7B LLM 上，EMD (Earth Mover's Distance) 为 降低，对比 基线EMD，变化 EMD随ε增加而降低。
> - 合成数据集（默认任务） 上，IIA 为 0.9988 ± 0.0005 (仅CL损失)，对比 0.997 ± 0.001 (仅DAS行为损失)，变化 +0.0018。

## 概述

本文系统研究了神经网络因果干预方法中普遍存在的表征发散（representational divergence）问题。因果干预技术（如激活修补、Distributed Alignment Search (DAS)）常用于解释神经网络内部机制，但本文发现这些方法产生的干预表征常常偏离目标模型的自然分布。这种发散可能激活隐藏的神经通路或导致潜在的行为变化，从而损害干预解释的忠实性。

核心贡献包括：（1）从理论和实验两个角度证明多种流行因果干预方法均产生发散表征；（2）提出行为零空间（behavioral null-space）概念，区分无害发散与有害发散；（3）引入并改进反事实潜在（Counterfactual Latent, CL）损失，在保持干预解释能力的同时最小化有害发散；（4）在合成数据集和7B LLM上验证了方法的有效性，并发现训练EMD与分布外IIA之间存在显著反相关。

## 背景与动机

### 2.1 因果干预方法

**激活修补（Activation Patching）** 是一种广泛使用的因果干预技术，其核心思想是将中间层的神经活动从一个前向传播“修补”到另一个被破坏的前向传播中。具体方法包括：

- **均值差异向量修补（Mean Difference Vector Patching, MDVP）**：定义干预向量 $\delta_{MD} \in \mathbb{R}^d$ 为两个条件下平均激活的差异，然后将其加到激活上：$\hat{h} = h + \delta_{MD}$（Feng & Steinhardt, 2024）。
- **稀疏自编码器（Sparse Autoencoder, SAE）投影**：通过训练好的编码器 $E: \mathbb{R}^d \to \mathbb{R}^k$ 和线性解码器 $D: \mathbb{R}^k \to \mathbb{R}^d$ 重构表征：$h' = D(E(h))$（Bloom et al., 2024）。
- **分布式对齐搜索（Distributed Alignment Search, DAS）**：使用可学习的可逆线性对齐函数 $A(h) = Wh$ 将隐藏表征 $h \in \mathbb{R}^{d_m}$ 变换为包含正交因果变量子空间和额外零空间的向量 $z \in \mathbb{R}^{d_m}$（Geiger et al., 2021; 2023）。

DAS的交换干预定义为：
$$\hat{h} = \mathcal{A}^{-1}((\mathcal{I} - D_{\mathrm{var}_i})\mathcal{A}(h^{\mathrm{trg}}) + D_{\mathrm{var}_i}\mathcal{A}(h^{\mathrm{src}}))$$

DAS训练损失为：
$$\mathcal{L}_{\mathrm{DAS}}(\mathcal{A}) = -\frac{1}{N}\sum_{k=1}^N \log p_{\mathcal{A}}(c^{(k)} | x^{(k)}, \hat{h}^{(k)})$$

### 2.2 表征发散问题

现有方法如因果擦洗（causal scrubbing）和噪声/去噪激活修补（Wang et al., 2022; LawrenceC et al., 2022; Meng et al., 2023; Chen et al., 2025; Zhang & Nanda, 2024）有意引入发散表征来测试电路属性。然而，本文指出这种发散可能产生误导性结果。Figure 1 展示了因果干预如何招募隐藏电路并跨越决策边界，导致误导性确认或潜在行为变化。

## 核心创新

本文的核心创新在于：

1. **理论框架**：提出行为零空间概念，严格区分无害发散（位于行为零空间内）与有害发散（激活隐藏通路或跨越潜在决策边界）。

2. **反事实潜在（CL）损失**：引入并改进CL损失，在保持干预解释能力的同时最小化干预表征与自然分布之间的发散。

3. **因果子空间改进**：提出仅针对因果子空间的改进版CL损失，进一步提高分布外泛化性能。

## 整体框架

![[assets/figures/papers/iclr26_representation_self_supervised_transfer__representation_learning__b001_cZrTMqYVL6_Address/figures/001_Figure_1.jpg]]
*Figure 1: A*

本文的整体框架包括三个主要阶段：

**阶段一：问题识别与理论分析**
- 通过理论证明（Proposition: coordinate patching exceeds the class radius）和实证分析（Figure 2）证明发散现象的普遍性。
- 定义行为零空间 $\mathcal{N}(\psi, X) = \{ v \in \mathbb{R}^d | \forall x \in X, \psi(x+v) = \psi(x) \}$，区分无害与有害发散。

**阶段二：有害发散机制分析**
- 隐藏通路激活：均值差异修补可激活对所有自然类输入均沉默的单元，从而翻转决策。
- 潜在行为变化：在一个上下文中行为无效的扰动在另一个上下文中改变行为。

**阶段三：缓解方法**
- 引入CL损失：$\mathcal{L}_{CL}(\hat{h}, h_{CL}) = \frac{1}{2} \|\hat{h} - h_{CL}\|_2^2 - \frac{1}{2} \frac{\hat{h} \cdot h_{CL}}{\|\hat{h}\|_2 \|h_{CL}\|_2}$
- 总损失：$\mathcal{L}_{total} = \epsilon \mathcal{L}_{CL} + \mathcal{L}_{DAS}$
- 改进版：仅针对因果子空间维度计算CL损失

## 核心模块与公式推导

### 5.1 发散的理论保证

**坐标修补定理**：如果 $h^{src}, h^{trg} \in \mathcal{M}_K$（即 $\|u\|_2 \leq r_K$ 且 $\|v\|_2 \leq r_K$），则修补点 $\hat{h}$ 在 $u_1^2 + v_2^2 > r_K^2$ 时偏离流形。

修补点与类中心的距离：
$$\hat{h} - c_K = (u_1, v_2)^\top, \quad \lVert \hat{h} - c_K \rVert_2^2 = u_1^2 + v_2^2$$

### 5.2 行为零空间与二元子空间

**行为零空间定义**：
$$\mathcal{N}(\psi, X) = \{ v \in \mathbb{R}^d | \forall x \in X, \psi(x+v) = \psi(x) \}$$

**行为二元子空间条件**：
$$\mathrm{sign}(D_{\mathrm{var}} \mathbf{A}(h)) = \mathrm{sign}(D_{\mathrm{var}} \mathbf{A}(h')) \implies \mathbf{f}(h) = \mathbf{f}(h')$$

### 5.3 CL损失及其改进

**原始CL损失**：
$$\mathcal{L}_{CL}(\hat{h}, h_{CL}) = \frac{1}{2} \|\hat{h} - h_{CL}\|_2^2 - \frac{1}{2} \frac{\hat{h} \cdot h_{CL}}{\|\hat{h}\|_2 \|h_{CL}\|_2}$$

**改进版CL损失（每个因果变量）**：
$$\hat{h}^{\mathrm{var}_i} = \mathcal{A}^{-1}(D_{\mathrm{var}_i} \mathcal{A}(\hat{h})), \quad h_{CL}^{\mathrm{var}_i} = \mathrm{stopgrad}(\mathcal{A}^{-1}(D_{\mathrm{var}_i} \mathcal{A}(h_{CL})))$$

**求和改进版CL损失**：
$$\mathcal{L}_{CL}^{\prime} = \sum_{i=1}^n \mathcal{L}_{CL}^{\mathrm{var}_i}$$

### 5.4 局部投影算子

用于将干预表征投影到自然流形上：
$$\Pi_K(x) = \mu_K + Q_r Q_r^\top (x - \mu_K)$$

## 实验与分析

![[assets/figures/papers/iclr26_representation_self_supervised_transfer__representation_learning__b001_cZrTMqYVL6_Address/figures/005_Table_1.jpg]]

![[assets/figures/papers/iclr26_representation_self_supervised_transfer__representation_learning__b001_cZrTMqYVL6_Address/figures/002_Figure_1.jpg]]
*Figure 1: B Figure 1: Causal interventions can recruit hidden circuits that produce misleadingly confirmatory or dormant behavior. (a) Consider natural pathways (dashed arrows) for two classes A and B that carry activity to different behavioral outputs y. In a hypothetical intervention meant to find path A, patching h ^ { 1 } with a divergent representation can activate distinct, hidden pathways (solid arrows) that result in misleadingly confirmatory behavior (orange) and/or undetected behavior (red). (b) Consider 2D projections of the neural activity of h ^ { 1 } for a different network that classifies states into one of 10 classes (denoted by hue). Suppose that natural representations (dark points)...*

![[assets/figures/papers/iclr26_representation_self_supervised_transfer__representation_learning__b001_cZrTMqYVL6_Address/figures/003_Figure_2.jpg]]
*Figure 2: Representational divergence is a common occurrence across various interventions. (a) Directly replacing a coordinate value in one natural representation (orange) with the value from another will eventually create divergent representations (blue). (b) Top two principal components of natural and corresponding intervened representations, taken from the residual stream at the intervention position and with PCA is performed over the combined set of natural and intervened vectors, for three popular causal intervention techniques: a replication of Feng & Steinhardt (2024) for mean difference patching, reconstructed vectors for a single transformer layer using SAELens (Bloom et al., 2024)...*

![[assets/figures/papers/iclr26_representation_self_supervised_transfer__representation_learning__b001_cZrTMqYVL6_Address/figures/004_Figure_3.jpg]]
*Figure 3: The CL loss reduces representational divergence and can improve out-of-distribution generalization. (a) PCA of natural (orange) and intervened (blue) representations in the Boundless DAS setting presented in Wu et al. (2023) for two CL loss weightings with the same final IIA. (b) IIA (orange) and divergence (purple) of intervened representations from Section 5.1 as a function of CL loss weight (ϵ). (c) Diagram of CL loss; rectangles are model representations and x _ { 1 } and x2 are deterministic values of the representations along the two synthetic causal dimensions shown in panels (d) and (e). We patch the x _ { 2 } value from source to target using DAS and define the CL represe...*

![[assets/figures/papers/iclr26_representation_self_supervised_transfer__representation_learning__b001_cZrTMqYVL6_Address/figures/006_Figure_5.jpg]]

### 6.1 发散现象的普遍性

Figure 2 展示了三种流行因果干预方法（均值差异修补、SAE、DAS）均产生发散表征。PCA可视化显示干预表征（蓝色）与自然表征（橙色）在二维投影中明显分离，且干预分布的Earth Mover's Distance (EMD)显著高于自然-自然基线。

### 6.2 有害发散的具体案例

**隐藏通路激活**：均值差异修补可以激活对所有自然类输入均沉默的单元，从而翻转决策。投影到凸包 $\mathrm{conv}(\mathcal{S}_A)$ 或局部PCA子空间可以防止这种决策翻转。

**潜在行为变化**：相同的干预在一种上下文中行为无效，但在另一种上下文中改变行为。例如，当 $v_4 < 0.75$ 时预测为类A，当 $v_4 > 0.75$ 时预测为类C。

### 6.3 CL损失效果

**7B LLM Boundless DAS设置**：对于小 $\epsilon$ 值，IIA保持不变（甚至略有提升），同时EMD降低（Figure 3B）。

**合成数据集**：

| 指标 | 仅DAS行为损失 | 仅CL损失 | 变化 |
|------|--------------|----------|------|
| IIA | 0.997 ± 0.001 | 0.9988 ± 0.0005 | +0.0018 |
| EMD | 0.032 ± 0.003 | 0.007 ± 0.001 | -0.025 |

**分布外泛化**：Figure 3F 显示，使用CL损失训练的对齐函数在分布外任务上表现优于仅使用行为损失。

### 6.4 训练EMD与OOD IIA的反相关

线性回归结果（Appendix A.6 Table）：
- 系数：-0.3424（标准误 0.039，t=-8.677，p=0.000）
- R² = 0.729
- F(1,28) = 75.28，p < 0.001

这表明训练EMD越低，分布外IIA越高，验证了减少发散对泛化能力的正面影响。

### 6.5 消融实验

- CL损失权重 $\epsilon$ 影响IIA和EMD之间的权衡：小 $\epsilon$ 保持IIA并降低EMD，大 $\epsilon$ 可能降低IIA（Figure 3B）。
- 仅使用CL损失（无行为损失）训练的对齐函数在分布外任务上表现优于仅使用行为损失（Figure 3F）。

### 6.6 公平性说明

- 实验仅使用合成数据集和单个7B LLM（Meta-Llama-3-8B-Instruct），未在多种模型架构或真实世界任务上验证。
- 合成数据集中的类分布是人为构造的，可能无法完全反映真实神经网络表征的复杂性。
- OOD实验中的密集和稀疏分区是人为定义的，可能无法代表真实分布外场景。

## 方法谱系与知识库定位

### 7.1 与现有方法的关系

本文建立在以下工作的基础上：

- **DAS框架**（Geiger et al., 2021; 2023）：提供了因果干预的基本框架，本文在此基础上引入发散缓解机制。
- **Boundless DAS**（Wu et al., 2023）：提供了7B LLM的实验设置，本文在此设置上验证CL损失效果。
- **CL损失**（Grant, 2025）：原始CL损失用于减少发散，本文将其改进为针对因果子空间的版本。
- **行为零空间与隐藏通路**（Makelov et al., 2023）：展示了零空间与隐藏子空间之间的交互可能影响行为。

### 7.2 知识库定位

本文属于**可解释人工智能（XAI）** 和**因果表示学习**交叉领域，具体定位如下：

1. **问题定位**：解决因果干预方法中表征发散导致的忠实性问题，这是机械可解释性（mechanistic interpretability）的核心挑战之一。

2. **方法创新**：将对比学习思想（CL损失）引入因果干预框架，提出针对因果子空间的改进版本，在保持解释能力的同时提高干预的忠实性。

3. **理论贡献**：提出行为零空间框架，为区分无害与有害发散提供了理论基础，并建立了发散度量与分布外泛化之间的定量关系。

4. **实践意义**：为使用因果干预进行电路发现和模型编辑提供了重要指导——干预表征的发散可能产生误导性结果，最小化发散可以降低风险并提高泛化能力。

### 7.3 开放问题与局限性

- 如何在实际应用中自动区分无害发散和有害发散？
- CL损失在更大规模模型（如100B+参数）和更复杂任务上的效果如何？
- 非线性对齐函数下的发散问题是否更严重？如何解决？
- 最小化发散幅度并不能保证消除隐藏通路，只能降低风险面。
- 论文未提供理论保证证明CL损失总能消除有害发散。

## 原文 PDF

![[paperPDFs/ICLR_2026/Addressing_divergent_representations_from_causal_interventions_on_neural_networks.pdf]]

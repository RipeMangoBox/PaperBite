---
title: 3DSMT A Hybrid Spiking Mamba-Transformer for Point Cloud Analysis
type: paper
paper_level: A
venue: ICLR
year: 2026
pdf_ref: paperPDFs/ICLR_2026/3DSMT_A_Hybrid_Spiking_Mamba_Transformer_for_Point_Cloud_Analysis.pdf
aliases:
- T3SS
- 3HSMTPCA
- AHSMTP
acceptance: accepted
core_operator: 将脉冲局部偏移注意力和脉冲 Mamba 块组合成混合 SNN 点云分析架构。
primary_logic: |
  先用脉冲补丁嵌入把点云编码为脉冲令牌，再在堆叠的 Spiking Hybrid Blocks 中交替用 SLOA 捕捉局部几何、用 SMB 融合全局上下文，最后接任务特定头完成分类或分割，并用多数据集实验验证精度-能量权衡。
claims:
- 点云稀疏性与 SNN 事件驱动计算匹配，但需要同时引入局部几何注意力和全局序列建模才能缩小与 ANN 的精度差距。
- 3DSMT 在点云分类与分割任务上取得强 SNN 性能，同时保持较低能耗。
paradigm: supervised spiking neural network training for point cloud analysis
tags:
- topic/iclr_2026
- topic/vision_multimodal_applications
- topic/vision_multimodal_applications/classification_and_understanding
---

# 3DSMT A Hybrid Spiking Mamba-Transformer for Point Cloud Analysis

> [!tip] 核心洞察
> 点云的稀疏性与SNN的事件驱动计算天然契合，但SNN需要同时具备局部几何注意力和全局序列建模能力才能缩小精度差距，因为局部偏移注意力能有效编码点云的细粒度几何结构，而脉冲曼巴块能以线性复杂度融合全局特征，从而使全脉冲混合架构在保持低能耗的同时实现高精度成为可能。

| 字段 | 内容 |
| --------- | ------------------------------------------------------------------------------------ |
| 中文题名  | 3DSMT：用于点云分析的混合脉冲曼巴-Transformer                                        |
| 英文题名  | 3DSMT A Hybrid Spiking Mamba-Transformer for Point Cloud Analysis                    |
| 会议/期刊 | ICLR 2026 (accepted)                                                                 |
| Links     | [paper](https://openreview.net/forum?id=KkoS6y0pHP)                                     |
| Topic | #ICLR_2026 #topic/vision_multimodal_applications #topic/vision_multimodal_applications/classification_and_understanding |
| Method    | 混合脉冲曼巴-Transformer（3DSMT），包含脉冲局部偏移注意力（SLOA）和脉冲曼巴块（SMB） |
| Dataset   | ModelNet40, ScanObjectNN, ShapeNetPart, S3DIS, SemanticKITTI                         |

> [!tip] 效果简介
>
> - 在ModelNet40分类任务上达到95.2%的总体准确率，在对比的SNN点云方法中取得最优结果。
> - 在ScanObjectNN上展现出比ANN和SNN基线更好的精度-能量权衡。
> - 在ShapeNetPart部件分割任务上取得85.1%的实例mIoU，在SNN点云模型中具有竞争力。

## 概述

3DSMT提出一种面向点云分析的混合脉冲曼巴-Transformer架构。模型通过脉冲补丁嵌入、脉冲局部偏移注意力和脉冲曼巴块，将点云局部几何建模与全局特征融合结合起来，在保持SNN低能耗优势的同时提升分类和分割精度。实验覆盖ModelNet40、ScanObjectNN、ShapeNetPart、S3DIS和SemanticKITTI等点云任务，显示该方法在SNN点云模型中具有较好的精度-效率权衡。

## 背景与动机

点云分析在自动驾驶、机器人感知等领域至关重要，但现有方法面临严峻的精度-效率权衡。基于人工神经网络（ANN）的模型，如PointNet++、Point Transformer和Mamba3D，虽然精度高，但计算和能量开销大，难以部署在边缘设备上。脉冲神经网络（SNN）因其事件驱动特性和低能耗而成为有前景的替代方案，但现有SNN点云模型（如SPT系列）在表达能力上往往不足，导致精度差距。

核心瓶颈在于：SNN需要同时捕捉点云的局部几何细节和全局上下文，但现有SNN模块要么缺乏有效的局部注意力机制，要么全局建模能力有限，无法在保持低能耗的同时达到与ANN模型相当的精度。

## 核心创新

核心洞察：点云的稀疏性与SNN的事件驱动计算天然契合，但SNN需要同时具备局部几何注意力和全局序列建模能力才能缩小精度差距，因为局部偏移注意力能有效编码点云的细粒度几何结构，而脉冲曼巴块能以线性复杂度融合全局特征，从而使全脉冲混合架构在保持低能耗的同时实现高精度成为可能。

## 整体框架

![[assets/figures/papers/08b28ce0-d653-4dc0-99b7-bb42a5771876/figures/001_Figure_1.jpg]]
*Figure 1: 3DSMT overview. The model comprises a Spiking Patch Embedding (SPE) module, a sequence of Spiking Hybrid Blocks (SHBs), and a task-specific head. The output of the (i-1)-th SHB serves as the input to the i-th SHB. (a) The SPE module first maps low-dimensional point coordinates into a high-dimensional feature space, which serves as the input to the first SHB. (b) Each SHB integrates a Spiking Local Offset Attention (SLOA) block, a Spiking Mamba Block (SMB), and a Spiking Position Encoding (SPE) module to capture local and global features*

 3DSMT的整体架构如Figure 1所示。模型由三个主要模块组成：

1. **脉冲补丁嵌入（Spiking Patch Embedding, SPE）**：将输入点云划分为补丁，并通过脉冲神经元转换为脉冲令牌序列。
2. **堆叠的脉冲混合块（Spiking Hybrid Blocks）**：每个混合块包含两个核心子模块——脉冲局部偏移注意力（SLOA）用于局部几何建模，脉冲曼巴块（SMB）用于全局特征融合。多个混合块堆叠以逐步提取层次化特征。
3. **任务特定头（Task-specific Head）**：根据任务（分类、分割）将融合后的点特征映射为最终预测。

## 核心模块与公式推导

**脉冲局部偏移注意力（SLOA）**：该模块通过局部邻域内的偏移注意力机制捕捉点云的几何细节。给定输入特征 $O_{i-1}$ 和位置编码 $S_{pos}$，SLOA的更新公式为：

$$
O_i' = \text{SLOA}(\text{LN}(O_{i-1}+S_{pos})) + O_{i-1}
$$

其中LN表示层归一化，残差连接确保梯度流动。SLOA在脉冲域内计算查询、键、值，并通过局部邻域聚合偏移特征，从而高效编码局部几何结构。

**脉冲曼巴块（SMB）**：该模块基于状态空间模型（SSM）实现全局特征融合，具有线性计算复杂度。其更新公式为：

$$
O_i = \text{SMB}(\text{LN}(O_i')) + O_i'
$$

SMB将脉冲令牌序列作为输入，通过可学习的SSM参数进行全局上下文建模，并通过残差连接保留局部信息。双向策略进一步增强了全局建模质量。

两个模块交替堆叠，形成局部-全局交替的混合架构，使模型既能感知精细几何，又能捕获长程依赖。

## 实验与分析

**主要结果**：![[assets/figures/papers/08b28ce0-d653-4dc0-99b7-bb42a5771876/figures/002_Table_1.jpg]]
*Table 1: Classification results on ModelNet40 and ScanObjectNN. ‘-’ denotes that the model did not provide results, The units of OA, Energy and FLOPs are percentage (%), millijoule (mJ) and Gigabyte (G), respectively. ‘w/o vot’ denotes the method without voting strategy, while ‘w vot’ indicates testing with voting strategy applied. Among the SNN-based methods, the best results are presented in bold, and the second-best results are underlined*

 在ModelNet40分类任务上，3DSMT达到95.2%的总体准确率，在对比的SNN点云方法中取得最优结果（Table 1）。在ScanObjectNN上，模型同样展现出优于ANN和SNN基线的精度-能量权衡。在ShapeNetPart部件分割任务上，3DSMT取得85.1%的实例mIoU（Table 3），在SNN模型中具有竞争力。此外，模型在S3DIS语义分割（Table 4）和SemanticKITTI场景分割（Table 5）上也验证了其适用性，同时保持低能耗。

**效率分析**：

![[assets/figures/papers/08b28ce0-d653-4dc0-99b7-bb42a5771876/figures/007_Table_6.jpg]]
*Table 6: Model Efficiency on ModelNet40 (Latency, Memory)*

 Table 6报告了在ModelNet40上的延迟和内存对比，3DSMT在保持低延迟和低内存占用的同时，能量消耗远低于ANN基线。

**消融研究**：Table 7的消融实验表明，移除混合脉冲曼巴-Transformer架构会降低ScanObjectNN上的分类精度，验证了混合设计的必要性。Table 8-9分别分析了SLOA邻域尺度k和令牌数量L的影响，支持局部几何敏感性的设计选择。Table 11显示双向策略对全局建模质量有显著影响。Figure 2进一步分析了阈值-时间步组合对分类精度的影响。

总体而言，实验充分证明了3DSMT在多个点云任务上实现了精度与效率的平衡，且各核心模块的设计均通过消融实验得到验证。

## 方法谱系与知识库定位

3DSMT属于**脉冲神经网络（SNN）点云分析方法**家族，其直接基线包括PointNet++（经典点云基线）、Point Transformer（ANN Transformer基线）、Mamba3D（ANN Mamba基线）以及SPT系列（SNN点云基线）。

**改变的插槽**：

- **全局建模块**：将Transformer或ANN Mamba的全局建模替换为**脉冲曼巴块（SMB）**，实现线性复杂度的全局特征融合。
- **局部几何模块**：在标准点注意力的基础上新增**脉冲局部偏移注意力（SLOA）**，用于在脉冲域内捕捉局部几何关系。

**新增组件**：

- **脉冲补丁嵌入（SPE）**：将点云补丁编码为脉冲令牌。
- **脉冲局部偏移注意力（SLOA）**：局部几何建模。
- **脉冲曼巴块（SMB）**：全局特征融合。

**定位**：3DSMT是首个将脉冲曼巴与脉冲Transformer混合用于点云分析的工作，填补了SNN在点云局部-全局联合建模方面的空白。未来工作可探索更复杂的真实3D场景、硬件部署以及与其他SNN架构的融合。

## 原文 PDF

![[paperPDFs/ICLR_2026/3DSMT_A_Hybrid_Spiking_Mamba_Transformer_for_Point_Cloud_Analysis.pdf]]

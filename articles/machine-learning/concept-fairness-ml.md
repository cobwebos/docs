---
title: 评估并提高机器学习模型中的公平性
titleSuffix: Azure Machine Learning
description: 了解机器学习模型中的公平性以及 Fairlearn Python 包如何帮助你构建更公平的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596356"
---
# <a name="fairness-in-machine-learning-models"></a>机器学习模型中的公平性

了解机器学习模型中的公平性以及 Fairlearn 开源 Python 包如何帮助你构建更公平的模型。

## <a name="what-is-fairness-in-machine-learning-systems"></a>什么是机器学习系统中的公平性？

人工智能和机器学习系统可能会表现出不公平的行为。 定义不公平行为的一种方法是按其损害或对人的影响来定义。 人工智能系统可以造成许多类型的损害。 人工智能造成的两种常见损害是：

- 对分配的损害：人工智能系统会扩展或保留机会、资源或信息。 示例包括招聘、学校招生和贷款。在这些场景中，某个模型在特定人群中挑选优秀候选人的能力可能要强于在其他人群中进行挑选的能力。

- 对服务质量的损害：人工智能系统针对一个人群的工作质量没有针对另一个人群的工作质量好。 例如，语音识别系统针对女性的工作质量可能没有针对男性的工作质量好。

为了减少人工智能系统中的不公平行为，你必须评估并缓解这些损害。

>[!NOTE]
> 公平性是一个社会性的技术难题。 公平性的许多方面（例如公正和正当程序）没有通过量化的公平性指标进行捕获。 另外，许多量化的公平性指标无法同时得到满足。 目标是使人类能够评估不同的缓解策略，然后做出适合其方案的权衡。

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>通过 Fairlearn 进行公平性评估和缓解

Fairlearn 是一个开源 Python 包，它使机器学习系统开发者能够评估其系统的公平性并缓解所看到的公平性问题。

Fairlearn 有两个组件：

- 评估仪表板：这是一个 Jupyter 笔记本小组件，用于评估模型的预测如何影响不同的群体。 它还支持使用公平性和性能指标对多个模型进行比较。
- 缓解算法：一组用于在二元分类和回归中缓解不公平性的算法。

借助这些组件，数据科学家和业务主管能够在公平性和性能之间进行权衡，并选择最适合其需求的缓解策略。

## <a name="fairness-assessment"></a>公平性评估

在 Fairlearn 中，公平性通过一种称为**群体公平性**的方法进行概念化，该方法会询问以下问题：哪些群体有遭受损害的风险？

相关群体（也称为亚群体）是通过**敏感特征**或敏感特性定义的。 敏感特征作为名为 `sensitive_features` 的矢量或矩阵传递给 Fairlearn 估算器。 此术语的意思是，系统设计者在评估群体公平性时应该对这些特征敏感。 需要注意的是，这些特征是否包含由于个人身份信息而产生的隐私影响。 但是“敏感”并不意味着这些特征不应被用来进行预测。

在评估阶段，将通过差异指标对公平性进行量化。 **差异指标**能够以比率或差值的形式评估并比较模型在不同群体中的行为。 Fairlearn 支持两类差异指标：


- 模型性能差异：这些指标集计算所选性能指标的值在不同子群体之间的差异。 示例包括：

  - 准确率差异
  - 错误率差异
  - 精度差异
  - 召回率差异
  - MAE 差异
  - 许多其他差异

- 选择率差异：此指标包含不同子群体之间的选择率差异。 此差异的一个示例是贷款批准率差异。 选择率是指每个分类中归类为 1 的数据点所占的比例（在二元分类中）或者指预测值的分布（在回归中）。

## <a name="unfairness-mitigation"></a>不公平性缓解

### <a name="parity-constraints"></a>奇偶校验约束

Fairlearn 包括了各种不公平性缓解算法。 这些算法支持对预测器行为的一组约束（称为**奇偶校验约束**或条件）。 奇偶校验约束要求预测器行为的某些方面在敏感特征所定义的群体（例如不同的种族）之间具有可比性。 Fairlearn 的缓解算法使用此类奇偶校验约束来缓解所看到的公平性问题。

Fairlearn 支持下列类型的奇偶校验约束：

|奇偶校验约束  | 目的  |机器学习任务  |
|---------|---------|---------|
|人口统计奇偶校验     |  缓解分配损害 | 二元分类、回归 |
|均等几率  | 诊断分配和服务质量损害 | 二元分类        |
|有界群体损失     |  缓解服务质量损害 | 回归 |

### <a name="mitigation-algorithms"></a>缓解算法

Fairlearn 提供了后期处理和约简不公平性缓解算法：

- 约简：这些算法采用标准的黑盒 ML 估算器（例如，LightGBM 模型），并使用一系列重新加权的训练数据集生成一组重新训练的模型。 例如，某一性别的申请者可能会被提高或降低权重，然后重新训练模型，降低不同性别群体之间的差异。 然后，用户可以选择一个模型，该模型在准确性（或其他性能指标）与差异之间提供最佳的权衡，这一权衡通常需要基于业务规则和成本计算。  
- 后期处理：这些算法采用现有分类器和敏感特征作为输入。 然后，它们将派生分类器的预测转换，以强制实施指定的公平性约束。 阈值优化的最大优势在于其简易性和灵活性，因为它不需要重新训练模型。 

| 算法 | 说明 | 机器学习任务 | 敏感特征 | 支持的奇偶校验约束 | 算法类型 |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | [A Reductions Approach to Fair Classification](https://arxiv.org/abs/1803.02453)（公平分类的约简方法）中描述的公平分类的黑盒方法 | 二元分类 | 分类 | [人口统计奇偶校验](#parity-constraints)、[均等几率](#parity-constraints) | 约简 |
| `GridSearch` | [A Reductions Approach to Fair Classification](https://arxiv.org/abs/1803.02453)（公平分类的约简方法）中描述的黑盒方法| 二元分类 | Binary | [人口统计奇偶校验](#parity-constraints)、[均等几率](#parity-constraints) | 约简 |
| `GridSearch` | 一种黑盒方法，它通过[公平回归：量化的定义和基于约简的算法](https://arxiv.org/abs/1905.12843)中描述的用于有界群体损失的算法实现公平回归的网格搜索变体。 | 回归 | Binary | [有界群体损失](#parity-constraints) | 约简 |
| `ThresholdOptimizer` | 基于 [Equality of Opportunity in Supervised Learning](https://arxiv.org/abs/1610.02413)（监督式学习中的机会均等性）一文的后期处理算法。 此方法采用现有分类器和敏感特征作为输入，并派生分类器预测的单一转换，以强制实施指定的奇偶校验约束。 | 二元分类 | 分类 | [人口统计奇偶校验](#parity-constraints)、[均等几率](#parity-constraints) | 后处理 |

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用各种组件，请参阅 [Fairlearn GitHub 存储库](https://github.com/fairlearn/fairlearn/)和[示例笔记本](https://github.com/fairlearn/fairlearn/tree/master/notebooks)。
- 了解如何使用[差异隐私和 WhisperNoise 包](concept-differential-privacy.md)来保留数据隐私。
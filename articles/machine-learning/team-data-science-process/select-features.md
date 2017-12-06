---
title: "团队数据科学过程中的特征选择 | Microsoft Docs"
description: "介绍特征选择的目的，并提供其在机器学习数据增强过程中作用的相关示例。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 878541f5-1df8-4368-889a-ced6852aba47
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: zhangya;bradsev
ms.openlocfilehash: c69e7a7e86ccea2073209eabe123049e59a2b8e3
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Team Data Science Process (TDSP) 中的功能选择
本文介绍特征工程的目的，并提供其在机器学习数据增强过程中作用的相关示例。 这些示例来自 Azure 机器学习工作室。 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

特征的工程设计和选择是 [Team Data Science Process (TDSP) 是什么？](overview.md)一文中所述的 Team Data Science Process (TDSP) 的一部分。 特征工程和选择是 TDSP 的**开发特征**步骤的一部分。

* **特征工程**：该过程尝试从数据中的现有原始特征创建其他相关特征，并提高学习算法的预测能力。
* **特征选择**：该过程选择原始数据特征的关键子集，试图降低定型问题的维度。

通常，首先应用**特征工程**生成其他特征，然后执行**特征选择**步骤，消除不相关、冗余或高度相关的特征。

## <a name="filter-features-from-your-data---feature-selection"></a>从数据中筛选特征 - 特征选择
特征选择是一个过程，通常适用于为预测建模任务（如分类或回归任务）构建定型数据集。 目标是从原始数据集中选择特征子集，即通过使用最少特征集来表示数据中最大方差量以减少其维度。 此特征子集用于定型模型。 特征选择有两个主要目的。

* 第一，特征选择通常通过消除不相关、冗余或高度相关的特征，来提高分类准确度。
* 第二，它会减少特征数，这使模型定型过程更高效。 高效对于定型成本高昂的学习器（例如支持向量机）尤其重。要。

虽然特征选择确实试图减少数据集中用于定型模型的特征数，但不会将它称为术语“降维”。 特征选择方法提取数据中原始特征的子集，而不会改变它们。  降维方法采用可以转换原始特征并且可对它们进行修改的工程特征。 降维方法示例包括主成分分析、典型相关分析和奇异值分解。

其中特征选择方法在监管上下文中的一个广泛应用类别称为“基于筛选器的特征选择”。 通过评估每个特征和目标属性之间的相关性，这些方法应用统计测量来为每个特征分配分数。 将按分数对这些特征排名，从而有助于基于该排名针对保留或消除特定特征设置阈值。 这些方法中使用的统计度量值示例包括积差相关、互信息和卡方分布检验。

在 Azure 机器学习工作室中，为特征选择提供了多个模块。 如下图中所示，这些模块包括[基于筛选器的特征选择][filter-based-feature-selection]和[费舍尔线性判别分析][fisher-linear-discriminant-analysis]。

![特征选择示例](./media/select-features/feature-Selection.png)

例如，考虑使用[基于筛选器的特征选择][filter-based-feature-selection]模块。 为方便起见，继续使用文本挖掘示例。 假设在通过[特征哈希][feature-hashing]模块创建了一组 256 个特征后想要生成回归模型，并且响应变量为“Col1”（包含范围为 1 到 5 的书籍审核评级）。 通过将“特征评分方法”设为“皮尔逊相关”，“目标列”将设为“Col1”，而“所需特征数”将设为 50。 然后，[基于筛选器的特征选择][filter-based-feature-selection]模块将生成一个包含 50 个特征且目标属性为“Col1”的数据集。 下图显示了此实验的流程以及输入参数：

![特征选择示例](./media/select-features/feature-Selection1.png)

下图显示了生成的数据集：

![特征选择示例](./media/select-features/feature-Selection2.png)

基于特征本身和目标属性“Col1”之间的皮尔逊相关对每个特征进行评分。 保留高分数的特征。

下图显示了所选特征的相应分数：

![特征选择示例](./media/select-features/feature-Selection3.png)

通过应用此[基于筛选器的特征选择][filter-based-feature-selection]模块，将从256 个特征中选出 50 个，因为它们基于评分方法“皮尔逊相关”具有与目标变量“Col1”最相关的特征。

## <a name="conclusion"></a>结束语
特征工程和特征选择是两个常见的工程和选择的特征，用于提高定型过程的效率，此过程尝试提取数据中包含的关键信息。 它们还提高了这些模型准确分类输入数据以及更加可靠地预测感兴趣的结果的能力。 特征工程和选择也可以结合起来使用，以使学习在计算上更易处理。 它通过增加然后减少校准或定型模型所需的特征数量来实现这一目标。 从数学上来说，所选的定型模型的特征是一组最小的独立变量，它们解释数据中的模式，并成功预测结果。

并不总是必须执行特征工程或特征选择。 根据收集的数据、选择的算法以及实验的目的决定是否需要。

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/


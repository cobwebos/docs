---
title: "在 Azure 机器学习中调试模型 | Microsoft Docs"
description: "如何在 Azure 机器学习中调试“训练模型”和“评分模型”模块生成的错误。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bradsev;garye
ms.translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: d4cc94a6395ea45bccf65d9a9f3118ec98cb258d
ms.contentlocale: zh-cn
ms.lasthandoff: 03/17/2017

---
# <a name="debug-your-model-in-azure-machine-learning"></a>在 Azure 机器学习中调试模型

本文解释了运行模型时可能遇到以下两种故障之一的潜在原因：

* [训练模型][train-model]模块产生错误 
* [评分模型][score-model]模块生成不正确的结果 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>训练模型模块产生错误

![image1](./media/machine-learning-debug-models/train_model-1.png)

[训练模型][train-model]模块需要两个输入：

1. 来自 Azure 机器学习提供的模型集合的机器学习模型的类型。
2. 包含指定标签列的训练数据，标签列指定要预测的变量（其余列假定为特征）。

在以下情况下，此模块可能引发错误：

1. 未正确指定标签列。 如果将多个列选择为标签，或者选择的列索引不正确，则可能发生这种情况。 例如，如果列索引 30 用于只有 25 列的输入数据集，则会出现第二种情况。

2. 数据集不包含任何特征列。 例如，如果输入数据集只有一列（该列标记为标签列），则没有可用于生成模型的任何特征。 在此情况下，[训练模型][train-model]模块产生错误。

3. 输入数据集（特征或标签）包含无穷大作为值。

## <a name="score-model-module-produces-incorrect-results"></a>“评分模型”模块生成不正确的结果

![image2](./media/machine-learning-debug-models/train_test-2.png)

在用于监督学习的典型训练/测试实验中，[拆分数据][split]模块将原始数据集分为两部分：一部分用于训练模型，另一部分用于对已训练模型的执行程度评分。 然后使用已训练模型对测试数据进行评分，之后评估结果以确定模型的准确性。

[评分模型][score-model]模块需要两个输入：

1. 来自[训练模型][train-model]模块的已训练模型输出。
2. 与用于训练模型的数据集不同的评分数据集。

即使实验成功，[评分模型][score-model]模块也可能生成不正确的结果。 几种情形可能会导致发生这种情况：

1. 如果指定标签分类，并且基于数据训练回归模型，则[评分模型][score-model]模块会生成不正确的输出。 这是因为回归需要连续响应变量。 在这种情况下，更适合使用分类模型。 

2. 同样地，如果基于标签列中具有浮点数的数据集训练分类模型，可能会生成不合需要的结果。 这是因为分类需要离散响应变量，其值仅允许有限范围、通常略小的类集。

3. 如果评分数据集未包含用于训练模型的所有特征，[评分模型][score-model]会生成错误。

4. 如果评分数据集中的某行对于其任意特征包含缺少值或无限值，则[评分模型][score-model]不会生成任何对应于该行的输出。

5. [评分模型][score-model]可能会为评分数据集中的所有行生成相同的输出。 例如，当尝试使用决策林分类时，如果选择的每个叶节点的最小示例数大于可用的训练示例数，就会发生这种情况。

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/



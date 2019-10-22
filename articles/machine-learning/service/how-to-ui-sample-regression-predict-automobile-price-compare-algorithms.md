---
title: '视觉对象接口示例 #3：回归到价格和比较算法'
titleSuffix: Azure Machine Learning
description: 本文说明如何在不使用可视界面编写代码的情况下生成复杂的机器学习管道。 了解如何定型和比较多个回归模型，以根据技术特征预测汽车的价格
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 651644eaae910792aac2144531d09afc4cde7153
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692796"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>示例 2-回归：预测价格和比较算法

了解如何生成复杂的机器学习管道，而无需使用可视界面编写单行代码。 本示例训练和比较多个回归模型，以根据其技术特征预测汽车的价格。 我们将为在此管道中做出的选择提供基本原理，以便您能够应对自己的机器学习问题。

如果只是开始使用机器学习，请查看此管道的[基本版本](how-to-ui-sample-regression-predict-automobile-price-basic.md)。

下面是此管道的完成关系图：

[管道的 ![Graph](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>必备组件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 选择示例2管道的 "**打开**" 按钮：

    ![打开管道](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="pipeline-summary"></a>管道摘要

使用以下步骤生成机器学习管道：

1. 获取数据。
1. 预先处理数据。
1. 训练模型。
1. 测试、评估和比较模型。

## <a name="get-the-data"></a>获取数据

此示例使用从 UCI 机器学习存储库中的 "**汽车价格数据（原始）** " 数据集。 此数据集包含26列，其中包含有关汽车的信息，包括品牌、型号、价格、车辆功能（如柱面数）、MPG 和保险风险评分。

## <a name="pre-process-the-data"></a>预先处理数据

主要的数据准备任务包括数据清除、集成、转换、缩减、离散化或量化。 在 visual interface 中，可以在左侧面板的 "**数据转换**" 组中找到用于执行这些操作和其他数据预处理任务的模块。

使用 "**选择数据集中的列**" 模块可以排除具有多个缺失值的规范化损失。 然后，使用 "**清理缺失数据**" 删除包含缺失值的行。 这有助于创建一组清晰的定型数据。

![数据预处理](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>定型模型

机器学习问题有所不同。 常见的机器学习任务包括分类、群集、回归和推荐器系统，其中每个系统都可能需要不同的算法。 选择的算法通常取决于用例的要求。 选取算法后，需要优化其参数，以训练更准确的模型。 然后，需要基于指标（如准确性、intelligibility 和效率）评估所有模型。

由于此管道的目标是预测汽车价格，并且由于标签列（价格）包含实数，因此回归模型是一个不错的选择。 考虑到功能数量相对较小（小于100），并且这些功能不是稀疏的，决策边界可能是非线性的。

为了比较不同算法的性能，我们使用两个非线性算法，**提升决策树回归**和**决策林回归**，以生成模型。 这两种算法都具有可更改的参数，但此示例使用此管道的默认值。

使用**拆分数据**模块随机划分输入数据，以便定型数据集包含70% 的原始数据，并且测试数据集包含30% 的原始数据。

## <a name="test-evaluate-and-compare-the-models"></a>测试、评估和比较模型

您可以使用两组随机选择的数据来定型和测试模型，如前一部分中所述。 拆分数据集并使用不同的数据集定型和测试模型，使模型的评估更具目标。

在对模型进行定型后，使用 "**评分模型**" 和 "**评估模型**" 模块来生成预测结果并对模型进行评估。 **评分模型**通过使用训练的模型生成测试数据集的预测。 然后，将评分传递到**评估模型**以生成评估指标。

在此管道中，使用两个 "**评估模型**" 实例来比较两个模型对。

首先，比较定型数据集上的两种算法。
其次，比较测试数据集上的两种算法。

下面是结果：

![比较结果](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

这些结果表明，使用**提升决策树回归**生成的模型具有低于**决策林回归**构建模型的根平均平方误差。

与不可见的测试数据集相比，这两种算法在定型数据集上的错误更小。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于可视界面的其他示例：

- [示例 1-回归：预测汽车的价格](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [示例 3-分类：预测信用风险](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [示例 4-分类：预测信用风险（区分成本）](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [示例 5-分类：预测改动](how-to-ui-sample-classification-predict-churn.md)
- [示例 6-分类：预测航班延迟](how-to-ui-sample-classification-predict-flight-delay.md)
- [示例 7-文本分类：书籍评论](how-to-ui-sample-text-classification.md)

---
title: 回归：预测价格
titleSuffix: Azure Machine Learning service
description: 了解如何生成机器学习模型来预测汽车的价格，而无需编写一行代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 9dfa4b62f5cb79a5716f6f29651e85d0f8a3a409
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787844"
---
# <a name="sample-1---regression-predict-price"></a>示例 1-回归：预测价格

了解如何生成机器学习回归模型，而无需编写一行代码使用直观的界面。

此实验火车**决策林回归量**来预测一辆汽车的价格基于技术的功能，例如品牌、 型号、 马力，以及大小。 因为我们正在尝试回答"多少？" 这称为回归问题。 不过，您可以应用在此试验中来解决任何类型的机器学习问题，无论是回归、 分类、 聚类分析，等的相同基本步骤。

训练机器学习模型的基本步骤如下：

1. 获取数据
1. 预处理数据
1. 训练模型
1. 评估模型

以下是我们将致力于实验的最终、 已完成关系图。 可以自己进行类似的决定，因此，我们将为所有模块提供基本原理。

![实验的关系图](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>必备组件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 选择**打开**示例 1 实验的按钮：

    ![打开试验](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>获取数据

在此试验中，我们将使用**汽车价格数据 （原始）** 数据集，这是从 UCI 机器学习存储库。 数据集包含 26 个列包含有关汽车，包括制造商、 型号、 价格、 车辆功能 （如气缸数）、 MPG、 和保险风险评分的信息。 本实验的目标是预测汽车的价格。

## <a name="pre-process-the-data"></a>预处理数据

主数据准备任务包括执行数据清理、 集成、 转换、 缩短，原本和离散化或量化。 在可视化界面，您可以找到模块来执行这些操作和其他数据预先处理中的任务**数据转换**组在左侧面板中。

我们使用**选择数据集中的列**模块，以排除规范化损失具有多个缺失值。 然后，我们使用**清理缺失数据**删除具有缺失值的行。 这有助于创建干净的定型数据集。

![数据预处理](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>训练模型

机器学习问题而有所不同。 常见的机器学习任务包括分类，聚类分析，回归和推荐器系统，其中每个可能需要不同的算法。 所选的算法通常取决于用例的要求。 在选择一种算法后，您需要调整其参数来定型更准确的模型。 然后需要评估准确性、 清晰度和效率等度量值所基于的所有模型。

由于本实验的目标是预测汽车价格，并且因为标签列 （价格） 包含实数，回归模型是一个不错的选择。 考虑到，许多功能是相对较小 （小于 100），这些功能不是稀疏，则很可能是非线性的决策边界。 因此，我们使用**决策林回归**对于此试验。

我们使用**拆分数据**模块来将输入的数据随机划分，以便训练数据集包含 70%的原始数据和测试数据集包含 30%的原始数据。

## <a name="test-evaluate-and-compare"></a>测试、 评估和比较

 我们将数据集拆分，并使用不同的数据集来训练和测试模型，以便执行更客观的模型的评估。

训练模型后，我们使用**评分模型**并**评估模型**模块来生成预测的结果和评估模型。

**评分模型**使用经过训练的模型生成测试数据集的预测。 若要检查的结果，请选择的输出端口**评分模型**，然后选择**可视化**。

![评分结果](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

我们然后传递到分数**评估模型**模块生成的评估指标。 若要检查的结果，请选择的输出端口**评估模型**，然后选择**可视化**。

![评估结果](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

了解可用于可视化界面的其他示例：

- [示例 2-回归：比较进行汽车价格预测算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [示例 3-分类：预测信用风险](ui-sample-classification-predict-credit-risk-basic.md)
- [示例 4-分类：预测信用风险 （成本敏感）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [示例 5-分类：预测客户流失](ui-sample-classification-predict-churn.md)
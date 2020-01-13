---
title: 设计器：预测车载价格（基本）示例
titleSuffix: Azure Machine Learning
description: 构建 ML 回归模型来预测汽车的价格，无需使用 Azure 机器学习设计器编写单行代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 156b963fc1644d1f863d8ddd1d86c15b311e18a8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763379"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>通过 Azure 机器学习设计器使用回归预测车载价格

**设计器（预览）示例1**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

了解如何在不编写代码的情况下使用设计器（预览）生成机器学习回归模型。

此管道根据品牌、型号、动力和大小等技术功能，训练**线性回归量**来预测汽车的价格。 因为你要尝试回答 "多少？" 这称为回归问题。 不过，您可以在本示例中应用相同的基本步骤来处理任何类型的机器学习问题，无论是回归、分类、群集等。

训练机器学习模型的基本步骤如下：

1. 获取数据
1. 预先处理数据
1. 定型模型
1. 评估模型

下面是管道的最终完成关系图。 本文提供了所有模块的基本原理，因此你可以自行做出类似的决策。

![管道的图形](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>必备组件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 单击示例1将其打开


## <a name="get-the-data"></a>获取数据

此示例使用从 UCI 机器学习存储库中的 "**汽车价格数据（原始）** " 数据集。 数据集包含26列，其中包含有关汽车的信息，包括品牌、型号、价格、车辆功能（如柱面数）、MPG 和保险风险评分。 此示例的目标是预测汽车的价格。

## <a name="pre-process-the-data"></a>预先处理数据

主要的数据准备任务包括数据清除、集成、转换、缩减、离散化或量化。 在设计器中，可以在左侧面板的 "**数据转换**" 组中找到用于执行这些操作和其他数据预处理任务的模块。

使用 "**选择数据集中的列**" 模块可以排除具有多个缺失值的规范化损失。 然后，使用 "**清理缺失数据**" 删除包含缺失值的行。 这有助于创建一组清晰的定型数据。

![数据预处理](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>定型模型

机器学习问题有所不同。 常见的机器学习任务包括分类、群集、回归和推荐器系统，其中每个系统都可能需要不同的算法。 选择的算法通常取决于用例的要求。 选取算法后，需要优化其参数，以训练更准确的模型。 然后，需要基于指标（如准确性、intelligibility 和效率）评估所有模型。

由于本示例的目的是预测汽车价格，并且由于标签列（价格）包含实数，因此回归模型是一个不错的选择。 考虑到功能数量相对较小（小于100），并且这些功能不是稀疏的，决策边界可能是非线性的。 我们对此管道使用**决策林回归**。

使用**拆分数据**模块随机划分输入数据，以便定型数据集包含70% 的原始数据，并且测试数据集包含30% 的原始数据。

## <a name="test-evaluate-and-compare"></a>测试、评估和比较

拆分数据集并使用不同的数据集定型和测试模型，使模型的评估更具目标。

在对模型进行定型后，可以使用 "**评分模型**" 和 "**评估模型**" 模块来生成预测结果并对模型进行评估。

**评分模型**通过使用训练的模型生成测试数据集的预测。 若要检查结果，请选择 "**评分模型**" 的输出端口，然后选择 "**可视化**"。

![评分结果](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

将评分传递到 "**评估模型**" 模块以生成评估指标。 若要检查结果，请选择 "**评估模型**" 的输出端口，然后选择 "**可视化**"。

![评估结果](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：

- [示例 2-回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 3-通过功能选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 4-分类：预测信用风险（区分成本）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 5-分类：预测改动](how-to-designer-sample-classification-churn.md)
- [示例 6-分类：预测航班延迟](how-to-designer-sample-classification-flight-delay.md)
- [示例 7-文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)
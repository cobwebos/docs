---
title: 设计器：预测汽车价格（高级）示例
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设计器生成并比较多个机器学习回归模型，以基于技术特征来预测汽车的价格。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: a80a1567c84ff3c2eda8ad22391aa862bb7d9d82
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915820"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>使用 Azure 机器学习设计器训练并比较多个回归模型来预测汽车价格

**设计器（预览版）示例 2**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

了解如何使用设计器（预览版）在不编写代码的情况下生成机器学习管道。 此示例训练并比较多个回归模型，以基于其技术特征预测汽车的价格。 我们将提供在此管道中所做选择的基本原理，以便你可以解决自己的机器学习问题。

如果你刚开始了解机器学习，请看一下此管道的[基本版本](how-to-designer-sample-regression-automobile-price-basic.md)。

下面是此管道的已完成图形：

[![管道图形](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png)](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 单击示例 2 将其打开。 

## <a name="pipeline-summary"></a>管道摘要

使用以下步骤生成机器学习管道：

1. 获取数据。
1. 预处理数据。
1. 定型模型。
1. 测试、评估并比较模型。

## <a name="get-the-data"></a>获取数据

此示例使用来自 UCI 机器学习存储库的“汽车价格数据(原始)”数据集  。 此数据集包含 26 个列，其中包含有关汽车的信息，包括品牌、型号、价格、车辆特征（例如缸数）、MPG 和保险风险评分。

## <a name="pre-process-the-data"></a>预处理数据

主要的数据准备任务包括数据清理、集成、转换、缩减、离散化或量化。 在设计器中，可以在左侧面板的“数据转换”  组中找到用于执行这些操作和其他数据预处理任务的模块。

使用“选择数据集中的列”模块排除具有许多缺失值的标准化损耗  。 然后，使用“清理缺失数据”删除包含缺失值的行  。 这有助于创建一组清晰的训练数据。

![数据预处理](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>定型模型

机器学习问题各有不同。 常见的机器学习任务包括分类、聚类、回归和推荐器系统，每个系统可能需要不同的算法。 选择哪个算法通常取决于用例的要求。 选取算法后，需要优化其参数，以训练更准确的模型。 然后，需要基于指标（例如准确度、可理解性和效率）评估所有模型。

由于此管道的目标是预测汽车价格，并且由于标签列（价格）包含实数，因此回归模型是一个不错的选择。

为了比较不同算法的性能，我们将使用两个非线性算法（“提升决策树回归”和“决策林回归”）来生成模型   。 这两种算法都具有可更改的参数，但此示例使用了此管道的默认值。

使用“拆分数据”模块来随机分割输入数据，使训练数据集包含 70% 的原始数据，使测试数据集包含 30% 的原始数据。 

## <a name="test-evaluate-and-compare-the-models"></a>测试、评估并比较模型

使用两组随机选择的数据进行训练，然后测试模型，如前一部分所述。 将拆分数据集并使用不同的数据集来训练和测试模型，使模型的评估更加客观。

在对模型进行训练后，使用“评分模型”和“评估模型”模块来生成预测结果并评估模型。   “评分模型”使用训练后的模型针对测试数据集生成预测  。 然后将评分传递给“评估模型”以生成评估指标。 



结果如下：

![比较结果](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/result.png)

这些结果表明，与基于“决策林回归”生成的模型相比，使用“提升决策树回归”生成的模型具有更小的均方根误差   。



## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：

- [示例 1 - 回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 3 - 通过特征选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 4 - 分类：预测信用风险（代价敏感）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 5 - 分类：预测流失率](how-to-designer-sample-classification-churn.md)
- [示例 6 - 分类：预测航班延误](how-to-designer-sample-classification-flight-delay.md)
- [示例 7 - 文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)

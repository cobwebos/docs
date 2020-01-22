---
title: 设计器：预测改动示例
titleSuffix: Azure Machine Learning
description: 按照此分类示例预测 Azure 机器学习设计器 & 提升决策树的变动。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 88f688608a0ae3d435699362f9326c7c02d494a4
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311104"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>使用提升决策树预测 Azure 机器学习设计器的变动

**设计器示例5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

了解如何在不编写代码的情况下使用设计器生成复杂的机器学习管道。

此管道培训 2**个双类提升决策树**分类器，用于预测客户关系管理（CRM）系统的常见任务-客户流失。 数据值和标签跨多个数据源进行拆分，并打乱编码为匿名的客户信息，但我们仍可以使用该设计器组合数据集，并使用这些模糊值来训练模型。

因为你要尝试回答问题 "哪一个？" 这称为分类问题，但你可以应用此示例中所示的相同逻辑来处理任何类型的机器学习问题，无论是回归、分类、群集等。

下面是此管道的完成关系图：

![管道图形](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>必备组件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 单击 "示例 5" 将其打开。 

## <a name="data"></a>数据

此管道的数据来自 KDD 杯2009。 它包含50000行和230功能列。 该任务旨在预测使用这些功能的客户的变动量、亲和力和向上销售情况。 有关数据和任务的详细信息，请参阅[KDD 网站](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)。

## <a name="pipeline-summary"></a>管道摘要

设计器中的此示例管道显示了对变动、亲和力和向上销售的二元分类器预测，这是客户关系管理（CRM）的常见任务。

首先，一些简单的数据处理。

- 原始数据集具有多个缺失值。 使用 "**清理缺失数据**" 模块将缺失值替换为0。

    ![清理数据集](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- 功能和相应的改动在不同的数据集中。 使用 "**添加列**" 模块将标签列追加到特征列。 第一列**Col1**是标签列。 从可视化结果中可以看到，数据集不均衡。 有比正示例更多的负数（-1）示例（+ 1）。 稍后，我们将使用**SMOTE**模块增加常见情况。

    ![添加列数据集](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- 使用 "**拆分数据**" 模块将数据集拆分为定型集和测试集。

- 然后，将提升决策树二进制分类器与默认参数一起使用，以生成预测模型。 为每个任务生成一个模型，即每个模型预测向上销售、亲和力和变动。

- 在管道的右侧，我们使用**SMOTE**模块来增加正示例的百分比。 SMOTE 百分比设置为100，表示正的示例。 详细了解 SMOTE 模块如何与[SMOTE module reference0](algorithm-module-reference/smote.md)一起使用。

## <a name="results"></a>结果

可视化 "**评估模型**" 模块的输出，以查看该模型在测试集上的性能。 

![评估结果](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 可以移动 "**阈值**" 滑块，并查看二元分类任务的指标变化。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：

- [示例 1-回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2-回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 3-通过功能选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 4-分类：预测信用风险（区分成本）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 6-分类：预测航班延迟](how-to-designer-sample-classification-flight-delay.md)
- [示例 7-文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)

---
title: 设计器：预测客户流失示例
titleSuffix: Azure Machine Learning
description: 按照此分类示例使用 Azure 机器学习设计器和提升决策树来预测客户流失。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 701bf186080ef627ef4621e959b281fd58aa4132
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963354"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>通过 Azure 机器学习设计器使用提升决策树来预测客户流失

**设计器（预览版）示例 5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

了解如何使用设计器（预览版）在不编写代码的情况下生成复杂的机器学习管道。

此管道训练 2 个“双类提升决策树”分类器来预测客户管理 (CRM) 系统的常见任务 - 客户流失  。 数据值和标签被分成多个数据源，并被无序收集以匿名化客户信息，但是，我们仍然可以使用设计器来组合数据集，并使用模糊值来训练模型。

因为你尝试回答问题“哪一个？”， 因此这称为分类问题，但是，你可以应用此示例中显示的同一逻辑来解决任何类型的机器学习问题，无论是回归、分类还是聚类，等等。

下面是此管道的已完成图形：

![管道图形](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>必备条件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 单击示例 5 将其打开。 

## <a name="data"></a>数据

此管道的数据来自 KDD Cup 2009。 它有 50,000 行和 230 个特征列。 任务是为使用这些特征的客户预测客户流失、购买欲和追加销售。 有关数据和任务的详细信息，请参阅 [KDD 网站](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)。

## <a name="pipeline-summary"></a>管道摘要

设计器中的此示例管道显示了客户流失、购买欲和追加销售的二元分类器预测，这是客户关系管理 (CRM) 的一个常见任务。

首先完成一些简单的数据处理。

- 原始数据集有许多缺失值。 使用“清理缺失数据”模块将缺失值替换为 0  。

    ![清理数据集](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- 特征和对应的客户流失位于不同的数据集中。 使用“添加列”模块将标签列追加到特征列  。 第一个列“Col1”是标签列  。 从可视化结果中，我们可以看到数据集不平衡。 负示例 (-1) 比正示例 (+1) 要多。 稍后我们将使用“SMOTE”模块来增加未被充分代表的案例  。

    ![添加列数据集](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- 使用“拆分数据”模块将数据集拆分为训练集和测试集  。

- 然后，使用提升决策树二元分类器生成预测模型。 为每个任务生成一个模型，也就是说，各个模型分别预测追加销售、购买欲和客户流失。

- 在管道的右侧部分，使用“SMOTE”模块来增加正示例的百分比  。 SMOTE 百分比设置为 100 以使正示例加倍。 通过 [SMOTE 模块参考](algorithm-module-reference/smote.md)详细了解 SMOTE 模块的工作原理。

## <a name="results"></a>结果

将“评估模型”模块的输出可视化来查看模型处理测试集时的性能  。 

![评估结果](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 可以移动“阈值”滑块并查看二元分类任务的指标变化  。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：

- [示例 1 - 回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2 - 回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 3 - 通过特征选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 4 - 分类：预测信用风险（代价敏感）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 6 - 分类：预测航班延误](how-to-designer-sample-classification-flight-delay.md)
- [示例 7 - 文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)

---
title: 设计器：分类，预测收入示例
titleSuffix: Azure Machine Learning
description: 按照此示例使用 Azure 机器学习设计器生成一个无代码分类器来预测收入。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 560339fb04e3bbbe42c4370655e74e8536a7c015
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963355"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>使用 Azure 机器学习设计器生成分类器并使用特征选择来预测收入

**设计器（预览版）示例 3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

了解如何使用设计器（预览版）在不编写代码的情况下生成机器学习分类器。 此示例训练一个“双类提升决策树”来预测成人普查收入（>=50K 或 <=50K）  。

因为此问题回答“哪一个？”， 所以这称为分类问题。 但是，你可以应用相同的基本过程来解决任何类型的机器学习问题，无论是回归、分类还是聚类，等等。

下面是本示例的最终管道图形：

![管道图形](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>必备条件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 单击示例 3 将其打开。



## <a name="data"></a>数据

此数据集包含 14 个特征和一个标签列。 有多种类型的特征，包括数字特征和类别特征。 下图显示了来自数据集的摘录：![数据](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>管道摘要

按照以下步骤创建管道：

1. 将 Adult Census Income Binary 数据集模块拖到管道画布上。
1. 添加“拆分数据”模块来创建训练和测试集  。 将第一个输出数据集中的行部分设置为 0.7。 此设置指定 70% 的数据将输出到模块的左侧端口，其余的输出到右侧端口。 我们使用左侧数据集进行训练，使用右侧数据集进行测试。
1. 添加“基于筛选器的特征选择”模块来按 PearsonCorreclation 选择 5 个特征  。 
1. 添加“双类提升决策树”模块来初始化提升决策树分类器  。
1. 添加“训练模型”模块  。 将上一步骤中的分类器连接到“训练模块”的左侧输入端口  。 将通过“基于筛选器的特征选择”模块筛选的数据集连接为训练数据集。  “训练模型”将训练分类器  。
1. 添加“选择列转换并应用转换”模块来将同一转换（基于筛选器的特征选择）应用于测试数据集。
![apply-transformation](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. 添加“评分模型”模块并将“训练模型”模块连接到它   。 然后，将测试集（也是将特征选择应用于测试集的“应用转换”模块的输出）添加到“评分模型”  。 “评分模型”将进行预测  。 可以选择它的输出端口来查看预测和正类概率。


    此管道有两个评分模块，右侧的模块在进行预测之前已排除标签列。 这是为了部署实时终结点而准备的，因为 Web 服务输入只需要特征不需要标签。 

1. 添加“评估模型”模块并将已评分数据集连接到其左侧输入端口  。 若要查看评估结果，请选择“评估模型”模块的输出端口，并选择“可视化”   。

## <a name="results"></a>结果

![评估结果](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

在评估结果中，可以看到 ROC、精准率-召回率和混淆指标等曲线。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：

- [示例 1 - 回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2 - 回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 4 - 分类：预测信用风险（代价敏感）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 5 - 分类：预测流失率](how-to-designer-sample-classification-churn.md)
- [示例 6 - 分类：预测航班延误](how-to-designer-sample-classification-flight-delay.md)
- [示例 7 - 文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)

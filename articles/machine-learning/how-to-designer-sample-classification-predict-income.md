---
title: 设计器：分类，预测收入示例
titleSuffix: Azure Machine Learning
description: 按照此示例生成一个无代码分类器，使用 Azure 机器学习设计器预测收入。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 227bf490d1ac264bb54ff3d1ecf0ccc2b3ef2e0e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763538"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>生成分类器 & 使用功能选择通过 Azure 机器学习设计器预测收入

**设计器（预览）示例3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

了解如何在不编写代码的情况下使用设计器（预览）生成机器学习分类器。 本示例训练一个**双类提升决策树**，以预测成人人口收益（> = 50K 或 < = 50K）。

因为问题回答 "哪一个？" 这称为分类问题。 但是，您可以应用相同的基本过程来解决任何类型的机器学习问题-回归、分类、群集等。

下面是此示例的最终管道图：

![管道的图形](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>必备组件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 单击示例3打开它。



## <a name="data"></a>数据

数据集包含14个特征和一个标签列。 有多种类型的功能，包括数字和分类。 下图显示了数据集的摘录： ![数据](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>管道摘要

按照以下步骤创建管道：

1. 将 "成人人口收入二进制数据集" 模块拖入管道画布。
1. 添加 "**拆分数据**" 模块以创建定型集和测试集。 将第一个输出数据集中的行的小数部分设置为0.7。 此设置指定将70% 的数据输出到模块的左端口，将其余的数据输出到正确的端口。 我们使用左侧的数据集进行培训，使用正确的数据集进行测试。
1. 添加**基于筛选器的特征选择**模块，按 PearsonCorreclation 选择5个特征。 
1. 添加一个**双类提升决策树**模块来初始化提升决策树分类器。
1. 添加 "**训练模型**" 模块。 将上一步中的分类器连接到**定型模型**的左侧输入端口。 将筛选的数据集从基于筛选器的特征选择模块连接为定型数据集。  **训练模型**将对分类器进行定型。
1. 添加 "选择列" "转换并应用转换" 模块，以将相同的转换（基于筛选的功能选择）应用于测试数据集。
![应用转换](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. 添加 "**评分模型**" 模块并将 "**定型模型**" 模块连接到该模块。 然后将测试集（应用转换模块的输出，它也将功能选择应用于测试集）添加到**评分模型**。 **评分模型**将进行预测。 您可以选择其输出端口来查看预测和正类概率。


    此管道有两个评分模块，在进行预测之前，右侧的模块已排除标签列。 这是为了部署实时终结点做好准备，因为 web 服务输入只需要 "不标记功能"。 

1. 添加 "**评估模型**" 模块并将评分的数据集连接到其左侧输入端口。 若要查看评估结果，请选择 "**评估模型**" 模块的输出端口，并选择 "**可视化**"。

## <a name="results"></a>结果

![评估结果](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

在评估结果中，可以看到类似于 ROC、精度召回和混淆指标的曲线。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：

- [示例 1-回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2-回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 4-分类：预测信用风险（区分成本）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 5-分类：预测改动](how-to-designer-sample-classification-churn.md)
- [示例 6-分类：预测航班延迟](how-to-designer-sample-classification-flight-delay.md)
- [示例 7-文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)

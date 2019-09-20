---
title: '视觉对象接口示例 #3：用于预测信用风险的分类'
titleSuffix: Azure Machine Learning
description: 了解如何生成机器学习分类器，而无需使用可视界面编写单行代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: b9d5308a0b7d9249ea816bafb5c6cb7d9c5e5fd6
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131917"
---
# <a name="sample-3---classification-predict-credit-risk"></a>示例 3-分类：预测信用风险

了解如何生成机器学习分类器，而无需使用可视界面编写单行代码。 本示例训练一个**双类提升决策树**，以根据信用额度、年龄和信用卡数量等信用额度来预测信用风险（高或低）。

因为问题回答 "哪一个？" 这称为分类问题。 但是，您可以应用相同的基本过程来解决任何类型的机器学习问题，无论是回归、分类、群集等。

下面是此示例的最终实验图：

![试验图](media/how-to-ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 对于示例3试验，请选择 "**打开**" 按钮：

    ![打开试验](media/how-to-ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>相关示例

[示例 4-分类：信用风险预测（成本敏感）](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)提供了解决与此试验相同的问题的高级实验。 它演示了如何通过使用**执行 Python 脚本**模块来执行*成本敏感*分类，并比较两种二元分类算法的性能。 如果要了解有关如何生成分类管道的详细信息，请参阅。

## <a name="data"></a>Data

该示例使用 UC Irvine 存储库中的德国信用卡数据集。
数据集包含1000个具有20个功能的样本和1个标签。 每个示例表示一个人。 这些功能包括数字和分类功能。 请参阅[UCI 网站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)，了解分类功能的含义。 最后一列是标签，表示信用风险，只有两个可能的值：高信用风险 = 2 和低信用风险 = 1。

## <a name="experiment-summary"></a>试验摘要

按照以下步骤创建试验：

1. 将德国信用卡 "UCI 数据数据集" 模块拖入试验的画布。
1. 添加 "**编辑元数据**" 模块，以便为每个列添加有意义的名称。
1. 添加 "**拆分数据**" 模块以创建定型集和测试集。 将第一个输出数据集中的行的小数部分设置为0.7。 此设置指定将 70% 的数据输出到模块的左端口，将其余的数据输出到正确的端口。 我们使用左侧的数据集进行培训，使用正确的数据集进行测试。
1. 添加一个**双类提升决策树**模块来初始化提升决策树分类器。
1. 添加 "**训练模型**" 模块。 将上一步中的分类器连接到**定型模型**的左侧输入端口。 将定型集（**拆分数据**的左侧输出端口）添加到**训练模型**的右侧输入端口。 **训练模型**将对分类器进行定型。
1. 添加 "**评分模型**" 模块并将 "**定型模型**" 模块连接到该模块。 然后，将测试集（**拆分数据**的右端端口）添加到**评分模型**。 **评分模型**将进行预测。 您可以选择其输出端口来查看预测和正类概率。
1. 添加 "**评估模型**" 模块并将评分的数据集连接到其左侧输入端口。 若要查看评估结果，请选择 "**评估模型**" 模块的输出端口，并选择 "**可视化**"。

## <a name="results"></a>结果

![评估结果](media/how-to-ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

在评估结果中，可以看到模型的 AUC 为0.776。 阈值为0.5，精度为0.621，召回为0.456，F1 分数为0.526。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于可视界面的其他示例：

- [示例 1-回归：预测汽车的价格](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [示例 2-回归：比较汽车价格预测的算法](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [示例 4-分类：预测信用风险（区分成本）](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [示例 5-分类：预测改动](how-to-ui-sample-classification-predict-churn.md)
- [示例 6-分类：预测航班延迟](how-to-ui-sample-classification-predict-flight-delay.md)

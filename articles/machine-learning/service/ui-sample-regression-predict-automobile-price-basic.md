---
title: '视觉对象接口示例 #1: 回归以预测价格'
titleSuffix: Azure Machine Learning service
description: 了解如何构建机器学习模型来预测汽车的价格, 无需编写单行代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 0c86955e0b10111bf9b6db0d884b73867a4467c5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990408"
---
# <a name="sample-1---regression-predict-price"></a>示例 1-回归:预测价格

了解如何使用可视界面构建机器学习回归模型, 而无需编写单行代码。

此试验培训**决策林回归量**, 根据品牌、型号、动力和大小等技术功能预测汽车的价格。 因为我们正在尝试回答 "多少？" 这称为回归问题。 不过, 您可以在此试验中应用相同的基本步骤来处理任何类型的机器学习问题, 无论是回归、分类、群集等。

训练机器学习模型的基本步骤如下:

1. 获取数据
1. 预先处理数据
1. 训练模型
1. 评估模型

下面是我们将处理的试验的最终完整图形。 我们将提供所有模块的基本原理, 以便您可以自行做出类似的决策。

![试验图](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 对于示例1试验, 请选择 "**打开**" 按钮:

    ![打开试验](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>获取数据

在此实验中, 我们使用来自 UCI 机器学习存储库的 "**汽车价格数据 (原始)** " 数据集。 数据集包含26列, 其中包含有关汽车的信息, 包括品牌、型号、价格、车辆功能 (如柱面数)、MPG 和保险风险评分。 此实验的目的是预测汽车的价格。

## <a name="pre-process-the-data"></a>预先处理数据

主要的数据准备任务包括数据清除、集成、转换、缩减、离散化或量化。 在视觉对象界面中, 可以在左侧面板的 "**数据转换**" 组中找到用于执行这些操作和其他数据预处理任务的模块。

我们使用 "**选择数据集中的列**" 模块排除具有多个缺失值的规范化损失。 然后, 使用 "**清理缺失数据**" 删除包含缺失值的行。 这有助于创建一组清晰的定型数据。

![数据预处理](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>训练模型

机器学习问题有所不同。 常见的机器学习任务包括分类、群集、回归和推荐器系统, 其中每个系统都可能需要不同的算法。 选择的算法通常取决于用例的要求。 选取算法后, 需要优化其参数, 以训练更准确的模型。 然后, 需要基于指标 (如准确性、intelligibility 和效率) 评估所有模型。

由于此试验的目的是预测汽车价格, 并且由于标签列 (价格) 包含实数, 因此回归模型是一个不错的选择。 考虑到功能数量相对较小 (小于 100), 并且这些功能不是稀疏的, 决策边界可能是非线性的。 我们对此试验使用**决策林回归**。

我们使用**拆分数据**模块随机划分输入数据, 以便定型数据集包含 70% 的原始数据, 并且测试数据集包含 30% 的原始数据。

## <a name="test-evaluate-and-compare"></a>测试、评估和比较

 我们拆分数据集并使用不同的数据集来定型和测试模型, 以使模型的评估更具目标。

在对模型进行定型后, 我们使用**评分模型**并**评估模型**模块以生成预测结果并对模型进行评估。

**评分模型**通过使用训练的模型生成测试数据集的预测。 若要检查结果, 请选择 "**评分模型**" 的输出端口, 然后选择 "**可视化**"。

![评分结果](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

然后, 将评分传递到 "**评估模型**" 模块以生成评估指标。 若要检查结果, 请选择 "**评估模型**" 的输出端口, 然后选择 "**可视化**"。

![评估结果](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于可视界面的其他示例:

- [示例 2-回归:比较汽车价格预测的算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [示例 3-分类:预测信用风险](ui-sample-classification-predict-credit-risk-basic.md)
- [示例 4-分类:预测信用风险 (区分成本)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [示例 5-分类:预测改动](ui-sample-classification-predict-churn.md)
- [示例 6-分类:预测航班延迟](ui-sample-classification-predict-flight-delay.md)

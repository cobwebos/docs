---
title: 训练 PyTorch 模型
titleSuffix: Azure Machine Learning
description: 学习如何从头开始训练 pytorch 模型或对其进行优化。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 0ba603dad7d48be725f308f3a3296676c5f4f108
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883229"
---
# <a name="train-pytorch-model"></a>训练 PyTorch 模型

本文介绍如何使用 Azure 机器学习设计器中的 " **训练 Pytorch" 模型** 模块来训练 Pytorch 模型，如 DenseNet。 训练在你定义模型并设置其参数后进行，并且需要带标签的数据。 

## <a name="how-to-use-train-pytorch-model"></a>如何使用“训练 PyTorch 模型” 

1. 在设计器中将 [DenseNet](densenet.md) 模块或 [ResNet](resnet.md) 添加到管道草稿。

2. 将“训练 Pytorch 模型”模块添加到管道。 可以在“模型训练”类别下找到此模块。 展开“训练”，然后将“训练 Pytorch 模型”模块拖到你的管道中 。

   > [!NOTE]
   > 对于大型数据集，“训练 Pytorch 模型”模块最好在“GPU”类型的计算上运行，否则管道会失败。 通过设置“使用其他计算目标”，可以在特定模块的右窗格中为该模块选择计算。

3.  在左侧输入中，附加未训练的模型。 将训练数据集和验证数据集附加到“训练 Pytorch 模型”的中间和右侧输入。

    对于未经训练的模型，它必须是像 DenseNet 那样的 pytorch 模型；否则，将引发“InvalidModelDirectoryError”。

    对于数据集，训练数据集必须是已标记的图像目录。 请参阅“转换为图像目录”以了解如何获取标记的图像目录。 如果未标记，将引发“NotLabeledDatasetError”。

    训练数据集和验证数据集具有相同的标签类别，否则将引发 InvalidDatasetError。

4.  对于“时期”，指定要训练的时期数。 每个时期将循环访问整个数据集，默认循环访问 5 次。

5.  对于“批大小”，指定在一个批次中训练的实例数，默认为 16 个。

6.  对于“学习速率”，请指定一个值作为“学习速率”。 学习速率值控制每次对模型进行测试和纠正时用于优化程序中梯度的大小。

    降低该速率可以加快模型测试频率，但风险是可能会在局部出现拟合效果改进缓慢的情况。 加大梯度可以加快聚合速度，但风险是可能会错过真正的最小值。 默认为 0.001。

7.  对于“随机种子”，可以选择键入一个整数值，将其用作种子。 如果需要跨运行确保试验的可再现性，建议使用种子。

8.  对于“耐性”，请指定在验证损失不连续减少时提前停止训练的时期数。 默认为 3。

9.  提交管道。 如果数据集较大，这将需要一段时间。

## <a name="results"></a>结果

管道运行完成后，若要使用模型进行评分，请将[训练 Pytorch 模型](train-pytorch-model.md)连接到[为图像模型评分](score-image-model.md)，以预测新输入示例的值。

## <a name="technical-notes"></a>技术说明
###  <a name="expected-inputs"></a>预期输入  

| 名称               | 类型                    | 说明                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| 未训练的模型    | UntrainedModelDirectory | 未经训练的模型，需要 pytorch         |
| 训练数据集   | ImageDirectory          | 训练数据集                         |
| 验证数据集 | ImageDirectory          | 每个时期用于评估的验证数据集 |

###  <a name="module-parameters"></a>模块参数  

| 名称          | 范围            | 类型    | 默认 | 说明                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| 时期        | >0               | Integer | 5       | 选择包含标签的列或结果列 |
| 批大小    | >0               | Integer | 16      | 要在一个批次中训练的实例数   |
| 学习速率 | >=double.Epsilon | Float   | 0.001   | “随机梯度下降”优化器的初始学习速率。 |
| 随机种子   | 任意              | Integer | 1       | 模型使用的随机数生成器的种子。 |
| 耐性      | >0               | Integer | 3       | 要提前停止训练的时期数   |

###  <a name="outputs"></a>Outputs  

| 名称          | 类型           | 说明   |
| ------------- | -------------- | ------------- |
| 已训练模型 | ModelDirectory | 已训练模型 |

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 




---
title: 评分模型：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用 "评分模型" 模块，以使用训练的分类或回归模型生成预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: e7ab953a7ac0907244ebaab70b3b86cbe6f0f4d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497642"
---
# <a name="score-model-module"></a>“评分模型”模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块可以使用训练的分类或回归模型生成预测。

## <a name="how-to-use"></a>如何使用

1. 将**评分模型**模块添加到管道。

2. 附加定型模型和包含新输入数据的数据集。 

    数据应采用与所使用的定型模型类型兼容的格式。 输入数据集的架构通常还应与用于为模型定型的数据的架构匹配。

3. 运行管道。

## <a name="results"></a>结果

使用[评分模型](./score-model.md)生成一组评分后：

+ 生成一组用于评估模型准确性（性能）的指标。  可以将评分的数据集连接到[评估模型](./evaluate-model.md)， 
+ 右键单击模块并选择 "**可视化**" 以查看结果的示例。
+ 将结果保存到数据集。

分数或预测值可以采用多种不同的格式，具体取决于模型和输入数据：

- 对于分类模型，[分数模型](./score-model.md)输出类的预测值，以及预测值的概率。
- 对于回归模型，[分数模型](./score-model.md)仅生成预测的数值。
- 对于图像分类模型，分数可能是图像中的对象类，或指示是否找到了特定功能的布尔值。

## <a name="publish-scores-as-a-web-service"></a>将评分发布为 web 服务

评分的一个常见用途是在预测 web 服务中返回输出。 有关详细信息，请参阅本教程，了解如何在 Azure 机器学习中基于管道创建 web 服务：

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 
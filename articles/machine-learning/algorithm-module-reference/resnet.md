---
title: ResNet
titleSuffix: Azure Machine Learning
description: 了解如何使用 ResNet 算法创建图像分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: bd0431a8e503605c6137d948cf207c1bd2fa45b4
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442030"
---
# <a name="resnet"></a>ResNet

本文介绍如何使用 Azure 机器学习设计器中的 **ResNet** 模块，通过 ResNet 算法创建图像分类模型。  

此分类算法是一种监督式学习方法，需要一个标记的数据集。 
> [!NOTE]
> 此模块不支持从 studio 中的 *数据标签* 生成的标记的数据集，但仅支持从 [转换为图像目录](convert-to-image-directory.md) 模块生成的标记为图像目录。 

可通过提供模型和带标记的图像目录作为[训练 Pytorch 模型](train-pytorch-model.md)模块的输入来训练模型。 然后，可使用训练后的模型来预测使用[为图像模型评分](score-image-model.md)的新输入示例的值。

### <a name="more-about-resnet"></a>关于 ResNet 的详细信息

请参阅[本文](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d)，详细了解 ResNet。

## <a name="how-to-configure-resnet"></a>如何配置 ResNet

1.  在设计器中将 ResNet 模块添加到管道****。  

2.  对于“模型名称”，指定特定 ResNet 结构的名称，然后可以从支持的 resnet 中进行选择：“resnet18”、“resnet34”、“resnet50”、“resnet101”、“resnet152”、“resnet152”、“resnext50\_32x4d”、“resnext101\_32x8d”、“wide_resnet50\_2”、“wide_resnet101\_2”****。

3.  对于“预先训练”，指定是否使用在 ImageNet 上预先训练的模型****。 如果已选择，可以根据选定的预训练的模型来微调模型；如果取消选中，可以从头开始训练。

4.  将 **DenseNet** 模块、定型和验证图像数据集模块的输出连接到 [定型 Pytorch 模型](train-pytorch-model.md)。 

5. 提交管道。

## <a name="results"></a>结果

管道运行完成后，若要使用模型进行评分，请将[训练 Pytorch 模型](train-pytorch-model.md)连接到[为图像模型评分](score-image-model.md)，以预测新输入示例的值。

## <a name="technical-notes"></a>技术说明  

###  <a name="module-parameters"></a>模块参数  

| 名称       | 范围 | 类型    | 默认           | 说明                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| 模型名称 | 任意   | Mode    | resnext101\_32x8d | 特定 ResNet 结构的名称       |
| 经过预先训练 | 任意   | 布尔 | True              | 是否使用在 ImageNet 上预先训练的模型 |
|            |       |         |                   |                                          |

###  <a name="output"></a>输出  

| 名称            | 类型                    | 说明                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 未训练的模型 | UntrainedModelDirectory | 可连接到训练 Pytorch 模型的未训练的 ResNet 模型。 |

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
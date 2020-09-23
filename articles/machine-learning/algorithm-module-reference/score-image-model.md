---
title: 使用 "分数图像模型" 模块
titleSuffix: Azure Machine Learning
description: 了解如何通过已定型图像模型使用 Azure 机器学习中的“为图像模型评分”模块生成预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 88ca997e2d22283babf582b10d9b0eeb7de122c0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905183"
---
# <a name="score-image-model"></a>为图像模型评分

本文介绍 Azure 机器学习设计器中的模块。

对输入图像数据使用此模块可以通过已定型图像模型生成预测。

## <a name="how-to-configure-score-image-model"></a>如何配置“为图像模型评分”模块

1. 将“为图像模型评分”模块添加到管道。

2. 连接已定型图像模型和包含输入图像数据的数据集。 

    数据应为 ImageDirectory 类型。 若要详细了解如何获取图像目录，请参阅[转换为图像目录](convert-to-image-directory.md)模块。 输入数据集的架构通常还应与用于训练模型的数据的架构相匹配。

3. 提交管道。

## <a name="results"></a>结果

使用[为图像模型评分](score-image-model.md)模块生成一组分数后，若要生成一组用于评估模型准确性（性能）的指标，可以将此模块和评分数据集连接到[评估模型](evaluate-model.md)。 

### <a name="publish-scores-as-a-web-service"></a>将评分发布为 Web 服务

评分的一个常见用途是在预测 Web 服务中返回输出。 有关详细信息，请参阅[此教程](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy)，了解如何在 Azure 机器学习设计器中基于管道部署实时终结点。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 

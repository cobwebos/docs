---
title: 拆分图像目录
titleSuffix: Azure Machine Learning
description: 了解如何通过已定型图像模型使用 Azure 机器学习中的“为图像模型评分”模块生成预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 9673b3260425fd9244c635beaf77d367a14cac54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809914"
---
# <a name="split-image-directory"></a>拆分图像目录

本主题介绍如何使用 Azure 机器学习设计器（预览版）中的“拆分图像目录”模块将图像目录的图像划分为两个不同的集。

当需要将图像数据划分为训练集和测试集时，此模块特别有用。 

## <a name="how-to-configure-split-image-directory"></a>如何配置“拆分图像目录”模块

1. 将“拆分图像目录”模块添加到管道。 可以在“计算机视觉/图像数据转换”类别下找到此模块。

2. 将它连接到输出为其图像目录的模块。

3. 输入**第一个输出中的图像的比例**，以指定要放入左分割区的数据百分比（默认为 0.9）。


## <a name="technical-notes"></a>技术说明

### <a name="expected-inputs"></a>预期输入

| 名称                  | 类型           | 描述              |
| --------------------- | -------------- | ------------------------ |
| 输入图像目录 | ImageDirectory | 要拆分的图像目录 |

### <a name="module-parameters"></a>模块参数

| 名称                                   | 类型  | 范围 | 可选 | 描述                            | 默认 |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| 第一个输出中的图像比例 | Float | 0-1   | 必须 | 第一个输出中的图像比例 | 0.9     |

### <a name="outputs"></a>Outputs

| 名称                    | 类型           | 描述                              |
| ----------------------- | -------------- | ---------------------------------------- |
| 输出图像目录 1 | ImageDirectory | 包含所选图像的图像目录 |
| 输出图像目录 2 | ImageDirectory | 包含所有其他图像的图像目录 |

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 


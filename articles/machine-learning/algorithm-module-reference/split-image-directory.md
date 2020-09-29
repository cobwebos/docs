---
title: 拆分图像目录
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器中的拆分图像目录模块 (预览版) ，将图像目录的图像划分为两个不同的集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: fe5ba25904298fe1a394a4b01d6bdacc72d599c9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448689"
---
# <a name="split-image-directory"></a>拆分图像目录

本主题介绍如何使用 Azure 机器学习设计器中的 "拆分映像目录" 模块将图像目录的图像划分为两个不同的集。

当需要将图像数据划分为训练集和测试集时，此模块特别有用。 

## <a name="how-to-configure-split-image-directory"></a>如何配置“拆分图像目录”模块

1. 将“拆分图像目录”模块添加到管道。 可以在“计算机视觉/图像数据转换”类别下找到此模块。

2. 将它连接到输出为其图像目录的模块。

3. 输入**第一个输出中的图像的比例**，以指定要放入左分割区的数据百分比（默认为 0.9）。 如果该分数结果不是整数，则该模块将使用较小的 near 整数。


## <a name="technical-notes"></a>技术说明

### <a name="expected-inputs"></a>预期输入

| 名称                  | 类型           | 说明              |
| --------------------- | -------------- | ------------------------ |
| 输入图像目录 | ImageDirectory | 要拆分的图像目录 |

### <a name="module-parameters"></a>模块参数

| 名称                                   | 类型  | 范围 | 可选 | 说明                            | 默认 |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| 第一个输出中的图像比例 | Float | 0-1   | 必须 | 第一个输出中的图像比例 | 0.9     |

### <a name="outputs"></a>Outputs

| 名称                    | 类型           | 说明                              |
| ----------------------- | -------------- | ---------------------------------------- |
| 输出图像目录 1 | ImageDirectory | 包含所选图像的图像目录 |
| 输出图像目录 2 | ImageDirectory | 包含所有其他图像的图像目录 |

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 


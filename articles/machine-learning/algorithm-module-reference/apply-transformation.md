---
title: 应用转换：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "应用转换" 模块来修改基于之前计算的转换的输入数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a48ce60dca9f4221e364d53567f5b53719deb18c
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314515"
---
# <a name="apply-transformation-module"></a>“应用转换”模块

本文介绍 Azure 机器学习设计器中的模块。

使用此模块可以基于之前计算的转换来修改输入数据集。  
  
例如，如果您使用 z 分数通过**规范化数据**模块来规范化定型数据，则您需要使用在评分阶段为定型计算的 z 分数值。 在 Azure 机器学习中，可以将规范化方法保存为转换，然后使用 "**应用转换**" 将 z 分数应用到输入数据，然后评分。
  
Azure 机器学习为创建和应用多种不同类型的自定义转换提供支持。 例如，你可能想要保存并重复使用转换到：  
  
- 使用 "**清理缺失数据**" 删除或替换缺失值
- 规范化数据，使用**规范化数据**
  

## <a name="how-to-use-apply-transformation"></a>如何使用应用转换  
  
1. 向管道添加 "**应用转换**" 模块。 可以在 "**评分**" 类别中的 "**机器学习**" 下找到此模块。 
  
2. 找到要用作输入的现有转换。  以前保存的转换可在左侧导航窗格中的 "**转换**" 组中找到。  
  
   
  
3. 连接要转换的数据集。 数据集应具有与首次设计转换的数据集相同的架构（列数、列名和数据类型）。  
  
4. 不需要设置其他参数，因为在定义转换时完成所有自定义。  
  
5. 若要将转换应用于新数据集，请运行管道。  

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 
---
title: 应用转换：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "应用转换" 模块来修改基于之前计算的转换的输入数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395264"
---
# <a name="apply-transformation-module"></a>“应用转换”模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块可以基于之前计算的转换来修改输入数据集。

例如，如果您使用 z 分数通过**规范化数据**模块来规范化定型数据，则您需要使用在评分阶段为定型计算的 z 分数值。 在 Azure 机器学习中，可以将规范化方法保存为转换，然后使用 "**应用转换**" 将 z 分数应用到输入数据，然后评分。

## <a name="how-to-save-transformations"></a>如何保存转换

设计器使你能够将数据转换保存为数据**集**，以便在其他管道中使用它们。

1. 选择已成功运行的数据转换模块。

1. 选择 "**输出 + 日志**" 选项卡。

1. 选择 "**保存" 图标**以保存**结果转换**。

## <a name="how-to-use-apply-transformation"></a>如何使用应用转换  
  
1. 向管道添加 "**应用转换**" 模块。 您可以在模块调色板的 "**模型评分 & 评估**" 部分中找到此模块。 
  
1. 在模块调色板中，在 "**数据集** **" > "** 数据集" 下查找要使用的已保存转换。

1. 将保存的转换的输出连接到 "**应用转换**" 模块的左侧输入端口。

    数据集应具有与首次设计转换的数据集相同的架构（列数、列名和数据类型）。  
  
1. 将所需模块的数据集输出连接到 "**应用转换**" 模块的右侧输入端口。
  
1. 若要将转换应用于新数据集，请运行管道。  

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 
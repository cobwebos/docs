---
title: 应用转换：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“应用转换”模块来修改基于之前计算的转换的输入数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: e2b4233f8f59a26e7da532fca48aecbb41857b66
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84488624"
---
# <a name="apply-transformation-module"></a>“应用转换”模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块来修改基于之前计算的转换的输入数据集。

例如，如果使用了 z 分数通过“规范化数据”模块来规范化训练数据，则可能还需要使用在评分阶段为训练计算的 z 分数值。**** 在 Azure 机器学习中，可以将规范化方法另存为转换，然后使用“应用转换”在评分之前将 z 分数应用于输入数据。****

## <a name="how-to-save-transformations"></a>如何保存转换

设计器允许你将数据转换保存为“数据集”****，以便在其他管道中使用它们。

1. 选择已成功运行的数据转换模块。

1. 选择“输出 + 日志”**** 选项卡。

1. 找到转换输出，然后选择“注册数据集”，将其另存为“模块”面板中“数据集”类别下的模块**** ****。

## <a name="how-to-use-apply-transformation"></a>如何使用“应用转换”  
  
1. 将 **应用转换**模块添加到管道。 可以在“模块”面板的“模型评分和评估”**** 部分中找到此模块。 
  
1. 在“模块”面板的“数据集”下找到要使用的已保存转换****。

1. 将已保存转换的输出连接到“应用转换”**** 模块的左侧输入端口。

    数据集应具有与首次为其设计了转换的数据集相同的架构（列数、列名、数据类型）。  
  
1. 将所需模块的数据集输出连接到“应用转换”**** 模块的右侧输入端口。
  
1. 若要将转换应用于新数据集，请运行管道。  

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
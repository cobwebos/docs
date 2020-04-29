---
title: 添加行：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“添加行”模块连接两个数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477725"
---
# <a name="add-rows-module"></a>“添加行”模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块连接两个数据集。 在串联中，第二个数据集的行会添加到第一个数据集的末尾。  
  
行的串联在以下方案中很有用：  
  
+ 你已生成一系列评估统计信息，希望将它们合并到一个表中以方便报告。  
  
+ 你使用了不同的数据集，想要合并这些数据集以创建最终的数据集。  

## <a name="how-to-use-add-rows"></a>如何使用“添加行”  

若要连接两个数据集中的行，这些行必须具有完全相同的架构。 这意味着列的列数和列中的数据类型相同。

1.  将 "**添加行**" 模块拖放到管道中，可以在 "**数据转换**" 下找到它。

2. 将数据集连接到两个输入端口。 要追加的数据集应连接到第二个（右）端口。 
  
3.  提交管道。 输出数据集中的行数应等于两个输入数据集的行数之和。

    如果将同一数据集添加到“添加行”模块的两个输入，则会复制数据集。  

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
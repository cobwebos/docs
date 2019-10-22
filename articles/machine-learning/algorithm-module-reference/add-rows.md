---
title: 添加行：模块引用
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "添加行" 模块连接两个数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: c8d8b6a873ee17d1658b0cb47de830848b215b89
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693246"
---
# <a name="add-rows-module"></a>添加行模块

本文介绍了 Azure 机器学习服务的可视界面（预览）的模块。

使用此模块连接两个数据集。 在串联中，将第二个数据集的行添加到第一个数据集的末尾。  
  
串联行在以下方案中很有用：  
  
+ 您已生成一系列评估统计信息，并且您希望将它们合并到一个表中以方便报告。  
  
+ 你使用了不同的数据集，并且想要合并这些数据集以创建最终的数据集。  

## <a name="how-to-use-add-rows"></a>如何使用添加行  

若要连接两个数据集中的行，这些行必须具有完全相同的架构。 这意味着列的列数和列中的数据类型相同。

1.  将 "**添加行**" 模块拖放到管道中，可以在 "**操作**" 类别中的 "**数据转换**" 下找到它。

2. 将数据集连接到两个输入端口。 要追加的数据集应连接到第二个（右）端口。 
  
3.  运行管道。 输出数据集中的行数应该等于两个输入数据集的行的总和。

    如果将同一数据集添加到 "**添加行**" 模块的两个输入，则会复制数据集。 

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
---
title: 汇总数据
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "汇总数据" 模块为数据集中的列生成基本描述性统计信息报告。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: a037c959b44a6216724c285f932250b1b05cb509
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313735"
---
# <a name="summarize-data"></a>汇总数据

本文介绍 Azure 机器学习设计器的模块。

使用 "汇总数据" 模块可创建一组标准统计度量值，这些度量值用于描述输入表中的每一列。

当您想要了解完整数据集的特征时，汇总统计信息非常有用。 例如，你可能需要了解以下内容：

- 每个列中缺少多少个值？
- 功能列中有多少个唯一值？
- 每个列的平均值和标准偏差是怎样的？

该模块计算每列的重要分数，并为作为输入提供的每个变量（数据列）返回一行汇总统计信息。

## <a name="how-to-configure-summarize-data"></a>如何配置汇总数据  

1. 将 "**汇总数据**" 模块添加到管道。 您可以在设计器的 "**统计函数**" 类别中找到此模块。

1. 连接要为其生成报表的数据集。

    如果只想要报告某些列，请使用 "[选择数据集中的列](select-columns-in-dataset.md)" 模块投影要使用的列的子集。

1. 不需要其他参数。 默认情况下，该模块将分析作为输入提供的所有列，根据列中的值的类型，输出一组相关的统计信息，如 "[结果](#results)" 一节中所述。

1. 运行管道。

## <a name="results"></a>结果

模块中的报表可以包含以下统计信息。 

|列名称|Description|
|------|------|  
|**功能**|列的名称|
|**Count**|统计所有行|
|**唯一值计数**|列中的唯一值数|
|**缺少值计数**|列中的唯一值数|
|**Min**|列中的最小值|  
|**Max**|列中的最大值|
|**中间线**|所有列值的平均值|
|**平均偏差**|列值的平均偏差|
|**第一四分四**|第四个级别的值|
|**Median**|中间列值|
|**第三个分位**|第三个四分|
|**模式**|列值的模式|
|**范围**|表示最大值和最小值之间的值数的整数|
|**样本方差**|列的方差;请参阅说明|
|**样本标准偏差**|列的标准偏差;请参阅说明|
|**样本偏斜度**|列偏斜度;请参阅说明|
|**示例峰值**|列峰值;请参阅说明|
|**P 0。5**|0.5% 百评分|
|**P1**|1% 百分点|
|**P5**|5% 百分点|
|**P95**|95% 百分点|
|**P 99。5**|99.5% 百分点 |

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。  

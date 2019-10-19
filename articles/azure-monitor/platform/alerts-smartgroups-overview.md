---
title: 智能组
description: 智能组是警报的聚合，可帮助你降低警报噪音
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 05/15/2018
ms.openlocfilehash: 83ea68ad92a6c78ccf56483e1f0c2cbcbcd7d22a
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552352"
---
# <a name="smart-groups"></a>智能组
处理警报时面临的一个常见难题是筛选噪音以找出真正重要的事项 - 智能组旨在解决该问题。  

智能组是使用机器学习算法自动创建的，用于将表示单个问题的相关警报组合在一起。  创建某个警报时，算法会根据历史模式、相似属性和相似结构等信息，将其添加到新智能组或现有智能组。 例如，如果某个订阅中多个虚拟机上的 % CPU 同时达到峰值导致生成许多单独的警报，并且如果此类警报在过去的任何时间一起发生，则这些警报可能会被分组到一个智能组中，从而表明存在潜在的共同根本原因。 这意味着，对于对警报进行故障排除的人员来说，智能组不仅可以通过将相关警报作为单个聚合单元进行管理来降低噪音，还可以引导他们找到其警报的可能共同根本原因。

目前，算法只考虑来自订阅中同一监视服务的警报。 通过这种合并，智能组最大可将警报干扰降低 99%。 可以在智能组详细信息页中查看将警报包含在该组中的原因。

可以查看智能组的详细信息，并像设置警报状态一样设置智能组的状态。 每个警报是一个（且仅限一个）智能组的成员。 

## <a name="smart-group-state"></a>智能组状态
智能组状态与警报状态的概念类似，允许你在智能组级别管理解决过程。 与警报状态类似，创建智能组时，它具有“新建”状态，可以将其更改为“已确认”或“已关闭”。

支持以下智能组状态。

| 状况 | 描述 |
|:---|:---|
| 新 | 只是检测到了问题，但尚未审查问题。 |
| 已确认 | 管理员已审查智能组，并已开始进行处理。 |
| 已关闭 | 问题已解决。 关闭某个智能组后，可通过将其更改为另一种状态来重新打开它。 |

[了解如何更改智能组的状态。](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  更改智能组状态不会更改单个成员警报状态。

## <a name="smart-group-details-page"></a>“智能组详细信息”页

选择某个智能组时，会显示“智能组详细信息”页。 该页提供智能组的详细信息（包括创建该组的原因），并可在其中更改智能组的状态。
 
![智能组详细信息](media/alerts-smartgroups-overview/smart-group-detail.png)


“智能组详细信息”页包括以下部分。

| 部分 | 描述 |
|:---|:---|
| 警报 | 列出智能组中包含的各个警报。 选择某个警报会打开它的“警报详细信息”页。 |
| 历史记录 | 列出智能组执行的每个操作，以及对智能组进行的任何更改。 目前仅限状态更改和警报成员身份更改。 |

## <a name="smart-group-taxonomy"></a>智能组分类

智能组的名称是其第一个警报的名称。 无法创建或重命名智能组。

## <a name="next-steps"></a>后续步骤

- [管理智能组](https://aka.ms/managing-smart-groups)
- [更改警报和智能组状态](https://aka.ms/managing-alert-smart-group-states)



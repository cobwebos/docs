---
title: include 文件
description: include 文件
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 06/10/2019
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: c5fedc59c80c68fc222693a67664ef60ddd210a9
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293453"
---
以下限制适用于当前基于消费的定价层中引入了在 2018 年 4 月中每个 Log Analytics 工作区：

|     | 每 GB 2018 |
| --- | --- | 
| 每天收集的数据量 | 无 |
| 数据保留期 | 30 到 730 天<sup>1</sup> |

以下限制可适用于每个 Log Analytics 工作区定价层的最新旧：

|  | 免费 | 独立 （按 gb 计） | 每个节点 (OMS) |
| --- | --- | --- | --- | --- | --- |--- |
| 每天收集的数据量 |500 MB<sup>2</sup> |无 |无 |
| 数据保留期 |7 天 | 30 到 730 天<sup>1</sup> | 30 到 730 天<sup>1</sup> |

以下限制可适用于每个 Log Analytics 工作区的最早的旧的定价层：

|  | 标准 | 高级 | 
| --- | --- | --- | --- | --- | --- |--- |
| 每天收集的数据量 | 无 | 无 | 
| 数据保留期 |30 天 | 365 天 |

<sup>1</sup>数据保留 31 天以上是可用的附加费用。 详细了解 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

<sup>2</sup>时你的工作区达到每日数据传输限制时 500 MB，数据分析将停止，并在恢复的下一天开始。 日期基于 UTC。

>[!NOTE]
>具体取决于多长时间，已使用 Log Analytics，你可能有权旧的定价层。 详细了解如何[定价层的 Log Analytics 旧版](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)。 
>

以下限制适用于每个订阅的 Azure Log Analytics 资源 （工作区）。

| 定价层    | 每个订阅的工作区数 | 注释
| --- | --- | --- |
| 免费层  | 10 | 不能增加此限制。 |
| 所有免费层以外层 | 不适用 | 限制是资源组中的资源数和每个订阅的资源组的数目。 | 

以下限制适用于 Log Analytics Api:

| 类别 | 限制 | 注释
| --- | --- | --- |
| 数据收集器 API | 单个发布的最大大小为 30 MB。<br>字段值的最大大小为 32 KB。 | 将较大的卷拆分为多个帖子。<br>超过 32 KB 的字段会被截断。 |
| 搜索 API | 对于非聚合数据返回 5000 条记录。<br>聚合数据的 500,000 条记录。 | 聚合的数据是一种搜索包含`summarize`命令。
 

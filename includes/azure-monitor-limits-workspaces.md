---
title: include 文件
description: include 文件
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: dfcf47a95d1dbff34ff322768fc4ac6c9674cff4
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295947"
---
**数据收集量和保留期** 

| 层 | 每日限制 | 数据保留 | 注释 |
|:---|:---|:---|:---|
| 当前每个 GB 定价层<br>（已引入 2018 年 4 月） | 无限制 | 30-730 天 | 数据保留 31 天以上仅适用于额外的费用。 了解有关 Azure Monitor 定价的详细信息。 |
| 旧版免费层<br>（已引入 2016 年 4 月） | 500 MB | 7 天 | 当你的工作区达到每日数据传输限制时 500 MB，数据分析会停止，并在恢复下一天的开始。 日期基于 UTC。 |
| 旧的独立按 GB 层<br>（已引入 2016 年 4 月） | 无限制 | 30 到 730 天 | 数据保留 31 天以上仅适用于额外的费用。 了解有关 Azure Monitor 定价的详细信息。 |
| 每个节点 (OMS) 的旧<br>（已引入 2016 年 4 月） | 无限制 | 30 到 730 天 | 数据保留 31 天以上仅适用于额外的费用。 了解有关 Azure Monitor 定价的详细信息。 |
| 旧的标准层 | 无限制 | 30 天  | 不能调整保留期 |
| 旧的高级层 | 无限制 | 365 天  | 不能调整保留期 |

**每个订阅的工作区数。**

| 定价层    | 工作区限制 | 注释
|:---|:---|:---|
| 免费层  | 10 | 不能增加此限制。 |
| 所有其他层 | 无限制 | 限制是资源组中的资源数和每个订阅的资源组的数目。 |

**Azure 门户**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 日志查询返回的最大记录数 | 10,000 | 减少返回的结果在查询中使用查询作用域、 时间范围和筛选器。 |


**数据收集器 API**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 单个发布的最大大小 | 30 MB | 将较大的卷拆分为多个帖子。 |
| 字段值的最大大小  | 32 KB | 超过 32 KB 的字段会被截断。 |

**搜索 API**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 非聚合数据返回的最大记录 | 5,000 | |
| 聚合数据的最大记录数 | 500,000 | 聚合的数据是一种搜索包含`summarize`命令。 |
| 返回的数据的最大大小 | 64,000,000 字节 (~ 61 MiB)| |
| 最大查询运行时间 | 10 分钟 | 请参阅[超时](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)有关详细信息。  |
| 最大请求速率 | 每 30 秒，每个 AAD 用户或客户端 IP 地址的 200 个请求 | 请参阅[速率限制](https://dev.loganalytics.io/documentation/Using-the-API/Limits)有关详细信息。 |

**常规工作区限制**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 表中的最大列         | 500 | |
| 对于列名称的最大字符数 | 500 | |
| 最大程度上的区域 | 美国中西部 | 当前由于已达到临时容量限制，不能在此区域中创建新的工作区。 此限制已计划由 2019 年 9 月日结束。 |
| 数据导出 | 当前不可用 | 使用 Azure 函数或逻辑应用来聚合和导出数据。 | 

>[!NOTE]
>具体取决于多长时间，已使用 Log Analytics，你可能有权旧的定价层。 详细了解如何[定价层的 Log Analytics 旧版](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)。 
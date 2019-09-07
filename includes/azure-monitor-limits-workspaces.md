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
ms.openlocfilehash: 5d0c43fbcc1c59c3281f412aad96a3942a5c79b1
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70392903"
---
**数据收集量和保留期** 

| 层 | 每日限制 | 数据保留 | 注释 |
|:---|:---|:---|:---|
| 当前每 GB 定价层<br>（已于 2018 年 4 月推出） | 无限制 | 30 - 730 天 | 如果数据保留期超过 31 天，则需要收取额外的费用。 详细了解 Azure Monitor 定价。 |
| 旧的免费层<br>（已于 2016 年 4 月推出） | 500 MB | 7 天 | 当工作区达到 500 MB 的每日限制时，数据引入会停止，并在第二天开始时恢复。 日期基于 UTC。 请注意，由 Azure 安全中心收集的数据不包含在这一 500 MB 的每日限制内，并在超出此限制后继续进行收集。  |
| 旧的独立（按 GB）层<br>（已于 2016 年 4 月推出） | 无限制 | 30 至 730 天 | 如果数据保留期超过 31 天，则需要收取额外的费用。 详细了解 Azure Monitor 定价。 |
| 旧的按节点 (OMS) 定价层<br>（已于 2016 年 4 月推出） | 无限制 | 30 至 730 天 | 如果数据保留期超过 31 天，则需要收取额外的费用。 详细了解 Azure Monitor 定价。 |
| 旧的标准层 | 无限制 | 30 天  | 不能调整保留期 |
| 旧的高级层 | 无限制 | 365 天  | 不能调整保留期 |

**每个订阅的工作区数。**

| 定价层    | 工作区限制 | 注释
|:---|:---|:---|
| 免费层  | 10 | 此限制不能提高。 |
| 其他所有层 | 无限制 | 你会受到资源组中的资源数以及每个订阅的资源组数的限制。 |

**Azure 门户**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 日志查询返回的最大记录数 | 10,000 | 在查询中使用查询作用域、时间范围和筛选器来减少结果。 |


**数据收集器 API**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 单个发布内容的最大大小 | 30 MB | 将较大的卷拆分为多个发布内容。 |
| 字段值的最大大小  | 32 KB | 超过 32 KB 的字段会被截断。 |

**搜索 API**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 单个查询中返回的最大记录数 | 500,000 | |
| 返回的数据的最大大小 | 64,000,000 字节 (~61 MiB)| |
| 最长查询运行时间 | 10 分钟 | 有关详细信息，请参阅[超时](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)。  |
| 最大请求速率 | 每个 AAD 用户或客户端 IP 地址每 30 秒发出 200 个请求 | 有关详细信息，请参阅[速率限制](https://dev.loganalytics.io/documentation/Using-the-API/Limits)。 |

**常规工作区限制**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 表中的最大列数         | 500 | |
| 列名称的最大字符数 | 500 | |
| 满负荷的区域 | 美国中西部 | 目前，不能在这个区域中新建工作区，因为该区域已达到临时容量限制。 此限制计划为2019年10月结束。 |
| 数据导出 | 当前不可用 | 使用 Azure 函数或逻辑应用聚合和导出数据。 | 

>[!NOTE]
>根据 Log Analytics 的使用时长，你可能有权使用旧的定价层。 详细了解 [Log Analytics 的旧定价层](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)。 
---
title: include 文件
description: include 文件
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553326"
---
以下限制适用于每个订阅的 Azure Log Analytics 资源。

| 资源 | 默认限制 | 注释
| --- | --- | --- |
| 每个订阅的免费工作区数目 | 10 | 不能增加此限制。 |
| 每个订阅的付费工作区数目 | 不适用 | 限制是资源组中的资源数和每个订阅的资源组的数目。 | 

>[!NOTE]
>截至 2018 年 4 月 2 日，新的订阅中的新工作区自动使用*每个 GB*定价计划。 对于在 4 月 2 日之前创建的现有订阅或绑定到现有企业协议注册的订阅，可以继续从新的工作区的三个定价层中进行选择。 
>

以下限制适用于每个 Log Analytics 工作区。

|  | 免费 | 标准 | 高级 | 独立 | OMS | 每 GB |
| --- | --- | --- | --- | --- | --- |--- |
| 每天收集的数据量 |500 MB<sup>1</sup> |无 |无 | 无 | 无 | 无
| 数据保留期 |7 天 |1 个月 |12 个月 | 1 个月<sup>2</sup> | 1 个月<sup>2</sup>| 1 个月<sup>2</sup>|

<sup>1</sup>当客户达到其 500 MB 每日数据传输限制、 数据分析将停止，并在恢复第二天的开始。 日期基于 UTC。

<sup>2</sup>独立、 OMS 和定价计划的每个 GB 的数据保留期可以增加到 730 天。

| 类别 | 限制 | 注释
| --- | --- | --- |
| 数据收集器 API | 单个发布的最大大小为 30 MB。<br>字段值的最大大小为 32 KB。 | 将较大的卷拆分为多个帖子。<br>超过 32 KB 的字段会被截断。 |
| 搜索 API | 对于非聚合数据返回 5000 条记录。<br>聚合数据的 500,000 条记录。 | 聚合的数据是一种搜索包含`summarize`命令。
 

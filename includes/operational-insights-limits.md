---
title: include 文件
description: include 文件
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 03/29/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 29256b3bcfedb7fe5045ff4c6c3842eb25e00a28
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
下列限制适用于每个订阅的 Log Analytics 资源：

| 资源 | 默认限制 | 注释
| --- | --- | --- |
| 每个订阅的免费工作区数目 | 10 | 无法提高此限制。 |
| 每个订阅的付费工作区数目 | 不适用 | 受到的限制包括一个资源组中的资源数，以及一个订阅的资源组数 | 

>[!NOTE]
>截止 2018 年 4 月 2 日，新订阅中的新工作区将自动使用“每 GB”定价计划。  对于在 4 月 2 日之前创建的现有订阅，或者是已绑定到现有 EA 注册的订阅，对于新工作区，可以继续在三个定价层之间进行选择。 
>

以下限制适用于每个 Log Analytics 工作区：

|  | 免费 | 标准 | 高级 | 独立 | OMS | 每 GB |
| --- | --- | --- | --- | --- | --- |--- |
| 每天收集的数据量 |500 MB<sup>1</sup> |无 |无 | 无 | 无 | 无
| 数据保留期 |7 天 |1 个月 |12 个月 | 1 个月<sup>2</sup> | 1 个月<sup>2</sup>| 1 个月<sup>2</sup>|

<sup>1</sup>当客户达到其 500 MB 的每日数据传输限制时，数据分析会停止，并在下一日开始时恢复。 日期基于 UTC。

<sup>2</sup>“独立”、“OMS”和“每 GB”定价计划的数据保留期可以增加到 730 天。

| 类别 | 限制 | 注释
| --- | --- | --- |
| 数据收集器 API | 单个发布内容的最大大小为 30 MB<br>字段值的最大大小为 32 KB | 将较大的卷拆分为多个发布内容<br>超过 32 KB 的字段会被截断。 |
| 搜索 API | 针对非聚合数据返回 5000 条记录<br>针对聚合数据返回 500000 条记录 | 聚合数据是指包含 `measure` 命令的搜索
 

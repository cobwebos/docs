---
title: include 文件
description: include 文件
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: abe7668ed16ddd5cc1b247207d3e80f7c1118137
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50964522"
---
| 资源 | 最大限制 |说明|
| --- | --- |---|
| 每个自动化帐户每 30 秒可以提交的新作业的最大数量（非计划的作业） |100 |达到此限制时，后续作业创建请求会失败。 客户端会收到错误响应。|
| 每个自动化帐户相同时间实例并发运行的作业的最大数量（非计划的作业） |200 |达到此限制时，后续作业创建请求会失败。 客户端会收到错误响应。|
| 每个自动化帐户每 30 秒可以导入的模块的最大数量 |5 ||
| 一个模块的最大大小 |100 MB ||
| 作业运行时间 - 免费层 |每个订阅每个日历月 500 分钟 ||
| 每个沙盒允许的最大磁盘空间 **<sup>1</sup>** |1 GB |仅适用于 Azure 沙盒|
| 给定到沙盒的最大内存量 **<sup>1</sup>** |400 MB |仅适用于 Azure 沙盒|
| 每个沙盒允许的最大网络套接字数量 **<sup>1</sup>** |1000 |仅适用于 Azure 沙盒|
| 每个 runbook 允许的最大运行时 **<sup>1</sup>** |3 小时 |仅适用于 Azure 沙盒|
| 订阅中自动化帐户的最大数目 |无限制 ||
|单个混合 Runbook 辅助角色上可运行的最大并发作业数|50 ||
| Runbook 作业参数大小上限   | 512 kb||
| Runbook 参数数量上限   | 50|可以将 JSON 或 XML 字符串传递给参数，并在达到 50 个参数的限制时使用 Runbook 对其进行分析|
| Webhook 有效负载大小上限 |  512 kb|
| 保留作业数据的最大天数|30 天|

<sup>1</sup> 沙盒是可由多个作业使用的共享环境，使用相同沙盒的作业受到该沙盒的资源限制的约束。

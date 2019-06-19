---
title: include 文件
description: include 文件
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 2823a33b25812a69ad463433bacd9710655c9176
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238689"
---
#### <a name="process-automation"></a>流程自动化

| Resource | 最大限制 |说明|
| --- | --- |---|
| 每个 Azure 自动化帐户每 30 秒可以提交的新作业的最大数量（非计划的作业） |100 |达到此限制时，后续作业创建请求会失败。 客户端会收到错误响应。|
| 每个自动化帐户相同时间实例并发运行的作业的最大数量（非计划的作业） |200 |达到此限制时，后续作业创建请求会失败。 客户端会收到错误响应。|
| 30 天滚动期内作业元数据的最大存储大小 | 10 GB（约 400 万个作业）|达到此限制时，后续作业创建请求会失败。 |
| 最大作业流限制|1MB|单个流不能大于 1 MB。|
| 每个自动化帐户每 30 秒可以导入的模块的最大数量 |5 ||
| 模块的最大大小 |100 MB ||
| 作业运行时间，免费层 |每个订阅每个日历月 500 分钟 ||
| 每个沙盒允许的最大磁盘空间<sup>1</sup> |1 GB |仅适用于 Azure 沙盒。|
| 沙盒的最大内存量<sup>1</sup> |400 MB |仅适用于 Azure 沙盒。|
| 每个沙盒允许的最大网络套接字数量<sup>1</sup> |1,000 |仅适用于 Azure 沙盒。|
| 每个 runbook 允许的最大运行时<sup>1</sup> |3 小时 |仅适用于 Azure 沙盒。|
| 订阅中自动化帐户的最大数目 |无限制 ||
| 最大数量的每个自动化帐户的混合辅助角色组|4,000||
|可以在单个的混合 Runbook 辅助角色运行的并发作业的最大数目|50 ||
| Runbook 作业参数大小上限   | 512 千比特||
| Runbook 参数数量上限   | 50|如果达到 50 个参数的限制，则可将 JSON 或 XML 字符串传递给参数，并使用 Runbook 对其进行分析。|
| Webhook 有效负载大小上限 |  512 千比特|
| 保留作业数据的最大天数|30 天|
| PowerShell 工作流状态大小上限 |5 MB| 执行检查点工作流时适用于 PowerShell 工作流 runbook。|

<sup>1</sup>沙盒是可以由多个作业使用的共享环境。 使用同一沙盒的作业受沙盒的资源限制约束。

#### <a name="change-tracking-and-inventory"></a>更改跟踪和库存

下表显示了每台计算机进行更改跟踪的跟踪的项限制。

| **资源** | **限制**| **说明** |
|---|---|---|
|文件|500||
|注册表|250||
|Windows 软件|250|不包括软件更新。|
|Linux 包|1,250||
|服务|250||
|守护程序|250||

#### <a name="update-management"></a>更新管理

下表显示了用于更新管理的限制。

| **资源** | **限制**| **说明** |
|---|---|---|
|每个更新部署的计算机的数目|1000||
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
ms.openlocfilehash: 61f82771e53ac9bb594484b29bb109a03cee674b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553333"
---
#### <a name="process-automation"></a>流程自动化

| 资源 | 最大限制 |说明|
| --- | --- |---|
| 每个 Azure 自动化帐户 （nonscheduled 作业） 每隔 30 秒可以提交新作业最大数目 |100 |达到此限制后，后续请求创建作业失败。 客户端会收到错误响应。|
| 在每个自动化帐户 （nonscheduled 作业） 的时间的同一实例上并行运行作业的最大数目 |200 |达到此限制后，后续请求创建作业失败。 客户端会收到错误响应。|
| 最大存储大小的 30 天滚动内的作业元数据 | 10 GB （约 4 万个作业）|达到此限制后，后续请求创建作业失败。 |
| 可以导入每个自动化帐户每隔 30 秒的模块的最大数目 |5 ||
| 模块的最大大小 |100 MB ||
| 作业运行时间，免费层 |每个订阅每个日历月 500 分钟 ||
| 最大磁盘空间允许每个沙盒<sup>1</sup> |1 GB |适用于仅 Azure 沙盒。|
| 最大内存提供给沙盒量<sup>1</sup> |400 MB |适用于仅 Azure 沙盒。|
| 最大数量的每个沙盒允许的网络套接字<sup>1</sup> |1,000 |适用于仅 Azure 沙盒。|
| 每个 runbook 允许的最大运行时<sup>1</sup> |3 小时 |适用于仅 Azure 沙盒。|
| 在订阅中的自动化帐户的最大数目 |无限制 ||
|可以在单个的混合 Runbook 辅助角色运行的并发作业的最大数目|50 ||
| 最大 runbook 作业参数大小   | 512 千位||
| 最大 runbook 参数   | 50|如果达到 50 参数限制，可以将 JSON 或 XML 字符串传递给参数并使用 runbook 对其进行分析。|
| 最大 webhook 有效负载大小 |  512 千位|
| 保留作业数据的最大天数|30 天|
| PowerShell 工作流状态的最大大小 |5 MB| 适用于 PowerShell 工作流 runbook 时执行检查点操作工作流。|

<sup>1</sup>沙盒是一个共享的环境，可由多个作业。 沙盒的资源限制由绑定使用的同一沙盒的作业。

#### <a name="change-tracking-and-inventory"></a>更改跟踪和清单

下表显示了每台计算机进行更改跟踪的跟踪的项限制。

| **资源** | **限制**| **说明** |
|---|---|---|
|文件|500||
|注册表|250||
|Windows 软件|250|不包括软件更新。|
|Linux 包|1,250||
|服务|250||
|守护程序|250||

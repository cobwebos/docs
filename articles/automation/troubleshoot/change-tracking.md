---
title: 排查 Azure 自动化更改跟踪和库存的问题
description: 本文介绍了如何排查和解决 Azure 自动化更改跟踪和库存功能的问题。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ddd41756f0e373e3bf627a88f441512fe0db91b7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187228"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>排查更改跟踪和库存问题

本文介绍了如何排查和解决 Azure 自动化更改跟踪和库存的问题。 有关更改跟踪和库存的一般信息，请参阅[更改跟踪和库存概述](../change-tracking.md)。

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>场景：未显示 Windows 计算机的更改跟踪和库存记录

#### <a name="issue"></a>问题

对于已启用更改跟踪和库存功能的 Windows 计算机，你未看到任何更改跟踪和库存结果。

#### <a name="cause"></a>原因

此错误的可能原因如下：

* 适用于 Windows 的 Azure Log Analytics 代理未运行。
* 与自动化帐户的通信被阻止。
* 未下载更改跟踪和库存管理包。
* 要启用的 VM 可能来自某台克隆的计算机，但尚未通过系统准备工具 (sysprep) 准备该计算机以使其安装有适用于 Windows 的 Log Analytics 代理。

#### <a name="resolution"></a>解决方案

在 Log Analytics 代理计算机上，转到 **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** 并运行以下命令：

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

如果仍需帮助，可以收集诊断信息并联系支持人员。

> [!NOTE]
> 默认情况下，Log Analytics 代理会启用错误跟踪。 若要如上例所示启用详细错误消息，请使用 `VER` 参数。 要进行信息跟踪，请在调用 `StartTracing.cmd` 时使用 `INF`。

##### <a name="log-analytics-agent-for-windows-not-running"></a>适用于 Windows 的 Log Analytics 代理未运行

验证适用于 Windows 的 Log Analytics 代理 (**HealthService.exe**) 是否正在计算机上运行。

##### <a name="communication-to-automation-account-blocked"></a>与自动化帐户的通信被阻止

检查计算机上的事件查看器，查看是否有任何事件包含 `changetracking` 一词。

若要了解必须允许哪些地址和端口才能让更改跟踪和库存正常运行，请参阅[网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

##### <a name="management-packs-not-downloaded"></a>未下载管理包

验证是否在本地安装了以下更改跟踪和库存管理包：

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM 来自尚未使用 sysprep 进行配置的克隆计算机

如果使用克隆的映像，请先使用 sysprep 配置该映像，然后安装适用于 Windows 的 Log Analytics 代理。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>方案：Linux 计算机上未显示更改跟踪和库存结果

#### <a name="issue"></a>问题

对于已启用更改跟踪和库存功能的 Linux 计算机，你未看到任何更改跟踪和库存结果。 

#### <a name="cause"></a>原因
下面是此问题的具体可能原因：
* 适用于 Linux 的 Log Analytics 代理未运行。
* 适用于 Linux 的 Log Analytics 代理未正确配置。
* 发生了文件完整性监视 (FIM) 冲突。

#### <a name="resolution"></a>解决方法 

##### <a name="log-analytics-agent-for-linux-not-running"></a>适用于 Linux 的 Log Analytics 代理未运行

验证适用于 Linux 的 Log Analytics 代理 (**omsagent**) 的守护程序是否正在计算机上运行。 在链接到自动化帐户的 Log Analytics 工作区中运行以下查询。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

如果查询结果中未显示你的计算机，则表示该计算机最近未签入。 可能存在本地配置问题，应重新安装代理。 有关安装和配置的信息，请参阅[使用 Log Analytics 代理收集日志数据](../../azure-monitor/platform/log-analytics-agent.md)。

如果你的计算机显示在查询结果中，请验证作用域配置。 参阅[在 Azure Monitor 中设定监视解决方案的目标](../../azure-monitor/insights/solution-targeting.md)。

有关此问题的更多排查方法，请参阅[问题：看不到任何 Linux 数据](../../azure-monitor/platform/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data)。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>适用于 Linux 的 Log Analytics 代理未正确配置

适用于 Linux 的 Log Analytics 代理可能未正确配置，因此无法使用 OMS 日志收集器工具来收集日志和命令行输出。 请参阅[更改跟踪和库存概述](../change-tracking.md)。

##### <a name="fim-conflicts"></a>FIM 冲突

Azure 安全中心的 FIM 功能可能错误地验证了 Linux 文件的完整性。 请验证 FIM 是否正常运行，以及是否针对 Linux 文件监视进行了正确配置。 请参阅[更改跟踪和库存概述](../change-tracking.md)。

## <a name="next-steps"></a>后续步骤

如果未在此处看到你遇到的问题，或者无法解决你遇到的问题，请尝试以下途径之一以获取其他支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
* 连接到 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 Azure 支持人员会将你连接到 Azure 社区，从中可以获得解答、支持和专家建议。
* 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。

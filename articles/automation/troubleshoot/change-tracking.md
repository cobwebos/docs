---
title: 对 Azure 更改跟踪问题进行故障排除
description: 了解如何使用 Azure 自动化更改跟踪和清单功能解决问题。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198524"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>对更改跟踪和清单进行故障排除

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>方案：未为 Windows 计算机显示更改跟踪记录

#### <a name="issue"></a>问题

您看不到为更改跟踪而上载的 Windows 计算机的任何更改跟踪或清单结果。

#### <a name="cause"></a>原因

此错误可能有以下原因：

* 微软监视代理未运行。
* 回自动化帐户的通信被阻止。
* 不会下载用于更改跟踪的管理包。
* 正在装载的 VM 可能来自未安装 Microsoft 监视代理的克隆计算机。

#### <a name="resolution"></a>解决方法

下面描述的解决方案可能有助于解决您的问题。 如果仍需要帮助，可以收集诊断信息并联系支持人员。 在代理计算机上，导航到 C：\程序文件\微软监视代理\代理\工具并运行以下命令：

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 默认情况下，启用错误跟踪。 要启用详细错误消息，如上例所示，请使用*VER*参数。 有关信息跟踪，在调用**StartTracing.cmd**时使用*INF。*

##### <a name="microsoft-monitoring-agent-not-running"></a>微软监控代理未运行

验证 Microsoft 监视代理 （HealthService.exe） 是否在计算机上运行。

##### <a name="communication-to-automation-account-blocked"></a>与自动化帐户的通信被阻止

检查计算机上的事件查看器，并查找其中具有"更改跟踪"字样的任何事件。

请参阅[使用混合 Runbook 辅助角色自动在数据中心或云中实现资源](../automation-hybrid-runbook-worker.md#network-planning)，以了解更改跟踪工作必须允许的地址和端口。

##### <a name="management-packs-not-downloaded"></a>未下载的管理包

验证以下更改跟踪和库存管理包是否安装在本地：

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>未进行压缩的克隆计算机的 VM

如果使用克隆映像，则先对映像进行系统准备，然后安装 Microsoft 监视代理。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>方案：Linux 计算机上无更改跟踪或库存结果

#### <a name="issue"></a>问题

您看不到已加入以进行更改跟踪的 Linux 计算机的任何库存或更改跟踪结果。 

#### <a name="cause"></a>原因
以下是特定于此问题的可能原因：
* Linux 的日志分析代理未运行。
* Linux 的日志分析代理配置不正确。
* 存在文件完整性监视 （FIM） 冲突。

#### <a name="resolution"></a>解决方法 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Linux 未运行的日志分析代理

验证 Linux 日志分析代理（omsagent）的守护进程是否在您的计算机上运行。 在链接到自动化帐户的日志分析工作区中运行以下查询。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

如果在查询结果中看不到计算机，则计算机最近未签入。 可能存在本地配置问题，您应该重新安装代理。 有关安装和配置的信息，请参阅[使用日志分析代理收集日志数据](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。 

如果计算机显示在查询结果中，请验证范围配置。 请参阅[Azure 监视器 中的目标监视解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)。

有关此问题的更多故障排除，请参阅[问题：您没有看到任何 Linux 数据](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Linux 的日志分析代理配置不正确

使用 OMS 日志收集器工具，可能未正确配置 Linux 的日志分析代理以用于日志和命令行输出集合。 请参阅[使用"更改跟踪"解决方案跟踪环境中的更改](../change-tracking.md)。

##### <a name="fim-conflicts"></a>职能指令手册冲突

Azure 安全中心的 FIM 功能可能错误地验证了 Linux 文件的完整性。 验证 FIM 是否可操作并正确配置用于 Linux 文件监视。 请参阅[使用"更改跟踪"解决方案跟踪环境中的更改](../change-tracking.md)。

## <a name="next-steps"></a>后续步骤

如果您没有看到问题或无法解决问题，请使用以下通道之一获得更多支持。

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帐户连接，通过将 Azure 社区连接到正确的资源（答案、支持和专家），从而改善客户体验。
* 如需更多帮助，可以提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。

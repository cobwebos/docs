---
title: 对 Azure 更改跟踪问题进行故障排除
description: 了解如何通过 Azure 自动化更改跟踪和清单功能排查和解决问题。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198524"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>对更改跟踪和清单进行故障排除

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>方案：不显示 Windows 计算机更改跟踪记录

#### <a name="issue"></a>问题

对于载入更改跟踪的 Windows 计算机，不会看到任何更改跟踪或清单结果。

#### <a name="cause"></a>原因

此错误可能有以下原因：

* Microsoft Monitoring Agent 没有运行。
* 正在阻止回发到自动化帐户的通信。
* 不会下载更改跟踪的管理包。
* 要载入的 VM 可能来自克隆的计算机，但该计算机未经过系统准备安装 Microsoft Monitoring Agent。

#### <a name="resolution"></a>解决方法

下面所述的解决方案可帮助解决问题。 如果仍需要帮助，可以收集诊断信息并联系支持人员。 在代理计算机上，导航到 C:\Program Files\Microsoft Monitoring Agent\Agent\Tools，并运行以下命令：

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 默认情况下，启用错误跟踪。 若要启用如上一示例中所示的详细错误消息，请使用*VER*参数。 有关信息跟踪，请在调用**starttracing.cmd**时使用*INF* 。

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent 未运行

验证计算机上是否正在运行 Microsoft Monitoring Agent （运行状况服务）。

##### <a name="communication-to-automation-account-blocked"></a>已阻止与自动化帐户通信

检查计算机上的事件查看器，并查找其中包含 "更改跟踪" 一词的任何事件。

请参阅[使用混合 Runbook 辅助角色自动执行数据中心或云中的资源](../automation-hybrid-runbook-worker.md#network-planning)，了解更改跟踪工作时必须允许的地址和端口。

##### <a name="management-packs-not-downloaded"></a>未下载管理包

验证以下更改跟踪和清单管理包是否已在本地安装：

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>尚未经过系统准备的克隆计算机上的 VM

如果使用克隆的映像，请先对映像进行 sysprep，然后安装 Microsoft Monitoring Agent。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>方案： Linux 计算机上无更改跟踪或清单结果

#### <a name="issue"></a>问题

对于适用于更改跟踪的 Linux 计算机，看不到任何清单或更改跟踪结果。 

#### <a name="cause"></a>原因
下面是特定于此问题的可能原因：
* 适用于 Linux 的 Log Analytics 代理未运行。
* 适用于 Linux 的 Log Analytics 代理未正确配置。
* 存在文件完整性监视（FIM）冲突。

#### <a name="resolution"></a>解决方法 

##### <a name="log-analytics-agent-for-linux-not-running"></a>适用于 Linux 的 Log Analytics 代理未运行

验证适用于 Linux 的 Log Analytics agent （omsagent）的守护程序是否正在计算机上运行。 在链接到自动化帐户的 Log Analytics 工作区中运行以下查询。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

如果未在查询结果中看到您的计算机，则该计算机最近未签入。 可能存在本地配置问题，应重新安装代理。 有关安装和配置的详细信息，请参阅[用 Log Analytics Agent 收集日志数据](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。 

如果计算机显示在查询结果中，请验证作用域配置。 请参阅将[监视解决方案定位到 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)。

有关此问题的详细疑难解答，请参阅[问题：你未看到任何 Linux 数据](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>适用于 Linux 的 Log Analytics 代理配置不正确

对于使用 OMS 日志收集器工具的日志和命令行输出收集，可能未正确配置适用于 Linux 的 Log Analytics 代理。 请参阅[通过更改跟踪解决方案跟踪环境中的更改](../change-tracking.md)。

##### <a name="fim-conflicts"></a>FIM 冲突

Azure 安全中心的 FIM 功能可能会不正确地验证 Linux 文件的完整性。 验证 FIM 是否正常运行且已正确配置 Linux 文件监视。 请参阅[通过更改跟踪解决方案跟踪环境中的更改](../change-tracking.md)。

## <a name="next-steps"></a>后续步骤

如果你没有看到你的问题或无法解决你的问题，请使用以下通道之一获得更多支持。

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。

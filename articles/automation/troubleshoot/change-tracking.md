---
title: 解决更改跟踪和库存问题
description: 了解如何使用 Azure 自动化更改跟踪和清单解决方案解决问题。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679351"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>解决更改跟踪和库存问题

本文介绍如何解决更改跟踪和库存问题。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](../automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>方案：未为 Windows 计算机显示更改跟踪和库存记录

#### <a name="issue"></a>问题

您看不到已上载的 Windows 计算机的任何更改跟踪和清单结果。

#### <a name="cause"></a>原因

此错误可能有以下原因：

* Windows 的日志分析代理未运行。
* 回自动化帐户的通信被阻止。
* 不会下载更改跟踪和库存管理包。
* 正在装载的 VM 可能来自未与安装的 Windows 的日志分析代理结合的克隆计算机。

#### <a name="resolution"></a>解决方法

在日志分析代理计算机上，导航到**C：\程序文件\Microsoft 监视代理\代理\工具**并运行以下命令：

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

如果仍需要帮助，可以收集诊断信息并联系支持人员。 

> [!NOTE]
> 默认情况下，日志分析代理支持错误跟踪。 要启用详细错误消息，如上例所示，请使用 参数`VER`。 要进行信息跟踪，请在调用 `StartTracing.cmd` 时使用 `INF`。

##### <a name="log-analytics-agent-for-windows-not-running"></a>未运行的 Windows 的日志分析代理

验证 Windows 的日志分析代理 （**HealthService.exe**） 是否在计算机上运行。

##### <a name="communication-to-automation-account-blocked"></a>与自动化帐户的通信被阻止

检查计算机上的事件查看器并查看包含 `changetracking` 一词的任何事件。

请参阅[使用混合 Runbook Worker 来自动执行数据中心或云中的资源](../automation-hybrid-runbook-worker.md#network-planning)，以了解更改跟踪和清单正常工作必须允许的地址和端口。

##### <a name="management-packs-not-downloaded"></a>未下载的管理包

验证以下更改跟踪和库存管理包是否安装在本地：

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>未进行压缩的克隆计算机的 VM

如果使用克隆映像，请先对映像进行系统准备，然后安装 Windows 的日志分析代理。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>方案：Linux 计算机上无更改跟踪和库存结果

#### <a name="issue"></a>问题

您看不到为解决方案而加入的 Linux 计算机的任何库存和更改跟踪结果。 

#### <a name="cause"></a>原因
以下是特定于此问题的可能原因：
* Linux 的日志分析代理未运行。
* Linux 的日志分析代理配置不正确。
* 存在文件完整性监视 （FIM） 冲突。

#### <a name="resolution"></a>解决方法 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Linux 未运行的日志分析代理

验证 Linux**的日志**分析代理的守护进程是否在您的计算机上运行。 在链接到自动化帐户的日志分析工作区中运行以下查询。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

如果在查询结果中看不到计算机，则计算机最近未签入。 可能存在本地配置问题，您应该重新安装代理。 有关安装和配置的信息，请参阅[使用日志分析代理收集日志数据](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。 

如果计算机显示在查询结果中，请验证范围配置。 请参阅[Azure 监视器 中的目标监视解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)。

有关此问题的更多故障排除，请参阅[问题：您没有看到任何 Linux 数据](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Linux 的日志分析代理配置不正确

使用 OMS 日志收集器工具，可能未正确配置 Linux 的日志分析代理以用于日志和命令行输出集合。 请参阅[使用"更改跟踪"和"清单"解决方案跟踪环境中的更改](../change-tracking.md)。

##### <a name="fim-conflicts"></a>职能指令手册冲突

Azure 安全中心的 FIM 功能可能错误地验证了 Linux 文件的完整性。 验证 FIM 是否可操作并正确配置用于 Linux 文件监视。 请参阅[使用"更改跟踪"和"清单"解决方案跟踪环境中的更改](../change-tracking.md)。

## <a name="next-steps"></a>后续步骤

如果您在上面看不到问题或无法解决问题，请尝试以下渠道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)连接 ，官方 Microsoft Azure 帐户通过将 Azure 社区连接到正确的资源（答案、支持和专家）来改善客户体验。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。

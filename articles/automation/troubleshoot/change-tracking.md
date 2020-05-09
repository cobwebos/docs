---
title: 排查更改跟踪和清单问题
description: 了解如何通过 Azure 自动化更改跟踪和清单解决方案排查和解决问题。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582147"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>排查更改跟踪和清单问题

本文介绍如何排查 Azure 自动化更改跟踪和清单问题。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，你可以通过使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>方案：不显示 Windows 计算机的更改跟踪和清单记录

#### <a name="issue"></a>问题

对于载入的 Windows 计算机，看不到任何更改跟踪和清单结果。

#### <a name="cause"></a>原因

此错误可能有以下原因：

* 适用于 Windows 的 Azure Log Analytics 代理未运行。
* 正在阻止回发到自动化帐户的通信。
* 不会下载更改跟踪和库存管理包。
* 要载入的 VM 可能来自克隆的计算机，该计算机未与安装的 Windows 的 Log Analytics 代理经过系统准备。

#### <a name="resolution"></a>解决方法

在 Log Analytics 代理计算机上，请参阅**C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** ，并运行以下命令：

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

如果仍需要帮助，可以收集诊断信息并联系支持人员。

> [!NOTE]
> 默认情况下，Log Analytics 代理启用错误跟踪。 若要启用如前面的示例所示的详细错误消息`VER` ，请使用参数。 对于信息跟踪，请`INF`在调用`StartTracing.cmd`时使用。

##### <a name="log-analytics-agent-for-windows-not-running"></a>适用于 Windows 的 Log Analytics 代理未运行

验证 Windows 的 Log Analytics 代理（**运行状况服务**）是否正在计算机上运行。

##### <a name="communication-to-automation-account-blocked"></a>已阻止与自动化帐户通信

检查计算机上的事件查看器，并查找其中包含单词`changetracking`的任何事件。

若要了解更改跟踪和清单工作时必须允许的地址和端口，请参阅[使用混合 Runbook 辅助角色自动执行数据中心或云中的资源](../automation-hybrid-runbook-worker.md#network-planning)。

##### <a name="management-packs-not-downloaded"></a>未下载管理包

验证以下更改跟踪和清单管理包是否已在本地安装：

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>尚未经过系统准备的克隆计算机上的 VM

如果使用克隆的映像，请先对映像执行 sysprep，然后安装适用于 Windows 的 Log Analytics 代理。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>方案： Linux 计算机上无更改跟踪和清单结果

#### <a name="issue"></a>问题

对于载入解决方案的 Linux 计算机，不会看到任何更改跟踪和清单结果。 

#### <a name="cause"></a>原因
下面是特定于此问题的可能原因：
* 适用于 Linux 的 Log Analytics 代理未运行。
* 适用于 Linux 的 Log Analytics 代理未正确配置。
* 存在文件完整性监视（FIM）冲突。

#### <a name="resolution"></a>解决方法 

##### <a name="log-analytics-agent-for-linux-not-running"></a>适用于 Linux 的 Log Analytics 代理未运行

验证适用于 Linux 的 Log Analytics agent （**omsagent**）的守护程序是否正在计算机上运行。 在链接到自动化帐户的 Log Analytics 工作区中运行以下查询。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

如果未在查询结果中看到您的计算机，则该计算机最近未签入。 可能存在本地配置问题，应重新安装代理。 有关安装和配置的详细信息，请参阅[用 Log Analytics Agent 收集日志数据](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。

如果计算机显示在查询结果中，请验证作用域配置。 请参阅将[监视解决方案定位到 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)。

有关此问题的详细疑难解答，请参阅[问题：你未看到任何 Linux 数据](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>适用于 Linux 的 Log Analytics 代理配置不正确

对于使用 OMS 日志收集器工具进行日志和命令行输出收集，可能未正确配置适用于 Linux 的 Log Analytics 代理。 请参阅[跟踪环境中的更改，并提供更改跟踪和清单解决方案](../change-tracking.md)。

##### <a name="fim-conflicts"></a>FIM 冲突

Azure 安全中心的 FIM 功能可能会不正确地验证 Linux 文件的完整性。 验证 FIM 是否正常运行且已正确配置 Linux 文件监视。 请参阅[跟踪环境中的更改，并提供更改跟踪和清单解决方案](../change-tracking.md)。

## <a name="next-steps"></a>后续步骤

如果在此处看不到你的问题，或者无法解决你的问题，请尝试以下通道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帐户联系，以改善客户体验。 Azure 支持将 Azure 社区连接到答案、支持和专家。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。

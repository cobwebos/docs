---
title: Azure Automation 中的 Windows update 代理问题疑难解答更新管理
description: 了解如何使用更新管理解决方案排查和解决 Windows update 代理的问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: e9af9c6472f49ebccd36e8d73688636c98918ff1
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996441"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Windows update 代理问题疑难解答

在更新管理中，计算机未显示为 "就绪" （正常）的原因有很多。 可以检查 Windows 混合 Runbook 辅助角色代理的运行状况，以确定基本问题。 下面是计算机的三个就绪状态：

* 准备就绪：混合 Runbook 辅助角色已部署，最后发现不到1小时前。
* 已断开连接：混合 Runbook 辅助角色已部署，上次在一小时前查看。
* 未配置：混合 Runbook 辅助角色找不到或未完成加入。

> [!NOTE]
> 在 Azure 门户显示的内容和计算机的当前状态之间可能会有轻微的延迟。

本文介绍如何在[脱机方案](#troubleshoot-offline)中从 Azure 门户和非 Azure 计算机运行 Azure 计算机的疑难解答。 

> [!NOTE]
> 疑难解答脚本现在包含对 Windows Server Update Services （WSUS）以及 autodownload 和 install 密钥的检查。 

## <a name="start-the-troubleshooter"></a>启动“故障排除”

对于 Azure 计算机，你可以通过在门户中的 "**更新代理准备情况**" 列下选择 "**故障排除**" 链接，启动 "更新代理疑难解答" 页。 对于非 Azure 计算机，此链接会将你带入本文。 请参阅[脱机说明](#troubleshoot-offline)，对非 Azure 计算机进行故障排除。

![虚拟机更新管理列表的屏幕截图](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> 若要查看混合 Runbook 辅助角色的运行状况，VM 必须正在运行。 如果 VM 没有运行，屏幕上会显示“启动 VM”按钮****。

在“更新代理故障排除”页上选择“运行检查”，启动故障排除****。 疑难解答使用[运行命令](../../virtual-machines/windows/run-command.md)在计算机上运行脚本，以验证依赖关系。 完成故障排除时，它会返回检查的结果。

!["更新代理疑难解答" 页的屏幕截图](../media/update-agent-issues/troubleshoot-page.png)

结果准备就绪后会显示在该页上。 检查部分显示每个检查中包含的内容。

![更新代理检查疑难解答的屏幕截图](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先决条件检查

### <a name="operating-system"></a>操作系统

操作系统检查会验证混合 Runbook 辅助角色是否正在运行下表中所示的操作系统之一。

|操作系统  |注释  |
|---------|---------|
|Windows Server 2012 及更高版本 |需要 .NET Framework 4.6 或更高版本。 （[下载 .NET Framework](/dotnet/framework/install/guide-for-developers)。）<br/> Windows PowerShell 5.1 是必需的。  （[下载 Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。）        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework 检查将验证系统是否已安装[.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345)或更高版本。

### <a name="wmf-51"></a>WMF 5.1

WMF 检查将验证系统是否具有 windows Management framework （WMF）所需的 Windows management Framework （WMF）版本，即[Windows Management framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。

### <a name="tls-12"></a>TLS 1.2

此项检查用于确定是否使用 TLS 1.2 加密通信。 该平台不再支持 TLS 1.0。 使用 TLS 1.2 与更新管理通信。

## <a name="connectivity-checks"></a>连接性检查

### <a name="registration-endpoint"></a>注册终结点

此检查确定代理是否可以与代理服务正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与注册终结点通信。 有关要打开的地址和端口的列表，请参阅[网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="operations-endpoint"></a>操作终结点

此检查用于确定代理是否可以与作业运行时数据服务正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与作业运行时数据服务通信。 有关要打开的地址和端口的列表，请参阅[网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="vm-service-health-checks"></a>VM 服务运行状况检查

### <a name="monitoring-agent-service-status"></a>监视代理服务的状态

此检查确定适用于 Windows 的 Log Analytics 代理（`healthservice`）是否正在计算机上运行。 若要详细了解如何对服务进行故障排除，请参阅[Windows Log Analytics 代理未运行](hybrid-runbook-worker.md#mma-not-running)。

若要重新安装适用于 Windows 的 Log Analytics 代理，请参阅[安装适用于 windows 的代理](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。

### <a name="monitoring-agent-service-events"></a>监视代理服务事件

此检查确定在过去24小时内，计算机上的 Azure Operations Manager 日志中是否显示任何4502事件。

若要了解有关此事件的详细信息，请参阅此事件[Operations Manager 日志中的事件 4502](hybrid-runbook-worker.md#event-4502) 。

## <a name="access-permissions-checks"></a>访问权限检查

> [!NOTE]
> 如果配置了一个代理服务器，则疑难解答当前不会通过代理服务器路由流量。

### <a name="crypto-folder-access"></a>加密文件夹访问

加密文件夹访问检查确定本地系统帐户是否有权访问 C:\ProgramData\Microsoft\Crypto\RSA。

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>脱机使用故障排除

你可以通过在本地运行脚本来脱机使用混合 Runbook 辅助角色上的疑难解答。 从 PowerShell 库获取以下脚本： [WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)。 若要运行该脚本，必须安装 WMF 4.0 或更高版本。 若要下载最新版本的 PowerShell，请参阅[安装各种版本的 powershell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)。

此脚本的输出如以下示例所示：

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>后续步骤

[对混合 Runbook 辅助角色进行故障排除](hybrid-runbook-worker.md)

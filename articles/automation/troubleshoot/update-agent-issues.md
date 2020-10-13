---
title: 使用 Azure 自动化排查 Windows 更新代理问题
description: 本文介绍如何排查和解决在进行更新管理期间出现的 Windows 更新代理问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 92020313fccf1b8be0add58a7bafab62b5daa4d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86187126"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>排查 Windows 更新代理问题

在进行更新管理部署时计算机未显示为已就绪（正常），这可能有多种原因。 你可以检查 Windows 混合 Runbook 辅助角色代理的运行状况，以确定潜在问题。 以下是计算机的三种就绪状态：

* 迁移就绪性：已部署混合 Runbook 辅助角色，并且上次访问它的时间距当前时间不到一小时。
* 已断开连接：已部署混合 Runbook 辅助角色，并且上次访问它的时间距当前时间超过一小时。
* 未配置：混合 Runbook 辅助角色找不到或尚未完成部署。

> [!NOTE]
> Azure 门户显示的内容和计算机的当前状态之间可能会有些微延迟。

本文介绍如何从 Azure 门户为 Azure 计算机运行故障排除，以及如何为[离线场景](#troubleshoot-offline)下的非 Azure 计算机运行故障排除。 

> [!NOTE]
> 故障排除脚本现在包含对 Windows Server Update Services (WSUS) 以及对自动下载和安装密钥的检查。 

## <a name="start-the-troubleshooter"></a>启动“故障排除”

对于 Azure 计算机，通过选中门户中“更新代理准备”列下的“故障排除”链接，可以启动“排除更新代理故障”页。  对于非 Azure 计算机，该链接会转到本文。 若要对非 Azure 计算机进行故障排除，请参阅[脱机进行故障排除](#troubleshoot-offline)。

![虚拟机更新管理列表的屏幕截图](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> 若要查看混合 Runbook 辅助角色的运行状况，VM 必须处于运行状态。 如果 VM 没有运行，屏幕上会显示“启动 VM”按钮。

在“排除更新代理故障”页上选择“运行检查”，启动故障排除。 故障排除使用[运行命令](../../virtual-machines/windows/run-command.md)在计算机上运行脚本以验证依赖项。 完成故障排除时，它会返回检查的结果。

![“排除更新代理故障”页面的屏幕截图](../media/update-agent-issues/troubleshoot-page.png)

结果准备就绪后会显示在该页上。 检查部分显示每个检查中包含的内容。

![“排除更新代理故障检查”的屏幕截图](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先决条件检查

### <a name="operating-system"></a>操作系统

操作系统检查将验证混合 Runbook 辅助角色是否正在运行下表中的操作系统之一。

|操作系统  |说明  |
|---------|---------|
|Windows Server 2012 和更高版本 |需要 .NET Framework 4.6 或更高版本。 （[下载 .NET Framework](/dotnet/framework/install/guide-for-developers)。）<br/> 需要 Windows PowerShell 5.1。  （[下载 Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。）        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework 检查，用于验证系统是否安装了 [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) 或更高版本。

### <a name="wmf-51"></a>WMF 5.1

WMF 检查用于验证系统是否具有所需的 Windows Management Framework (WMF) 版本，即 [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。

### <a name="tls-12"></a>TLS 1.2

此项检查用于确定是否使用 TLS 1.2 加密通信。 该平台不再支持 TLS 1.0。 请使用 TLS 1.2 与更新管理进行通信。

## <a name="connectivity-checks"></a>连接性检查

### <a name="registration-endpoint"></a>注册终结点

此检查确定代理是否可以与代理服务正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与注册终结点通信。 有关要打开的地址和端口的列表，请参阅[网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="operations-endpoint"></a>操作终结点

此检查用于确定代理是否可以与作业运行时数据服务正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与作业运行时数据服务通信。 有关要打开的地址和端口的列表，请参阅[网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="vm-service-health-checks"></a>VM 服务运行状况检查

### <a name="monitoring-agent-service-status"></a>监视代理服务的状态

此检查将确定适用于 Windows 的 Log Analytics 代理 (`healthservice`) 是否正在计算机上运行。 若要详细了解如何对服务进行故障排查，请参阅[适用于 Windows 的 Log Analytics 代理未运行](hybrid-runbook-worker.md#mma-not-running)。

若要重新安装适用于 Windows 的 Log Analytics 代理，请参阅[安装适用于 Windows 的代理](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。

### <a name="monitoring-agent-service-events"></a>监视代理服务事件

此检查将确定过去 24 小时内计算机上的 Operations Manager 日志中是否出现过任何 4502 事件。

若要了解有关此事件的详细信息，请参阅 [Operations Manager 日志中的事件 4502](hybrid-runbook-worker.md#event-4502) 来了解此事件。

## <a name="access-permissions-checks"></a>访问权限检查

> [!NOTE]
> 如果配置了代理服务器，则故障排除当前不会通过它路由流量。

### <a name="crypto-folder-access"></a>Crypto 文件夹访问权限

Crypto 文件夹访问检查将确定本地系统帐户是否有权访问 C:\ProgramData\Microsoft\Crypto\RSA。

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>脱机进行故障排除

可以通过在本地运行脚本，在混合 Runbook 辅助角色上脱机使用故障排除。 从 PowerShell 库获取以下脚本：[Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)。 若要运行该脚本，必须安装 WMF 4.0 或更高版本。 若要下载最新版本的 PowerShell，请参阅[安装各种版本的 PowerShell](/powershell/scripting/install/installing-powershell)。

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

[排查混合 Runbook 辅助角色问题](hybrid-runbook-worker.md)。

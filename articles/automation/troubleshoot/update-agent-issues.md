---
title: 了解 Azure 更新管理中的 Windows 代理检查结果
description: 了解如何排查更新管理代理问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 72fdfe912a5560ce0c0e3886dd3c56cf9534dc22
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480779"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>了解更新管理中的 Windows 代理检查结果

可能会有许多原因导致计算机在更新管理中不显示“就绪”。 在更新管理中，可以检查混合辅助角色代理的运行状况以确定潜在问题。 本文介绍如何从 Azure 门户为 Azure 计算机运行故障排除，以及如何为[离线场景](#troubleshoot-offline)下的非 Azure 计算机运行故障排除。

下表列出计算机可能处于的三个就绪状态：

* **就绪** - 更新代理已部署且距上次查看不超过 1 小时。
* **断开连接** - 更新代理已部署且距上次查看超过 1 小时。
* **未配置** - 未找到更新代理或尚未完成载入。

> [!NOTE]
> 在 Azure 门户显示的内容和计算机的当前状态之间可能会有轻微的延迟。

## <a name="start-the-troubleshooter"></a>启动“故障排除”

对于 Azure 计算机，通过单击门户中“更新代理准备”列下的“故障排除”链接，可以启动“更新代理故障排除”页。 对于非 Azure 计算机，该链接会转到本文。 请参阅[离线说明](#troubleshoot-offline)来排查非 Azure 计算机问题。

![虚拟机更新管理列表](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> 要检查代理的运行状况，必须运行 VM。 如果 VM 没有运行，屏幕上会显示“启动 VM”按钮。

在“更新代理故障排除”页上选择“运行检查”，启动故障排除。 故障排除使用[运行命令](../../virtual-machines/windows/run-command.md)在计算机上运行脚本以验证代理依赖项。 完成故障排除时，它会返回检查的结果。

![“更新代理故障排除”页面](../media/update-agent-issues/troubleshoot-page.png)

结果准备就绪后会显示在该页上。 检查部分显示每个检查中包含的内容。

![“更新代理故障排除”检查](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先决条件检查

### <a name="operating-system"></a>操作系统

操作系统检查，用于验证混合 Runbook 辅助角色是否正在运行以下操作系统之一：

|操作系统  |说明  |
|---------|---------|
|Windows Server 2008 R2 RTM、Windows Server 2008 | 仅支持更新评估。         |
|Windows Server 2008 R2 SP1 和更高版本 |需要 .NET Framework 4.6 或更高版本。 （[下载 .NET Framework](/dotnet/framework/install/guide-for-developers)）<br/> Windows PowerShell 5.1 是必需的。  （[下载 Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)）        |

### <a name="net-462"></a>.NET 4.6。2

.NET Framework 检查将验证系统是否至少安装了[.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) 。

### <a name="wmf-51"></a>WMF 5.1

WMF 检查将验证系统是否具有所需的 Windows Management Framework （WMF）版本- [Windows Management framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。

### <a name="tls-12"></a>TLS 1.2

此项检查用于确定是否使用 TLS 1.2 加密通信。 该平台不再支持 TLS 1.0。 建议客户端使用 TLS 1.2 与更新管理进行通信。

## <a name="connectivity-checks"></a>连接性检查

### <a name="registration-endpoint"></a>注册终结点

此检查确定代理是否可以与代理服务正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与注册终结点通信。 有关要打开的地址和端口的列表，请参阅[混合辅助角色的网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="operations-endpoint"></a>操作终结点

此检查用于确定代理是否可以与作业运行时数据服务正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与作业运行时数据服务通信。 有关要打开的地址和端口的列表，请参阅[混合辅助角色的网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="vm-service-health-checks"></a>VM 服务运行状况检查

### <a name="monitoring-agent-service-status"></a>监视代理服务的状态

此项检查确定 `HealthService`（Microsoft Monitoring Agent）是否在计算机上运行。

若要详细了解如何对服务进行故障排查，请参阅 [Microsoft Monitoring Agent 未运行](hybrid-runbook-worker.md#mma-not-running)。

若要重新安装 Microsoft Monitoring Agent，请参阅[安装和配置 Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。

### <a name="monitoring-agent-service-events"></a>监视代理服务事件

此检查用于确定过去 24 小时内计算机上的 Operations Manager 日志中是否出现过任何 `4502` 事件。

有关此事件的详细信息，请参阅此事件的[故障排除指南](hybrid-runbook-worker.md#event-4502)。

## <a name="access-permissions-checks"></a>访问权限检查

### <a name="machinekeys-folder-access"></a>MachineKeys 文件夹访问权限

Crypto 文件夹访问检查确定本地系统帐户是否有权访问 C:\ProgramData\Microsoft\Crypto\RSA。

## <a name="troubleshoot-offline"></a>脱机进行故障排除

可以通过在本地运行脚本，在混合 Runbook 辅助角色上脱机使用故障排除。 可在 PowerShell 库中获得 [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) 脚本。 必须安装 WMF 4.0 或更高版本，才能运行该脚本。 若要下载最新版本的 PowerShell，请参阅[安装各种版本的 powershell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)。

此脚本的输出如以下示例所示：

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
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
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

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

若要排查混合 Runbook 辅助角色的更多问题，请参阅[混合 Runbook 辅助角色的故障排除](hybrid-runbook-worker.md)。


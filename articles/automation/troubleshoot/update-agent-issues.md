---
title: 了解 Azure 更新管理中的代理检查结果
description: 了解如何排查更新管理代理问题。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/10/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 1b258d115e7d9962ecab4b93dbcd98d13f5977c7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956674"
---
# <a name="understand-the-agent-check-results-in-update-management"></a>了解更新管理中的代理检查结果

非 Azure 计算机在更新管理中未显示“就绪”的原因可能有多种。 在更新管理中，可以检查混合辅助角色代理的运行状况以确定潜在问题。 本文介绍如何从 Azure 门户和脱机方案中运行故障排除。

## <a name="start-the-troubleshooter"></a>启动“故障排除”

通过单击门户中“更新代理准备”列下的“故障排除”链接，可以启动“更新代理故障排除”页。 此页显示代理的问题以及本文的链接，用于帮助你解决问题。

![VM 列表页](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> 检查要求 VM 处于运行状态。 如果 VM 未运行，会出现一个用于链接到“启动 VM”的按钮。

在“更新代理故障排除”页上，单击“运行检查”，启动故障排除。 故障排除使用[运行命令](../../virtual-machines/windows/run-command.md)在计算机上运行脚本以验证代理程序具有的依赖项。 完成故障排除时，它会返回检查的结果。

![故障排除页](../media/update-agent-issues/troubleshoot-page.png)

完成后，窗口中将返回结果。 [检查部分](#pre-requisistes-checks)提供有关每个检查正在寻找的信息。

![更新代理检查页](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先决条件检查

### <a name="operating-system"></a>操作系统

操作系统检查，用于验证混合 Runbook 辅助角色是否正在运行以下操作系统之一：

|操作系统  |说明  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 仅支持更新评估。         |
|Windows Server 2008 R2 SP1 和更高版本     |需要 .NET Framework 4.5 或更高版本。 （[下载 .NET 框架](/dotnet/framework/install/guide-for-developers)）<br/> 需要 Windows PowerShell 4.0 或更高版本。 （[下载 WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)）<br/> 为提高可靠性，建议使用 Windows PowerShell 5.1。  （[下载 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)）        |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理必须具有访问更新存储库的权限。 基于分类的修补需要借助“yum”来返回 CentOS 当前没有的安全数据。         |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|Ubuntu 14.04 LTS 和 16.04 LTS (x86/x64)      |Linux 代理必须具有访问更新存储库的权限。         |

### <a name="net-45"></a>.NET 4.5

.NET framework 检查，用于验证系统是否至少存在 [.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)。

### <a name="wmf-51"></a>WMF 5.1

WMF 检查，用于验证系统是否具有所需的 Windows Management Framework 版本。 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) 是支持的最低版本。 建议安装 [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) 以提高混合 Runbook 辅助角色的可靠性。

### <a name="tls-12"></a>TLS 1.2

此项检查用于确定是否使用 TLS 1.2 加密通信。 平台不再支持 TLS 1.0，建议客户端使用 TLS 1.2 与更新管理进行通信。

## <a name="connectivity-checks"></a>连接性检查

### <a name="registration-endpoint"></a>注册终结点

此检查确定代理是否可以与代理服务正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与注册终结点通信。 有关要打开的地址和端口的列表，请参阅[混合辅助角色的网络规划](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>操作终结点

此检查用于确定代理是否可以与作业运行时数据服务正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与作业运行时数据服务通信。 有关要打开的地址和端口的列表，请参阅[混合辅助角色的网络规划](../automation-hybrid-runbook-worker.md#network-planning)

## <a name="vm-service-health-checks"></a>VM 服务运行状况检查

### <a name="monitoring-agent-service-status"></a>监视代理服务的状态

此项检查确定 Microsoft Monitoring Agent `HealthService` 是否在计算机上运行。

若要详细了解如何对服务进行故障排查，请参阅 [Microsoft Monitoring Agent 未运行](hybrid-runbook-worker.md#mma-not-running)。

若要重新安装 Microsoft Monitoring Agent，请参阅[安装和配置 Microsoft Monitoring Agent](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent)

### <a name="monitoring-agent-service-events"></a>监视代理服务事件

此检查用于确定过去 24 小时内计算机上的 Operations Manager 日志中是否有任何 `4502` 事件。

有关此事件的详细信息，请参阅此事件的[故障排除指南](hybrid-runbook-worker.md#event-4502)。

## <a name="access-permissions-checks"></a>访问权限检查

### <a name="machinekeys-folder-access"></a>MachineKeys 文件夹访问权限

加密文件夹访问权限检查可确定本地系统帐户是否有权访问 `C:\ProgramData\Microsoft\Crypto\RSA`

## <a name="troubleshoot-offline"></a>脱机使用故障排除

可以通过在本地运行脚本，在混合 Runbook 辅助角色上脱机使用故障排除。 脚本 [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) 可在 PowerShell 库中找到。 以下示例显示了此脚本的输出示例：

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
RuleName                    : .Net Framework 4.5+
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

若要排查混合 Runbook 辅助角色的其他问题，请参阅[故障排除 - 混合 Runbook 辅助角色](hybrid-runbook-worker.md)

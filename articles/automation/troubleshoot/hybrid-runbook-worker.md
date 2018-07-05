---
title: 故障排除 - Azure 自动化混合 Runbook 辅助角色
description: 本文提供对 Azure 自动化混合 Runbook 辅助角色进行故障排除的信息
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cae7253a4bfcb4f83baf003a4d9d3c367d8f014
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064218"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>对混合 Runbook 辅助角色进行故障排除

本文提供有关混合 Runbook 辅助角色故障排除问题的信息。

## <a name="general"></a>常规

混合 Runbook 辅助角色依靠代理与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 对于 Windows，此代理为 Microsoft Monitoring Agent。 对于 Linux，此代理为适用于 Linux 的 OMS 代理。

###<a name="runbook-execution-fails"></a>方案：Runbook 执行失败

#### <a name="issue"></a>问题

Runbook 执行失败，你收到以下错误：

```
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

runbook 在尝试执行三次后立刻暂停。 存在可能会阻止 runbook 成功完成的情况，相关错误消息未包括指示原因的任何其他信息。

#### <a name="cause"></a>原因

以下是可能的原因：

* runbook 无法使用本地资源进行身份验证

* 混合辅助角色在代理或防火墙后面

* runbook 无法使用本地资源进行身份验证

* 指定为运行混合 Runbook 辅助角色功能的计算机满足最低硬件要求。

#### <a name="resolution"></a>解决方法

确保计算机在端口 443 上对 *.azure-automation.net 有出站访问权限。

运行混合 Runbook 辅助角色的计算机应满足最低硬件要求，才能指定它托管此功能。 否则，在执行过程中，根据其他后台进程的资源使用率和 runbook 所导致的争用，该计算机将变为过度使用，从而导致 runbook 作业延迟或超时。

确认指定为运行混合 Runbook 辅助角色功能的计算机满足最低硬件要求。 如果满足，请监视 CPU 和内存利用率，以确定混合 Runbook 辅助角色进程的性能和 Windows 之间的任何关联。 如果存在内存或 CPU 压力，这可能指示需要升级或添加额外的处理器或增加内存来解决资源瓶颈问题，从而解决此错误。 或者，选择其他可支持最低要求的计算资源，并在工作负荷需求指示需要增加时进行扩展。

检查 **Microsoft-SMA** 事件日志中是否有描述为 Win32 Process Exited with code [4294967295] 的相应事件。 此错误的原因是尚未在 runbook 中配置身份验证，或者未为混合辅助角色组指定运行方式凭据。 请查看 [Runbook 权限](../automation-hrw-run-runbooks.md#runbook-permissions)，确认已正确为 runbook 配置身份验证。

## <a name="linux"></a>Linux

Linux 混合 Runbook 辅助角色依靠适用于 Linux 的 OMS 代理与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

###<a name="oms-agent-not-running"></a>方案：适用于 Linux 的 OMS 代理未运行

如果适用于 Linux 的 OMS 代理未运行，这会导致 Linux 混合 Runbook 辅助角色无法与 Azure 自动化通信。 输入以下命令，验证代理是否正在运行：`ps -ef | grep python`。 你应该看到类似如下的输出，即使用 **nxautomation** 用户帐户的 python 进程。 如果未启用更新管理或 Azure 自动化解决方案，则以下任何进程都不会运行。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

以下列表显示针对 Linux 混合 Runbook 辅助角色启动的进程。 它们都位于 `/var/opt/microsoft/omsagent/state/automationworker/` 目录中。

* **oms.conf**：这是辅助角色管理器进程，直接从 DSC 启动。

* **worker.conf**：此进程是自动注册混合辅助角色进程，由辅助角色管理器启动。 此进程由更新管理使用且对用户而言是透明的。 如果未在计算机上启用更新管理解决方案，则不会显示此进程。

* **diy/worker.conf**：此进程是 DIY 混合辅助角色进程。 DIY 混合辅助角色进程用于执行混合 Runbook 辅助角色的用户 Runbook。 它仅与使用不同配置的自动注册混合辅助角色进程在主要细节上有所不同。 如果未启用 Azure 自动化解决方案，并且 DIY Linux 混合辅助角色未注册，则不会显示此进程。

如果适用于 Linux 的 OMS 代理未运行，请运行以下命令启动该服务：`sudo /opt/microsoft/omsagent/bin/service_control restart`。

###<a name="class-does-not-exist"></a>方案：指定的类不存在

如果看到错误“指定的类不存在。” （在 `/var/opt/microsoft/omsconfig/omsconfig.log` 中），则需要更新适用于 Linux 的 OMS 代理。 运行以下命令重新安装 OMS 代理：

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows 混合 Runbook 辅助角色依靠 Microsoft Monitoring Agent 与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

###<a name="mma-not-running"></a>方案：Microsoft Monitoring Agent 未运行

#### <a name="issue"></a>问题

`healthservice` 服务不在混合 Runbook 辅助角色计算机上运行。

#### <a name="cause"></a>原因

如果 Microsoft Monitoring Agent Windows 服务未运行，会导致混合 Runbook 辅助角色无法与 Azure 自动化通信。

#### <a name="resolution"></a>解决方法

在 PowerShell 中输入以下命令，验证代理是否正在运行：`Get-Service healthservice`。 如果该服务已停止，请在 PowerShell 中输入以下命令启动该服务：`Start-Service healthservice`。

###<a name="event-4502"></a> Operations Manager 日志中的事件 4502

#### <a name="issue"></a>问题

在 Application and Services Logs\Operations Manager 事件日志中，可看到事件 4502、包含 Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent 的 EventMessage 以及下列描述：服务 \<wsid\>.oms.opinsights.azure.com 提供的证书不是由 Microsoft 服务使用的证书颁发机构颁发的 *。请联系网络管理员以查看其是否正在运行截获 TLS/SSL 通信的代理。KB3126513 一文还介绍了关于连接问题的其他故障排除信息。*

#### <a name="cause"></a>原因

这可能是因为代理或网络防火墙阻止与 Microsoft Azure 通信。 确保计算机在端口 443 上对 *.azure-automation.net 有出站访问权限。

#### <a name="resolution"></a>解决方法

日志存储在每个混合辅助角色本地的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。 可以检查 **Application and Services Logs\Microsoft-SMA\Operations** 和 **Application and Services Logs\Operations Manager** 事件日志中是否写入了任何警告或错误事件，指示出现了影响角色载入 Azure 自动化的连接问题或其他问题，或者在执行正常操作时出现问题。

[Runbook 输出和消息](../automation-runbook-output-and-messages.md)将从混合辅助角色发送到 Azure 自动化，就像云中运行的 Runbook 作业一样。 就像在其他 Runbook 中一样，还可以启用详细流和进度流。

## <a name="next-steps"></a>后续步骤

如果未看到问题或者不能解决问题，请访问以下通道获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如果需要更多帮助，可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。

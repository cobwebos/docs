---
title: 排查更新管理问题
description: 了解如何排查更新管理问题
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b77d1210ff48a4bd30834fcbad64173bf77b1290
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064286"
---
# <a name="troubleshooting-issues-with-update-management"></a>排查更新管理问题

本文讨论用于解决使用更新管理时可能遇到的问题的解决方案

## <a name="windows"></a>Windows

如果在尝试在虚拟机上载入解决方案时遇到问题，请查看本地计算机“应用程序和服务日志”下的“Operations Manager”事件日志中是否存在事件 ID 为 4502、事件消息包含 Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent 的事件。

下一部分突出显示了特定的错误消息，以及每个消息的可能解决方案。 有关其他载入问题，请参阅[排查解决方案载入问题](onboarding.md)。

### <a name="machine-already-registered"></a>方案：计算机已注册到其他帐户

#### <a name="issue"></a>问题

看到以下错误消息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>原因

计算机已载入到其他进行更新管理的工作区。

#### <a name="resolution"></a>解决方法

通过[删除混合 runbook 组](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)对计算机上的旧项目进行清理，然后重试。

### <a name="machine-unable-to-communicate"></a>方案：计算机无法与服务进行通信

#### <a name="issue"></a>问题

收到以下错误消息之一：

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>原因

可能是因为代理、网关或防火墙阻止了网络通信。

#### <a name="resolution"></a>解决方法

检查网络并确保允许适当的端口和地址。 有关更新管理和混合 Runbook 辅助角色所需的端口和地址列表，请参阅[网络要求](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="unable-to-create-selfsigned-cert"></a>方案：无法创建自签名证书

#### <a name="issue"></a>问题

收到以下错误消息之一：

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>原因

混合 Runbook 辅助角色无法生成自签名证书

#### <a name="resolution"></a>解决方法

请验证系统帐户是否具有对文件夹 C:\ProgramData\Microsoft\Crypto\RSA 的读取权限，然后重试。

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>方案：无法启动更新运行

#### <a name="issue"></a>问题

无法在 Linux 计算机上启动更新运行。

#### <a name="cause"></a>原因

Linux 混合辅助角色运行状况不正常。

#### <a name="resolution"></a>解决方法

请创建以下日志文件的副本并保留它以用于故障排除：

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>方案：启动更新运行后遇到错误

#### <a name="issue"></a>问题

启动更新运行后，在运行期间遇到错误。

#### <a name="cause"></a>原因

可能的原因包括：

* 包管理器运行状况不正常
* 特定包可能干扰了基于云的修补
* 其他原因

#### <a name="resolution"></a>解决方法

如果更新运行在 Linux 上成功启动后又失败，请检查运行中受影响的计算机的作业输出。 可以从计算机的包管理器查找特定的错误消息，可以对这些错误消息进行调查并对其采取操作。 更新管理要求包管理器正常运行才能成功进行更新部署。

某些情况下，包更新可能会干扰更新管理，因而会阻更新部署完成。 如果出现此情况，则必须从将来的更新运行中排除这些包，或者手动安装它们。

如果无法解决修补问题，请在下次更新部署启动之前创建以下日志文件的副本，并保留它以用于故障排除：

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>后续步骤

如果未看到问题或者不能解决问题，请访问以下通道获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如果需要更多帮助，可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
---
title: 排查加入更新管理、更改跟踪和库存时发生的错误
description: 了解如何排查更新管理、更改跟踪和库存解决方案的加入错误
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 044cb56b8991a1eb2dd6a1d35be621f2ffab3250
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064215"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>排查加入解决方案时发生的错误

在加入更新管理、更改跟踪或库存等解决方案时，可能会遇到错误。 本文描述可能会发生的各种错误及其解决方法。

## <a name="general-errors"></a>常规错误

### <a name="computer-grou-query-format-error"></a>场景：ComputerGroupQueryFormatError

#### <a name="issue"></a>问题

此错误代码表示，用来将此解决方案作为目标的已保存搜索计算机组查询没有正确设置格式。 

#### <a name="cause"></a>原因

你可能已更改了该查询，或者系统可能已更改了该查询。

#### <a name="resolution"></a>解决方法

可以删除对此解决方案的查询，重新载入解决方案，这会重新创建查询。 可以在你的工作区内找到此查询，它位于“保存的搜索”下。 查询名称是 **MicrosoftDefaultComputerGroup**，查询类别是与此查询关联的解决方案的名称。 如果启用了多个解决方案，则 **MicrosoftDefaultComputerGroup** 会在“保存的搜索”下显示多次。

### <a name="policy-violation"></a>场景：PolicyViolation

#### <a name="issue"></a>问题

此错误代码表示部署由于违反一个或多个策略而失败。

#### <a name="cause"></a>原因 

实施的某个策略正在阻止操作完成。

#### <a name="resolution"></a>解决方法

为了成功部署解决方案，需要考虑更改指示的策略。 由于可以定义许多不同类型的策略，因此所需的特定更改取决于所违反的策略。 例如，如果在某个资源组上定义了一个拒绝更改该资源组中某些类型的资源内容的权限的策略，则可以（例如）执行以下任一操作：

* 将该策略完全删除。
* 尝试加入到不同的资源组。
* 通过以下方式修改策略，例如：
  * 将策略重定位到特定资源（例如，特定自动化帐户）。
  * 修改该策略已配置为拒绝的资源集。

检查 Azure 门户右上角的通知或导航到包含自动化帐户的资源组，然后选择“设置”下的“部署”以查看失败部署。 若要了解有关 Azure 策略的详细信息，请访问：[Azure 策略概述](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json)。

## <a name="mma-extension-failures"></a>MMA 扩展失败

部署解决方案时，会部署各种相关资源。 其中的一个资源是 Microsoft Monitoring Agent 扩展或适用于 Linux 的 OMS 代理。 这些虚拟机扩展由虚拟机的来宾代理安装，该代理负责与配置的 Operations Management Suite (OMS) 工作区通信，目的是在所要加入的解决方案开始执行后，协调该解决方案所依赖的二进制文件和其他文件的下载。
通常，通知中心显示的通知首先会指出发生了 MMA 或适用于 Linux 的 OMS 代理安装失败。 单击该通知可以获取有关特定失败问题的更多信息。 依次导航到“资源组”资源和“部署”元素，其中也提供了发生的部署失败问题的详细信息。
MMA 或适用于 Linux 的 OMS 代理安装可能会出于各种原因而失败，这些失败问题的解决步骤根据问题的不同而异。 具体的故障排除步骤如下。

以下部分描述了在加入过程中可能会遇到的、导致 MMA 扩展部署失败的各种问题。

### <a name="webclient-exception"></a>场景：在 WebClient 请求期间发生异常

虚拟机上的 MMA 扩展无法与外部资源通信，并且部署失败。

#### <a name="issue"></a>问题

下面是返回的错误消息示例：

```
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>原因

此错误的部分潜在原因包括：

* VM 中配置了一个只允许使用特定端口的代理。

* 某个防火墙设置已阻止访问所需的端口和地址。

#### <a name="resolution"></a>解决方法

确保已打开正确的端口和地址用于通信。 有关端口和地址的列表，请参阅[规划网络](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="transient-environment-issue"></a>场景：暂时性的环境问题导致安装失败

在部署期间安装 Microsoft Monitoring Agent 扩展失败，因为另一项安装或操作阻止了此项安装

#### <a name="issue"></a>问题

下面是可能返回的错误消息示例：

```
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>原因

此错误的部分潜在原因包括：

* 另一项安装正在进行
* 在部署模板期间，已触发系统重新启动

#### <a name="resolution"></a>解决方法

此错误是暂时性的。 请重试部署，以安装该扩展。

### <a name="installation-timeout"></a>场景：安装超时

由于超时，MMA 扩展的安装未完成。

#### <a name="issue"></a>问题

下面是可能返回的错误消息示例：

```
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

发生此错误的原因是安装期间虚拟机承受了较重的负载。

### <a name="resolution"></a>解决方法

请尝试在 VM 的负载降低时安装 MMA 扩展。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。

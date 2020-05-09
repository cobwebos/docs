---
title: Azure Automation 中的混合 Runbook 辅助角色概述
description: 本文概述了混合 Runbook 辅助角色，它是 Azure 自动化的一项功能，可用于在本地数据中心或云提供商的计算机上运行 runbook。
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: c02a61366b05fa2750c581a1dedefb0d6239dada
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994762"
---
# <a name="hybrid-runbook-worker-overview"></a>混合 Runbook 辅助角色概述

Azure 自动化中的 Runbook 可能无权访问其他云或本地环境中的资源，因为它们在 Azure 云平台中运行。 利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一台或多台指定的计算机。

下图说明了此功能：

![混合 Runbook 辅助角色概述](media/automation-hybrid-runbook-worker/automation.png)

混合 Runbook 辅助角色可以运行 Windows 或 Linux 操作系统。 对于监视，需要将 Azure Monitor 和 Log Analytics 代理用于受支持的操作系统。 有关详细信息，请参阅 [Azure Monitor](automation-runbook-execution.md#azure-monitor)。

每个混合 Runbook 辅助角色都是你在安装代理时指定的混合 Runbook 辅助角色组的成员。 一个组可以包含一个代理，但是可以在一个组中安装多个代理，以实现高可用性。 每台计算机都可以将一个混合辅助角色报告托管到一个自动化帐户。 

在混合 Runbook 辅助角色中启动 Runbook 时，可以指定该辅助角色会在其中运行的组。 组中的每个辅助角色都会轮询 Azure 自动化以查看是否有可用作业。 如果作业可用，获取作业的第一个辅助角色将执行该作业。 作业队列的处理时间取决于混合辅助角色硬件配置文件和负载。 不能指定特定的辅助角色。 

使用混合 Runbook 辅助角色，而不是[Azure 沙盒](automation-runbook-execution.md#runbook-execution-environment)，因为它对磁盘空间、内存或网络套接字没有很多沙箱[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。 混合辅助角色的限制仅与辅助角色自己的资源相关。 

> [!NOTE]
> 混合 Runbook 辅助角色不受 Azure 沙盒具有的[公平共享](automation-runbook-execution.md#fair-share)时间限制的限制。 

## <a name="hybrid-runbook-worker-installation"></a>混合 Runbook 辅助角色安装

安装混合 Runbook 辅助角色的过程取决于操作系统。 下表定义了部署类型。

|操作系统  |部署类型  |
|---------|---------|
|Windows     | [自动](automation-windows-hrw-install.md#automated-deployment)<br>[手动](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

推荐的安装方法是使用 Azure 自动化 runbook 来完全自动完成配置 Windows 计算机的过程。 第二种方法是按照分步过程来手动安装和配置角色。 对于 Linux 计算机，运行 Python 脚本，在计算机上安装代理。

## <a name="network-planning"></a><a name="network-planning"></a>网络规划

要使混合 Runbook 辅助角色连接到 Azure 自动化并向其注册，它必须有权访问此部分中所述的端口号和 Url。 辅助角色还必须有权访问 Log Analytics 代理连接到 Azure Monitor Log Analytics 工作区[所需的端口和 url](../azure-monitor/platform/agent-windows.md) 。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

混合 Runbook 辅助角色需要以下端口和 Url：

* 端口：仅适用于出站 internet 访问所需的 TCP 443
* 全局 URL：*.azure-automation.net
* US Gov 弗吉尼亚州的全局 URL：*.azure-automation.us
* 代理服务：https://\<workspaceId\>.agentsvc.azure-automation.net

建议在定义[异常](automation-runbook-execution.md#exceptions)时使用列出的地址。 对于 IP 地址，你可以下载[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。 此文件每周更新，包含当前部署的范围以及即将对 IP 范围进行的更新。

### <a name="dns-records-per-region"></a>每个区域的 DNS 记录

如果已为特定区域定义了自动化帐户，则可以将混合 Runbook 辅助角色通信限制到该区域数据中心。 下表提供了每个区域的 DNS 记录。

| **区域** | **DNS 记录** |
| --- | --- |
| 澳大利亚中部 |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| 澳大利亚东部 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| 澳大利亚东南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 美国东部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 日本东部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 北欧 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 美国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 东南亚 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 弗吉尼亚州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| 美国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 西欧 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 美国西部 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

有关区域 IP 地址列表（非区域名称列表），请从 Microsoft 下载中心下载 [Azure 数据中心 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653) XML 文件。 更新的 IP 地址文件每周发布一次。 

IP 地址文件列出了 Microsoft Azure 数据中心使用的 IP 地址范围。 它包括计算、SQL 和存储范围，并反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。

最好是每周下载新的 IP 地址文件。 然后，更新网站以正确地标识 Azure 中运行的服务。 

> [!NOTE]
> 如果使用的是 Azure ExpressRoute，请记住，在每个月的第一周，IP 地址文件用于更新 Azure 空间的边界网关协议（BGP）播发。

### <a name="proxy-server-use"></a>代理服务器使用

如果使用代理服务器在 Azure 自动化与 Log Analytics 代理之间进行通信，请确保可以访问相应的资源。 混合 Runbook 辅助角色和自动化服务发出的请求超时为30秒。 三次尝试后，请求会失败。 

### <a name="firewall-use"></a>防火墙使用

如果使用防火墙来限制对 internet 的访问，则必须将防火墙配置为允许访问。 如果使用 Log Analytics 网关作为代理，请确保将其配置为混合 Runbook 辅助角色。 请参阅[配置自动化混合辅助角色的 Log Analytics 网关](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)。

## <a name="update-management-on-hybrid-runbook-worker"></a>混合 Runbook 辅助角色上的更新管理

启用 Azure 自动化[更新管理](automation-update-management.md)后，任何连接到 Log Analytics 工作区的计算机都会自动配置为混合 Runbook 辅助角色。 每个工作线程都可以支持以更新管理为目标的 runbook。 

以这种方式配置的计算机未注册到已在自动化帐户中定义的任何混合 Runbook 辅助角色组。 你可以将计算机添加到混合 Runbook 辅助角色组，但必须同时为更新管理和混合 Runbook 辅助角色组成员身份使用同一帐户。 此功能已添加到混合 Runbook 辅助角色的版本7.2.12024.0。

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>混合 Runbook 辅助角色的更新管理地址

在混合 Runbook 辅助角色所需的标准地址和端口之上，更新管理需要下表中的地址。 与这些地址的通信使用端口443。

|Azure Public  |Azure Government   |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\* .blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="state-configuration-dsc-on-hybrid-runbook-worker"></a>混合 Runbook 辅助角色上的状态配置（DSC）

可以在混合 Runbook 辅助角色上运行[状态配置（DSC）](automation-dsc-overview.md)功能。 若要管理支持混合 Runbook 辅助角色的服务器配置，必须将这些服务器添加为 DSC 节点。 有关载入的详细信息，请参阅[板载计算机，按状态配置（DSC）进行管理](automation-dsc-onboarding.md)。

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>混合 Runbook 辅助角色上的 runbook

你的 runbook 可能会管理本地计算机上的资源，或针对部署了混合 Runbook 辅助角色的本地环境中的资源运行。 在这种情况下，可以选择在混合辅助角色而不是自动化帐户中运行 runbook。 在混合 Runbook 辅助角色上运行的 runbook 在结构上与在自动化帐户中运行的 runbook 相同。 请参阅[在混合 Runbook 辅助角色上运行 runbook](automation-hrw-run-runbooks.md)。

### <a name="hybrid-runbook-worker-jobs"></a>混合 Runbook 辅助角色作业

混合 Runbook 辅助角色作业在 Windows 上的本地**系统**帐户或 Linux 上的[nxautomation 帐户](automation-runbook-execution.md#log-analytics-agent-for-linux)下运行。 Azure 自动化处理混合 Runbook 辅助角色上的作业，不同于 Azure 沙箱中运行的作业。 请参阅[Runbook 执行环境](automation-runbook-execution.md#runbook-execution-environment)。

如果混合 Runbook 辅助角色主机重新启动，则任何正在运行的 runbook 作业将从开始重新启动，或者从 PowerShell 工作流 runbook 的最后一个检查点重新启动。 在 runbook 作业重启三次后，它会被挂起。

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>混合 Runbook 辅助角色的 Runbook 权限

由于它们访问非 Azure 资源，在混合 Runbook 辅助角色上运行的 runbook 不能使用通常由对 Azure 资源进行身份验证的 runbook 所使用的身份验证机制。 Runbook 提供自己的本地资源身份验证，或使用[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)配置身份验证。 还可以指定运行方式帐户，为所有 runbook 提供用户上下文。

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。
* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[混合 Runbook 辅助角色故障排除](troubleshoot/hybrid-runbook-worker.md#general)。
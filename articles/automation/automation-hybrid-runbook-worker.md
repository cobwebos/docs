---
title: Azure 自动化混合 Runbook 辅助角色概述
description: 本文概述了混合 Runbook 辅助角色，可以使用这些辅助角色在本地数据中心或云提供商的计算机上运行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 09/14/2020
ms.topic: conceptual
ms.openlocfilehash: f5dc9305df8ce0e26e13738d605849fa75cc53a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087876"
---
# <a name="hybrid-runbook-worker-overview"></a>混合 Runbook 辅助角色概述

Azure 自动化中的 Runbook 可能无权访问其他云或本地环境中的资源，因为它们在 Azure 云平台中运行。 利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一台或多台指定的计算机。

下图说明了此功能：

![混合 Runbook 辅助角色概述](media/automation-hybrid-runbook-worker/automation.png)

混合 Runbook 辅助角色可以在 Windows 或 Linux 操作系统上运行。 这取决于向 Azure Monitor [Log Analytics 工作区](../azure-monitor/platform/design-logs-deployment.md)报告的 [Log Analytics 代理](../azure-monitor/platform/log-analytics-agent.md)。 工作区不仅用于监视计算机是否运行支持的操作系统，还可以用于下载混合 Runbook 辅助角色所需的组件。

每个混合 Runbook 辅助角色都是你在安装代理时指定的混合 Runbook 辅助角色组的成员。 一个组可以包含一个代理，但是可以在一个组中安装多个代理，以实现高可用性。 每台计算机都可以将一个混合辅助角色报告托管到一个自动化帐户。

在混合 Runbook 辅助角色中启动 Runbook 时，可以指定该辅助角色会在其中运行的组。 组中的每个辅助角色都会轮询 Azure 自动化以查看是否有可用作业。 如果作业可用，获取作业的第一个辅助角色将执行该作业。 作业队列的处理时间取决于混合辅助角色硬件配置文件和负载。 不能指定特定的辅助角色。

使用混合 Runbook 辅助角色（而不是 [Azure 沙箱](automation-runbook-execution.md#runbook-execution-environment)），因为它对磁盘空间、内存或网络套接字没有许多沙盒[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。 对混合辅助角色的限制仅与辅助角色自己的资源相关。

> [!NOTE]
> 混合 Runbook 辅助角色不受[公平份额](automation-runbook-execution.md#fair-share)时间限制，而 Azure 沙盒受限于此限制。

## <a name="hybrid-runbook-worker-installation"></a>混合 Runbook 辅助角色安装

安装混合 Runbook 辅助角色的过程取决于操作系统。 下表定义了部署类型。

|操作系统  |部署类型  |
|---------|---------|
|Windows     | [自动](automation-windows-hrw-install.md#automated-deployment)<br>[手动](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

建议的安装方法是使用 Azure 自动化 Runbook 来完全自动执行 Windows 计算机的配置过程。 如果这种方法不可行，你可以执行分步过程来手动安装和配置角色。 对于 Linux 计算机，运行 Python 脚本，在计算机上安装代理。

## <a name="network-planning"></a><a name="network-planning"></a>网络规划

要使混合 Runbook 辅助角色连接并注册到 Azure 自动化，必须让其有权访问此部分所述的端口号和 URL。 辅助角色还必须有权访问 [Log Analytics 代理所需的端口和 URL](../azure-monitor/platform/agent-windows.md) 才能连接到 Azure Monitor Log Analytics 工作区。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

混合 Runbook 辅助角色需要以下端口和 URL：

* 端口：只需使用 TCP 443 即可进行出站 Internet 访问
* 全局 URL：`*.azure-automation.net`
* US Gov 弗吉尼亚州的全局 URL： `*.azure-automation.us`
* 代理服务：`https://<workspaceId>.agentsvc.azure-automation.net`

如果为特定的区域定义了自动化帐户，则可以限制与该区域数据中心之间的混合 Runbook 辅助角色通信。 查看 [Azure Automation 用于](how-to/automation-region-dns-records.md) 所需 dns 记录的 dns 记录。

### <a name="proxy-server-use"></a>使用代理服务器

如果使用代理服务器在 Azure 自动化与运行 Log Analytics 代理的计算机之间通信，请确保能够访问相应的资源。 来自混合 Runbook 辅助角色和自动化服务的请求的超时为 30 秒。 三次尝试后，请求失败。

### <a name="firewall-use"></a>防火墙使用

如果使用防火墙来限制对 Internet 的访问，则必须将防火墙配置为允许访问。 如果将 Log Analytics 网关用作代理，请确保为混合 Runbook 辅助角色配置 Log Analytics 网关。 请参阅 [配置自动化混合 Runbook 辅助角色的 Log Analytics 网关](../azure-monitor/platform/gateway.md)。

### <a name="service-tags"></a>服务标记

Azure 自动化从服务标记 [GuestAndHybridManagement](../virtual-network/service-tags-overview.md) 开始支持 Azure 虚拟网络服务标记。 可以在[网络安全组](../virtual-network/security-overview.md#security-rules)或 [Azure 防火墙](../firewall/service-tags.md)中使用服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 GuestAndHybridManagement），可以允许或拒绝自动化服务的流量。 此服务标记不支持通过将 IP 范围限制到特定区域来实现更精细的控制。

Azure 自动化服务的服务标记仅提供用于以下场景的 IP：

* 从虚拟网络内触发 Webhook
* 允许混合 Runbook 辅助角色或 VNet 中的 State Configuration 代理与自动化服务通信

>[!NOTE]
>服务标记 GuestAndHybridManagement 当前不支持在 Azure 沙盒中执行 runbook 作业，仅允许直接在混合 Runbook 辅助角色中执行。

## <a name="support-for-impact-level-5-il5"></a>对影响级别5的支持 (IL5) 

Azure Automation 混合 Runbook 辅助角色可在 Azure 政府版中使用，以支持以下两种配置中的任何一种的影响级别5工作负荷：

* [独立虚拟机](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines)。 部署时，它们会将整个物理主机用于该 VM，并提供所需的隔离级别，以支持 IL5 工作负荷。

* [Azure 专用主机](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts)，提供可承载一个或多个虚拟机的物理服务器，专用于一个 Azure 订阅。

>[!NOTE]
>通过混合 Runbook 辅助角色进行的计算隔离适用于 Azure 商业版和美国政府云。 

## <a name="update-management-on-hybrid-runbook-worker"></a>混合 Runbook 辅助角色上的更新管理

启用 Azure 自动化[更新管理](update-management/update-mgmt-overview.md)后，连接到 Log Analytics 工作区的任何计算机会自动配置为混合 Runbook 辅助角色。 每个辅助角色能够支持以更新管理为目标的 Runbook。

以这种方式配置的计算机不会注册到任何已在自动化帐户中定义的混合 Runbook 辅助角色组。 可将计算机添加到混合 Runbook 辅助角色组，但必须对更新管理和混合 Runbook 辅助角色组成员身份使用同一帐户。 此功能已添加到 7.2.12024.0 版本的混合 Runbook 辅助角色。

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>混合 Runbook 辅助角色的更新管理地址

除了混合 Runbook 辅助角色所需的标准地址和端口之外，更新管理还具有[网络规划](update-management/update-mgmt-overview.md#ports)部分下所述的其他网络配置要求。

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>混合 Runbook 辅助角色上的 Azure Automation State Configuration

可以在混合 Runbook 辅助角色上运行 [Azure Automation State Configuration](automation-dsc-overview.md)。 若要管理支持混合 Runbook 辅助角色的服务器的配置，必须将这些服务器添加为 DSC 节点。 请参阅[启用要通过 Azure Automation State Configuration 进行管理的计算机](automation-dsc-onboarding.md)。

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>混合 Runbook 辅助角色上的 Runbook

可以使用 Runbook 来管理本地计算机上的资源，或者针对部署了混合 Runbook 辅助角色的本地环境中的资源运行 Runbook。 在这种情况下，可以选择在混合辅助角色上（而不是在自动化帐户中）运行 Runbook。 在混合 Runbook 辅助角色上运行的 Runbook 与在自动化帐户中运行的 Runbook 具有相同的结构。 请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

### <a name="hybrid-runbook-worker-jobs"></a>混合 Runbook 辅助角色作业

混合 Runbook 辅助角色作业在 Windows 上的本地系统帐户下运行，或在 Linux 上的 [nxautomation 帐户](automation-runbook-execution.md#log-analytics-agent-for-linux)下运行。 Azure 自动化处理混合 Runbook 辅助角色上的作业，这与 Azure 沙箱中运行的作业稍有不同。 请参阅[Runbook 执行环境](automation-runbook-execution.md#runbook-execution-environment)。

如果混合 Runbook 辅助角色托管计算机重新启动，则任何正在运行的 Runbook 将从头重启，或者从 PowerShell 工作流 Runbook 的最后一个检查点重启。 如果某个 Runbook 作业重启了 3 次以上，则它会暂停。

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>混合 Runbook 辅助角色的 Runbook 权限

由于它们访问的是非 Azure 资源，因此在混合 Runbook 辅助角色上运行的 Runbook 不能使用通常用于针对 Azure 资源进行 Runbook 身份验证的身份验证机制。 Runbook 可以针对本地资源提供其自己的身份验证，也可以配置使用 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)的身份验证。 还可以指定运行方式帐户，为所有 Runbook 提供用户上下文。

## <a name="view-hybrid-runbook-workers"></a>查看混合 Runbook 辅助角色

在 Windows 服务器或 Vm 上启用更新管理功能后，可以列出 Azure 门户中系统混合 Runbook 辅助角色组的列表。 通过从所选自动化帐户的左侧窗格中选择 "混合辅助角色" 组中的 "**混合辅助****角色"** 组，可以在门户中查看最多2000个工作线程。

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="自动化帐户系统混合辅助角色组页" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

如果混合辅助角色超过2000个，若要获取所有这些辅助角色的列表，可以运行以下 PowerShell 脚本：

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[排查混合 Runbook 辅助角色问题](troubleshoot/hybrid-runbook-worker.md#general)。

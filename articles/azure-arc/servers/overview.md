---
title: Azure Arc for servers（预览版）概述
description: 了解如何使用 Azure Arc for servers 管理托管在 Azure 外部的计算机，就如同管理 Azure 资源一样。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: b0f1d235391c4c4e3804a6dccc8174e946035b6a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899205"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>什么是 Azure Arc for servers（预览版）

使用 Azure Arc for servers（预览版）可以管理企业网络或其他云提供商中的、托管在 Azure 外部的 Windows 和 Linux 计算机，就如同管理本地 Azure 虚拟机一样。 当混合计算机连接到 Azure 时，它将成为一台联网计算机，被视为 Azure 中的资源。 每台联网计算机都有一个资源 ID，作为订阅内资源组的一部分进行管理，并可受益于标准的 Azure 构造，例如 Azure Policy 和应用标记。

若要为托管在 Azure 外部的混合计算机提供此体验，需要在打算连接到 Azure 的每台计算机上安装 Azure Connected Machine 代理。 此代理不提供任何其他功能，而且不会取代 Azure [Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 若要主动监视计算机上运行的 OS 和工作负荷、使用自动化 Runbook 或更新管理等解决方案对其进行管理，或使用其他 Azure 服务（例如 [Azure 安全中心](../../security-center/security-center-intro.md)），需要安装适用于 Windows 和 Linux 的 Log Analytics 代理。

>[!NOTE]
>此预览版用于评估目的，我们建议不要用它来管理关键的生产计算机。
>

## <a name="supported-scenarios"></a>支持的方案

Azure Arc for servers（预览版）支持以下使用联网计算机的方案：

- 使用与适用于 Azure 虚拟机的策略分配相同的体验，分配 [Azure Policy 来宾配置](../../governance/policy/concepts/guest-configuration.md)。
- Log Analytics 代理收集的并存储在计算机所注册到的 Log Analytics 工作区中的日志数据现在包含特定于计算机的属性，例如，可用于支持[资源上下文](../../azure-monitor/platform/design-logs-deployment.md#access-mode)日志访问的资源 ID。

## <a name="supported-regions"></a>支持的区域

使用 Azure Arc for servers（预览版）时，仅支持特定的区域：

- 美国西部 2
- 西欧
- 西亚

## <a name="prerequisites"></a>必备条件

### <a name="supported-operating-systems"></a>支持的操作系统

Azure Connected Machine 代理正式支持以下版本的 Windows 和 Linux 操作系统： 

- Windows Server 2012 R2 和更高版本
- Ubuntu 16.04 和 18.04

>[!NOTE]
>适用于 Windows 的 Connected Machine 代理预览版仅支持配置为使用英语的 Windows Server。
>

### <a name="azure-subscription-and-service-limits"></a>Azure 订阅和服务限制

在为计算机配置 Azure Arc for servers（预览版）之前，应查看 Azure 资源管理器[订阅限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)和[资源组限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)，以规划要连接的计算机数。

### <a name="networking-configuration"></a>网络配置

适用于 Linux 和 Windows 的 Connected Machine 代理通过 TCP 端口 443 安全地与 Azure Arc 进行出站通信。 如果计算机通过防火墙或代理服务器建立连接以通过 Internet 进行通信，请查看下文了解网络配置要求。

如果防火墙或代理服务器限制了出站连接，请确保不要阻止下面列出的 URL。 如果只允许代理与服务进行通信所需的 IP 范围或域名，则还必须允许访问以下服务标记和 URL。

服务标记：

- AzureActiveDirectory
- AzureTrafficManager

URL：

| 代理资源 | 说明 |
|---------|---------|
|management.azure.com|Azure 资源管理器|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|来宾配置|
|*-agentservice-prod-1.azure-automation.net|来宾配置|
|*.his.hybridcompute.azure-automation.net|混合标识服务|

有关每个服务标记/区域的 IP 地址列表，请参阅 JSON 文件 - [Azure IP 范围和服务标记 - 公有云](https://www.microsoft.com/download/details.aspx?id=56519)。 Microsoft 每周将发布包含每个 Azure 服务及其使用的 IP 范围的更新。 有关详细信息，请查看[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)。

除了上表中列出的 URL 以外，还需要服务标记 IP 地址范围信息，因为大多数服务当前没有服务标记注册。 因此，IP 地址可能会变化。 如果防火墙配置需要 IP 地址范围，则应使用 **AzureCloud** 服务标记允许对所有 Azure 服务的访问。 请勿禁用对这些 URL 的安全监视或检查，但就像允许其他 Internet 流量一样允许这些 URL。

### <a name="register-azure-resource-providers"></a>注册 Azure 资源提供程序

Azure Arc for servers（预览版）依赖于通过订阅中的以下 Azure 资源提供程序来使用此服务：

-  Microsoft.HybridCompute
-  Microsoft.GuestConfiguration

如果未注册这些提供程序，可使用以下命令注册：

Azure PowerShell：

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI：

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

此外，还可以按照 [Azure 门户](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)下的步骤，在 Azure 门户中注册资源提供程序。

## <a name="connected-machine-agent"></a>Connected Machine 代理

可从以下位置下载适用于 Windows 和 Linux 的 Azure Connected Machine 代理包。

- Microsoft 下载中心提供的 [Windows 代理 Windows Installer 包](https://aka.ms/AzureConnectedMachineAgent)。
- Linux 代理包通过 Microsoft 的[包存储库](https://packages.microsoft.com/)使用分发版的首选包格式（.RPM 或 .DEB）进行分发。

>[!NOTE]
>在预览期，仅发布了一个适用于 Ubuntu 16.04 或 18.04 的包。

## <a name="install-and-configure-agent"></a>安装并配置代理

可以根据要求使用不同的方法，将混合环境中的计算机直接连接到 Azure。 下表详细介绍了每种方法，让你确定最适合组织的方法。

| 方法 | 说明 |
|--------|-------------|
| 交互式 | 遵循[从 Azure 门户连接计算机](quickstart-onboard-portal.md)中的步骤，在一台或多台计算机上手动安装代理。<br> 在 Azure 门户中，可以生成一个脚本并在计算机上执行，以自动完成代理安装和配置的步骤。|
| 大规模 | 遵循[使用服务主体连接计算机](quickstart-onboard-powershell.md)中的步骤，为多台计算机安装并配置代理。<br> 此方法创建一个服务主体来以非交互方式连接计算机。|


## <a name="next-steps"></a>后续步骤

- 若要开始评估 Azure Arc for servers（预览版），请阅读[从 Azure 门户将混合计算机连接到 Azure](quickstart-onboard-portal.md) 一文。 
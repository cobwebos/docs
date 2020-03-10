---
title: Azure Arc for servers（预览版）概述
description: 了解如何使用 Azure Arc for servers 管理托管在 Azure 外部的计算机，就如同管理 Azure 资源一样。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid
ms.date: 02/24/2020
ms.topic: overview
ms.openlocfilehash: 57b44db9c1bb9a607ad8478b7208df40441020c2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372223"
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

在大多数情况下，创建安装脚本时选择的位置应该是在地理位置上最接近你的计算机位置的 Azure 区域。 静态数据将存储在包含你指定区域的 Azure 地理区域中，如果你有数据驻留要求，这可能也会影响你对区域的选择。 如果你的计算机连接到的 Azure 区域受中断影响，则已连接的计算机不受影响，但使用 Azure 的管理操作可能无法完成。 为了在发生区域性中断时具有恢复能力，如果你有提供地理冗余服务的多个位置，最好将每个位置的计算机连接到不同的 Azure 区域。

## <a name="prerequisites"></a>先决条件

### <a name="supported-operating-systems"></a>支持的操作系统

Azure Connected Machine 代理正式支持以下版本的 Windows 和 Linux 操作系统： 

- Windows Server 2012 R2 及更高版本（包括 Windows Server Core）
- Ubuntu 16.04 和 18.04
- CentOS Linux 7
- SUSE Linux Enterprise Server （SLES）15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 7

>[!NOTE]
>适用于 Windows 的 Connected Machine 代理预览版仅支持配置为使用英语的 Windows Server。
>

### <a name="required-permissions"></a>所需的权限

- 若要将计算机加入，你必须是 **Azure Connected Machine 加入**角色的成员。

- 若要读取、修改、重新加入和删除计算机，你必须是 **Azure Connected Machine 资源管理员**角色的成员。 

### <a name="azure-subscription-and-service-limits"></a>Azure 订阅和服务限制

在为计算机配置 Azure Arc for servers（预览版）之前，应查看 Azure 资源管理器[订阅限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)和[资源组限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)，以规划要连接的计算机数。

## <a name="tls-12-protocol"></a>TLS 1.2 协议

为了确保数据传输到 Azure 的安全性，我们强烈建议你将计算机配置为使用传输层安全性（TLS）1.2。 我们发现旧版 TLS/安全套接字层 (SSL) 容易受到攻击，尽管目前出于向后兼容，这些协议仍可正常工作，但我们**不建议使用**。 

|平台/语言 | 支持 | 更多信息 |
| --- | --- | --- |
|Linux | Linux 分发版往往依赖于 [OpenSSL](https://www.openssl.org) 来提供 TLS 1.2 支持。 | 请检查 [OpenSSL 变更日志](https://www.openssl.org/news/changelog.html)，确认你的 OpenSSL 版本是否受支持。|
| Windows Server 2012 R2 和更高版本 | 受支持，并且默认已启用。 | 确认是否仍在使用[默认设置](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)。|

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

- Microsoft.HybridCompute
- Microsoft.GuestConfiguration

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

你可以从下列位置下载适用于 Windows 和 Linux 的 Azure 连接计算机代理包。

- Microsoft 下载中心提供的 [Windows 代理 Windows Installer 包](https://aka.ms/AzureConnectedMachineAgent)。
- Linux 代理包通过 Microsoft 的[包存储库](https://packages.microsoft.com/)使用分发版的首选包格式（.RPM 或 .DEB）进行分发。

>[!NOTE]
>在预览期，仅发布了一个适用于 Ubuntu 16.04 或 18.04 的包。

适用于 Windows 和 Linux 的 Azure 连接的计算机代理可以手动或自动升级到最新版本，具体取决于你的要求。 对于 Windows，可以使用[apt](https://help.ubuntu.com/lts/serverguide/apt.html)命令行工具，使用 Windows 更新和 Ubuntu 自动完成代理更新。

### <a name="agent-status"></a>代理状态

连接的计算机代理每5分钟向服务发送一条定期检测信号消息。 如果15分钟内未收到一台计算机，则会将该计算机视为脱机，并且状态将自动更改为在门户中**断开连接**。 收到来自连接的计算机代理的后续检测信号消息后，其状态将自动更改为 "**已连接**"。

## <a name="install-and-configure-agent"></a>安装并配置代理

可以根据要求使用不同的方法，将混合环境中的计算机直接连接到 Azure。 下表详细介绍了每种方法，让你确定最适合组织的方法。

| 方法 | 说明 |
|--------|-------------|
| 交互式 | 遵循[从 Azure 门户连接计算机](onboard-portal.md)中的步骤，在一台或多台计算机上手动安装代理。<br> 在 Azure 门户中，可以生成一个脚本并在计算机上执行，以自动完成代理安装和配置的步骤。|
| 大规模 | 遵循[使用服务主体连接计算机](onboard-service-principal.md)中的步骤，为多台计算机安装并配置代理。<br> 此方法创建一个服务主体来以非交互方式连接计算机。|

## <a name="next-steps"></a>后续步骤

- 若要开始评估 Azure Arc for servers（预览版），请阅读[从 Azure 门户将混合计算机连接到 Azure](onboard-portal.md) 一文。 
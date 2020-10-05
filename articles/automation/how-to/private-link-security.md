---
title: 使用 Azure 专用链接将网络安全连接到 Azure 自动化
description: 使用 Azure 专用链接将网络安全连接到 Azure 自动化
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 07/09/2020
ms.subservice: ''
ms.openlocfilehash: a4985784a17f2e0350a7b2c7a4f62f574862d50c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714355"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation-preview"></a>使用 Azure 专用链接将网络安全地连接到 Azure 自动化 (预览) 

Azure 专用终结点是一个网络接口，可以将你通过专用且安全的方式连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址，从而有效地将自动化服务引入 VNet。 VNet 上的计算机与自动化帐户之间的网络流量通过 VNet 和 Microsoft 主干网络上的专用链接进行遍历，从而消除了公共 internet 的泄露。

例如，你有一个 VNet，你已禁用出站 internet 访问。 但是，你需要私下访问自动化帐户，并使用混合 Runbook 辅助角色上的自动化功能，如 Webhook、状态配置和 runbook 作业。 而且，您希望用户只能通过 VNET 访问自动化帐户。  部署专用终结点实现了这些目标。

本文介绍了何时使用以及如何使用自动化帐户设置专用终结点， (预览) 。

![Azure Automation 专用链接的概念性概述](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> 仅在 Azure 商业版和 Azure 美国政府云中提供 Azure 自动化 (预览) 的私有链接支持。

## <a name="advantages"></a>优势

借助专用链接，你可以：

- 私下连接到 Azure 自动化，而无需打开任何公共网络访问。
- 私下连接到 Azure Monitor Log Analytics 工作区，而无需打开任何公共网络访问权限。

    >[!NOTE]
    >如果你的自动化帐户已链接到 Log Analytics 工作区以便转发作业数据，并且已启用更新管理、更改跟踪和清单、状态配置或在空闲时间启动/停止 VM 等功能，则这是必需的。 有关 Azure Monitor 的专用链接的详细信息，请参阅 [使用 Azure 专用链接安全地将网络连接到 Azure Monitor](../../azure-monitor/platform/private-link-security.md)。

- 确保仅通过已授权的专用网络访问自动化数据。
- 通过定义通过专用终结点连接的 Azure 自动化资源，防止数据渗透从专用网络进行。
- 使用 ExpressRoute 和专用链接安全地将专用本地网络连接到 Azure 自动化。
- 保留 Microsoft Azure 主干网络内的所有流量。

有关详细信息，请参阅[专用链接的主要优势](../../private-link/private-link-overview.md#key-benefits)。

## <a name="how-it-works"></a>工作原理

Azure 自动化专用链接将一个或多个专用终结点连接 (，因此，它们包含在) 到自动化帐户资源的虚拟网络。 这些终结点是使用 webhook 启动 runbook、托管混合 Runbook 辅助角色的计算机以及 DSC 节点的计算机。

创建自动化的专用终结点后，可以直接联系的每个面向公众的自动化 Url 都将映射到 VNet 中的一个专用终结点。

作为预览版本的一部分，自动化帐户不能访问使用专用终结点保护的 Azure 资源。 例如，Azure Key Vault、Azure SQL、Azure 存储帐户等。

### <a name="webhook-scenario"></a>Webhook 方案

可以通过对 webhook URL 执行 POST 操作来启动 runbook。 例如，URL 如下所示： `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="state-configuration-agentsvc-scenario"></a>状态配置 (agentsvc) 方案

状态配置为你提供 Azure 配置管理服务，该服务允许你为任意云和本地数据中心内的节点 (DSC) 配置编写、管理和编译 PowerShell Desired State Configuration。

计算机上的代理使用 DSC 服务注册，然后使用该服务终结点请求 DSC 配置。 代理服务终结点如下所示： `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` 。

公用 & 专用终结点的 URL 是相同的，但是，当启用 "专用" 链接时，它将映射到专用 IP 地址。

## <a name="planning-based-on-your-network"></a>根据自身网络进行规划

设置自动化帐户资源之前，请考虑网络隔离要求。 评估你的虚拟网络对公共 internet 的访问权限以及对你的自动化帐户的访问限制 (包括设置专用链接组作用域，以便在与自动化帐户集成) 时 Azure Monitor 日志。 还包括对自动化服务 [DNS 记录](./automation-region-dns-records.md) 作为计划的一部分的检查，以确保支持的功能正常运行。

### <a name="connect-to-a-private-endpoint"></a>连接到专用终结点

创建用于连接网络的专用终结点。 你可以在 [Azure 门户专用链接中心](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)中创建它。 应用更改 publicNetworkAccess 和 private 链接后，最长可能需要35分钟才能生效。

在本部分中，你将为自动化帐户创建专用终结点。

1. 在屏幕的左上方，选择 " **创建资源" > 网络 > 专用链接中心 (预览 ") **"。

2. 在“专用链接中心 - 概述”中的“与服务建立专用连接”选项的旁边，选择“启动”。**** **** ****

3. 在 " **创建虚拟机-基础知识**" 中，输入或选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 名称 | 输入你的 *PrivateEndpoint*。 |
    | 区域 | 选择 **YourRegion**。 |
    |||

4. 在完成时选择“下一步:资源”。

5. 在 " **创建专用终结点-资源**" 中，输入或选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    |连接方法  | 选择“连接到我的目录中的 Azure 资源”。|
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择 " **automationAccounts**"。 |
    | 资源 |选择 *myAutomationAccount*|
    |目标子资源 |选择 *Webhook* 或 *DSCAndHybridWorker* ，具体取决于你的方案。|
    |||

6. 在完成时选择“下一步:配置”。

7. 在 " **创建专用终结点-配置**" 中，输入或选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    |**网络**| |
    | 虚拟网络| 选择“MyVirtualNetwork”。** |
    | 子网 | 选择“mySubnet”**。 |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |请选择“是”。  |
    |专用 DNS 区域 |选择 * (新建) privatelink.azure-automation.net* |
    |||

8. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

9. 看到“验证通过”消息时，选择“创建” 。

在 " **专用链接中心 (预览") **中，选择 " **专用终结点** " 查看专用链接资源。

![自动化资源专用链接](./media/private-link-security/private-link-automation-resource.png)

选择资源以查看所有详细信息。 这会为自动化帐户创建新的专用终结点，并为其分配虚拟网络的专用 IP。 **连接状态**显示为 "**已批准**"。

同样， (FQDN) 创建唯一的完全限定域名 (agentsvc) 的状态配置和混合 Runbook 辅助角色作业运行时 (jrds) 。 其中每个都分配有一个来自 VNet 的单独 IP，并且 **连接状态** 显示为 " **已批准**"。

如果服务使用者对自动化资源具有 Azure RBAC 权限，则他们可以选择自动批准方法。 在这种情况下，当请求到达自动化提供程序资源时，服务提供程序不需要执行任何操作，并且将自动批准连接。

## <a name="set-public-network-access-flags"></a>设置公用网络访问标志

你可以将自动化帐户配置为拒绝所有公共配置，并仅允许通过专用终结点的连接，以进一步增强网络安全。 如果你希望仅在 VNet 内限制对自动化帐户的访问，而不允许从公共 internet 进行访问，则可以将 `publicNetworkAccess` 属性设置为 `$false` 。

当 " **公用网络访问** " 设置设置为时 `$false` ，仅允许通过专用终结点进行的连接，并且通过公共终结点的所有连接被拒绝，并出现 unathorized 错误消息和 HTTP 状态401。 

下面的 PowerShell 脚本演示如何 `Get` 和 `Set` 自动化帐户级别的 **公共网络访问** 属性：

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

## <a name="dns-configuration"></a>DNS 配置

使用完全限定的域名 (FQDN) 作为连接字符串的一部分连接到专用链接资源时，必须正确配置 DNS 设置，以解析为分配的专用 IP 地址。 现有的 Azure 服务可能已有在通过公共终结点进行连接时要使用的 DNS 配置。 应查看并更新 DNS 配置，以使用专用终结点进行连接。

与专用终结点关联的网络接口包含配置 DNS 所需的完整信息，其中包括为给定专用链接资源分配的 FQDN 和专用 IP 地址。

可使用以下选项来配置专用终结点的 DNS 设置：

* 使用主机文件（仅建议用于测试） 。 你可以使用虚拟机上的主机文件来替代使用 DNS 进行名称解析。

* 使用 [专用 DNS 区域](../../dns/private-dns-privatednszone.md)。 可使用专用 DNS 区域来替代特定专用终结点的 DNS 解析。 可将专用 DNS 区域链接到虚拟网络，以解析特定的域。 若要使虚拟机上的代理能够通过专用终结点进行通信，请创建一个专用 DNS 记录作为 `privatelink.azure-automation.net` 。 将新的 DNS *a* 记录映射添加到专用终结点的 IP。

* 使用 DNS 转发器（可选） 。 可使用 DNS 转发器来替代特定专用链接资源的 DNS 解析。 如果 [DNS 服务器](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)托管在虚拟网络上，可以创建 DNS 转发规则，以使用专用 DNS 区域来简化所有专用链接资源的配置。

有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](../../private-link/private-endpoint-dns.md)。

## <a name="next-steps"></a>后续步骤

若要详细了解专用终结点，请参阅[什么是 Azure 专用终结点？](../../private-link/private-endpoint-overview.md)。
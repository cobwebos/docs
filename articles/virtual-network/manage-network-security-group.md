---
title: 创建、更改或删除 Azure 网络安全组
titlesuffix: Azure Virtual Network
description: 了解如何创建、更改或删除网络安全组。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 9ed4ce6befda76069e965501a320dc110129a024
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521027"
---
# <a name="create-change-or-delete-a-network-security-group"></a>创建、更改或删除网络安全组

通过网络安全组中的安全规则，可以筛选可流入和流出虚拟网络子网和网络接口的流量类型。 若要深入了解网络安全组，请参阅[网络安全组概述](security-overview.md)。 接下来，完成["筛选网络流量](tutorial-filter-network-traffic.md)教程"，以获得网络安全组的一些经验。

## <a name="before-you-begin"></a>在开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果没有，则使用活动订阅设置 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 在开始本文的其余部分之前完成这些任务之一：

- **门户用户**：使用 Azure 帐户登录到[Azure 门户](https://portal.azure.com)。

- **PowerShell 用户**：在[Azure 云壳](https://shell.azure.com/powershell)中运行命令，或者从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 在 Azure 云壳浏览器选项卡中，查找 **"选择环境**"下拉列表，如果尚未选择**PowerShell，请选择 PowerShell。**

    如果在本地运行 PowerShell，请使用 Azure PowerShell 模块版本 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az.Network` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 运行 `Connect-AzAccount`，创建与 Azure 的连接。

- **Azure 命令行接口 （CLI） 用户**：在 Azure[云外壳](https://shell.azure.com/bash)中运行命令，或从计算机运行 CLI。 如果您在本地运行 Azure CLI，请使用 Azure CLI 版本 2.0.28 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 运行 `az login`，创建与 Azure 的连接。

必须将您登录或连接到 Azure 的帐户分配给[网络参与者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)或已分配[权限](#permissions)中列出的相应操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="work-with-network-security-groups"></a>使用网络安全组

对于网络安全组可执行创建、[查看所有](#view-all-network-security-groups)、[查看详细信息](#view-details-of-a-network-security-group)[更改](#change-a-network-security-group)以及[删除](#delete-a-network-security-group)操作。 也可从网络接口或子网[关联或取消关联](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)网络安全组。

### <a name="create-a-network-security-group"></a>创建网络安全组

可以为每个 Azure 位置和订阅创建多少个网络安全组是有限制的。 要了解更多信息，请参阅[Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在[Azure 门户](https://portal.azure.com)菜单或**主页**上，选择 **"创建资源**"。

2. 选择 **"网络**"，然后选择**网络安全组**。

3. 在"**创建网络安全组"** 页中，在 **"基本"** 选项卡下，为以下设置设置值：

    | 设置 | 操作 |
    | --- | --- |
    | **订阅** | 选择订阅。 |
    | **资源组** | 选择现有资源组，或选择 **"新建**"以创建新资源组。 |
    | **名称** | 在资源组中输入唯一的文本字符串。 |
    | **区域** | 选择您想要的位置。 |

4. 选择 **"审阅" = 创建**。

5. 看到**验证传递**的消息后，选择 **"创建**"。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>查看所有网络安全组

转到[Azure 门户](https://portal.azure.com)以查看网络安全组。 搜索并选择**网络安全组**。 为您的订阅显示网络安全组的列表。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 nsg 列表](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>查看网络安全组的详细信息

1. 转到[Azure 门户](https://portal.azure.com)以查看网络安全组。 搜索并选择**网络安全组**。

2. 选择网络安全组的名称。

在网络安全组的菜单栏中，在 **"设置"** 下，您可以查看网络安全组关联的**入站安全规则**、**出站安全规则**、**网络接口**和**子网**。

在 **"监视"** 下，您可以启用或禁用**诊断设置**。 在 **"支持 + 故障排除"下**，您可以查看**有效安全规则**。 要了解更多信息，请参阅[网络安全组的诊断日志记录](virtual-network-nsg-manage-log.md)和诊断 VM[网络流量筛选器问题](diagnose-network-traffic-filter-problem.md)。

要了解有关列出的常见 Azure 设置的详细信息，请参阅以下文章：

- [活动日志](../azure-monitor/platform/platform-logs-overview.md)
- [访问控制 （IAM）](../role-based-access-control/overview.md)
- [标记](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [锁](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [自动化脚本](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 nsg 显示](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>更改网络安全组

1. 转到[Azure 门户](https://portal.azure.com)以查看网络安全组。 搜索并选择**网络安全组**。

2. 选择要更改的网络安全组的名称。

最常见的更改是[添加安全规则](#create-a-security-rule)、[删除规则](#delete-a-security-rule)，并将[网络安全组关联或脱离子网或网络接口](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 nsg 更新](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>将网络安全组与子网或网络接口关联或取消关联

要将网络安全组关联到网络接口，或从网络接口取消关联网络安全组，请参阅[将网络安全组与网络接口关联或取消关联](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)。 要将网络安全组关联到子网，或从子网取消关联网络安全组，请参阅[更改子网设置](virtual-network-manage-subnet.md#change-subnet-settings)。

### <a name="delete-a-network-security-group"></a>删除网络安全组

如果网络安全组与任何子网或网络接口相关联，则无法将其删除。 从所有子网和网络接口取消关联网络安全组，然后再尝试将其删除。

1. 转到[Azure 门户](https://portal.azure.com)以查看网络安全组。 搜索并选择**网络安全组**。

2. 选择要删除的网络安全组的名称。

3. 在网络安全组的工具栏中，选择 **"删除**"。 在确认对话框中选择“是”。****

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 nsg 删除](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>使用安全规则

网络安全组包含零个或多个安全规则。 对于安全规则，可执行创建、[查看所有](#view-all-security-rules)、[查看详细信息](#view-details-of-a-security-rule)[更改](#change-a-security-rule)以及[删除](#delete-a-security-rule)操作。

### <a name="create-a-security-rule"></a>创建安全规则

每个网络安全组可以为每个 Azure 位置和订阅创建多少规则是有限制的。 要了解更多信息，请参阅[Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 转到[Azure 门户](https://portal.azure.com)以查看网络安全组。 搜索并选择**网络安全组**。

2. 选择要向其添加安全规则的网络安全组的名称。

3. 在网络安全组的菜单栏中，选择**入站安全规则**或**出站安全规则**。

    列出了几个现有规则，包括您可能尚未添加的一些规则。 创建网络安全组时，会创建多个默认安全规则。 要了解详细信息，请参阅[默认安全规则](security-overview.md#default-security-rules)。  无法删除默认安全规则，但可以使用更高优先级的规则将其覆盖。

4. <a name="security-rule-settings"></a>选择 **"添加**"。 选择或添加以下设置的值，然后选择 **"确定**" ：

    | 设置 | 值 | 详细信息 |
    | ------- | ----- | ------- |
    | **源** | 可取值为：<ul><li>**任何**</li><li>**IP 地址**</li><li>**服务标记**（入站安全规则）或**虚拟网络**（出站安全规则）</li><li>**应用程序&nbsp;安全&nbsp;组**</li></ul> | <p>如果选择**IP 地址**，还必须指定**源 IP 地址/CIDR 范围**。</p><p>如果选择**服务标记**，还可以选择**源服务标记**。</p><p>如果选择**应用程序安全组**，还必须选择现有的应用程序安全组。 如果同时为 **"源"****和目标**选择**应用程序安全组**，则两个应用程序安全组中的网络接口必须位于同一虚拟网络中。</p> |
    | **源 IP 地址/CIDR 范围** | IP 地址和无类域间路由 （CIDR） 范围的逗号分隔列表 | <p>如果将**源**更改为**IP 地址**，则将显示此设置。 必须指定多个值的单个值或逗号分隔列表。 多个值的一个示例`10.0.0.0/16, 192.188.1.1`是 。 可指定的值的数目有限制。 有关详细信息，请参阅[Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。</p><p>如果指定的 IP 地址分配给 Azure VM，请指定其专用 IP 地址，而不是其公共 IP 地址。 Azure 将公共 IP 地址转换为入站安全规则的专用 IP 地址后，但在将专用 IP 地址转换为出站规则的公共 IP 地址之前，将安全规则进行处理。 若要了解有关 Azure 中的公共和专用 IP 地址的详细信息，请参阅 [IP 地址类型](virtual-network-ip-addresses-overview-arm.md)。</p> |
    | **源服务标记** | 下拉列表中的服务标记 | 如果为入站安全规则设置了 **"源**到**服务标记"，** 则会出现此可选设置。 服务标记是 IP 地址类别的预定义标识符。 要了解有关可用服务标记以及每个标记表示的内容的更多内容，请参阅[服务标记](security-overview.md#service-tags)。 |
    | **源应用程序安全组** | 现有应用程序安全组 | 如果将 **"源"** 设置为**应用程序安全组**，则将显示此设置。 选择与网络接口位于同一区域中的应用程序安全组。 了解如何[创建应用程序安全组](#create-an-application-security-group)。 |
    | **源端口范围** | 可取值为：<ul><li>单个端口，例如`80`</li><li>一系列端口，例如`1024-65535`</li><li>单个端口和/或端口范围的逗号分隔列表，例如`80, 1024-65535`</li><li>星号 （`*`） 允许任何端口上的流量</li></ul> | 此设置指定规则允许或拒绝流量的端口。 可指定的端口数目有限制。 有关详细信息，请参阅[Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 |
    | **目的地** | 可取值为：<ul><li>**任何**</li><li>**IP 地址**</li><li>**服务标记**（出站安全规则）或**虚拟网络**（入站安全规则）</li><li>**应用程序&nbsp;安全&nbsp;组**</li></ul> | <p>如果选择**IP 地址**，则还要指定**目标 IP 地址/CIDR 范围**。</p><p>如果选择 **"虚拟网络"，** 则允许流量访问虚拟网络地址空间中的所有 IP 地址。 **虚拟网络**是服务标记。</p><p>如果选择**应用程序安全组**，则必须选择现有应用程序安全组。 了解如何[创建应用程序安全组](#create-an-application-security-group)。</p> |
    | **目标 IP 地址/CIDR 范围** | IP 地址和 CIDR 范围的逗号分隔列表 | <p>如果将 **"目标"** 更改为 IP**地址**，则将显示此设置。 与**源**和**源 IP 地址/CIDR 范围**类似，您可以指定单个或多个地址或范围。 您可以指定的数字有限制。 有关详细信息，请参阅[Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。</p><p>如果指定的 IP 地址已分配给 Azure VM，请确保指定其专用 IP，而不是其公共 IP 地址。 Azure 将公共 IP 地址转换为入站安全规则的专用 IP 地址后，但在 Azure 将专用 IP 地址转换为出站规则的公共 IP 地址之前，将安全规则进行处理。 若要了解有关 Azure 中的公共和专用 IP 地址的详细信息，请参阅 [IP 地址类型](virtual-network-ip-addresses-overview-arm.md)。</p> |
    | **目标服务标记** | 下拉列表中的服务标记 | 如果为出站安全规则更改 **"目标**到**服务标记"，** 则将显示此可选设置。 服务标记是 IP 地址类别的预定义标识符。 要了解有关可用服务标记以及每个标记表示的内容的更多内容，请参阅[服务标记](security-overview.md#service-tags)。 |
    | **目标应用程序安全组** | 现有应用程序安全组 | 如果将 **"目标"** 设置为**应用程序安全组**，则将显示此设置。 选择与网络接口位于同一区域中的应用程序安全组。 了解如何[创建应用程序安全组](#create-an-application-security-group)。 |
    | **目标端口范围** | 可取值为：<ul><li>单个端口，例如`80`</li><li>一系列端口，例如`1024-65535`</li><li>单个端口和/或端口范围的逗号分隔列表，例如`80, 1024-65535`</li><li>星号 （`*`） 允许任何端口上的流量</li></ul> | 与**Source 端口范围**一样，您可以指定单个或多个端口和范围。 您可以指定的数字有限制。 有关详细信息，请参阅[Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 |
    | **协议** | **任何** **、TCP、UDP**或**ICMP** **UDP** | 您可以将规则限制为传输控制协议 （TCP）、用户数据图协议 （UDP） 或 Internet 控制消息协议 （ICMP）。 默认值是规则应用于所有协议。 |
    | **操作** | **允许**或**拒绝** | 此设置指定此规则是否允许或拒绝对提供的源和目标配置的访问。 |
    | **优先** | 100 和 4096 之间的值，对于网络安全组中的所有安全规则都是唯一的 | Azure 按优先级顺序处理安全规则。 编号越低，优先级越高。 我们建议您在创建规则（如 100、200 和 300）时，在优先级数字之间留出间隙。 留下间隙可以更轻松地在将来添加规则，以便您可以给予它们比现有规则更高或更低的优先级。 |
    | **名称** | 网络安全组中规则的唯一名称 | 名称最多可包含 80 个字符。 它必须以字母或数字开头，并且必须以字母、数字或下划线结尾。 名称只能包含字母、数字、下划线、句点和连字符。 |
    | **说明** | 文本说明 | 您可以选择为安全规则指定文本说明。 |

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>查看所有安全规则

网络安全组包含零个或多个规则。 要详细了解有关查看规则时所列的信息，请参阅[网络安全组概述](security-overview.md)。

1. 转到[Azure 门户](https://portal.azure.com)以查看网络安全组的规则。 搜索并选择**网络安全组**。

2. 选择要为其查看规则的网络安全组的名称。

3. 在网络安全组的菜单栏中，选择**入站安全规则**或**出站安全规则**。

该列表包含您创建的任何规则以及网络安全组的[默认安全规则](security-overview.md#default-security-rules)。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>查看安全规则的详细信息

1. 转到[Azure 门户](https://portal.azure.com)以查看网络安全组的规则。 搜索并选择**网络安全组**。

2. 选择要为其查看规则详细信息的网络安全组的名称。

3. 在网络安全组的菜单栏中，选择**入站安全规则**或**出站安全规则**。

4. 选择要查看其详细信息的规则。 有关所有设置的说明，请参阅[安全规则设置](#security-rule-settings)。

    > [!NOTE]
    > 此过程仅适用于自定义安全规则。 如果选择默认安全规则，则不起作用。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 nsg 规则显示](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>更改安全规则

1. 完成[查看安全规则的详细信息](#view-details-of-a-security-rule)中的步骤。

2. 根据需要更改设置，然后选择 **"保存**"。 有关所有设置的说明，请参阅[安全规则设置](#security-rule-settings)。

    > [!NOTE]
    > 此过程仅适用于自定义安全规则。 不允许更改默认安全规则。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>删除安全规则

1. 完成[查看安全规则的详细信息](#view-details-of-a-security-rule)中的步骤。

2. 依次选择“删除”、“是”。********

    > [!NOTE]
    > 此过程仅适用于自定义安全规则。 不允许删除默认安全规则。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 nsg 规则删除](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>使用应用程序安全组

应用程序安全组包含零个或多个网络接口。 要了解详细信息，请参阅[应用程序安全组](security-overview.md#application-security-groups)。 应用程序安全组中的所有网络接口必须存在于同一虚拟网络中。 要了解如何将网络接口添加到应用程序安全组，请参阅[将网络接口添加到应用程序安全组](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)。

### <a name="create-an-application-security-group"></a>创建应用程序安全组

1. 在[Azure 门户](https://portal.azure.com)菜单或**主页**上，选择 **"创建资源**"。

2. 在搜索框中，输入*应用程序安全组*。

3. 在 **"应用程序安全组"** 页中，选择 **"创建**"。

4. 在"**创建应用程序安全组"** 页中，在 **"基本"** 选项卡下，为以下设置设置值：

    | 设置 | 操作 |
    | --- | --- |
    | **订阅** | 选择订阅。 |
    | **资源组** | 选择现有资源组，或选择 **"新建**"以创建新资源组。 |
    | **名称** | 在资源组中输入唯一的文本字符串。 |
    | **区域** | 选择您想要的位置。 |

5. 选择 **"审阅" = 创建**。

6. 在 **"审核 + 创建**"选项卡下，在看到 **"验证传递"** 消息后，选择 **"创建**"。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 asg 创建](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>查看所有应用程序安全组

转到[Azure 门户](https://portal.azure.com)以查看应用程序安全组。 搜索并选择**应用程序安全组**。 Azure 门户显示应用程序安全组的列表。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 asg 列表](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>查看特定应用程序安全组的详细信息

1. 转到[Azure 门户](https://portal.azure.com)以查看应用程序安全组。 搜索并选择**应用程序安全组**。

2. 选择要查看其详细信息的应用程序安全组的名称。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 asg 显示](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>更改应用程序安全组

1. 转到[Azure 门户](https://portal.azure.com)以查看应用程序安全组。 搜索并选择**应用程序安全组**。

2. 选择要更改的应用程序安全组的名称。

3. 选择要修改的设置旁边的**更改**。 例如，您可以添加或删除**标记**，也可以更改**资源组**或**订阅**。

    > [!NOTE]
    > 您无法更改位置。

    在菜单栏中，您还可以选择**访问控制 （IAM）。** 在**Access 控件 （IAM）** 页中，您可以向应用程序安全组分配或删除权限。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 asg 更新](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | 无电源外壳 cmdlet |

### <a name="delete-an-application-security-group"></a>删除应用程序安全组

如果应用程序安全组包含任何网络接口，则无法删除它。 要从应用程序安全组中删除所有网络接口，请更改网络接口设置或删除网络接口。 要了解更多信息，请参阅[从应用程序安全组添加或删除](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)或[删除网络接口](virtual-network-network-interface.md#delete-a-network-interface)。

1. 转到[Azure 门户](https://portal.azure.com)以管理应用程序安全组。 搜索并选择**应用程序安全组**。

2. 选择要删除的应用程序安全组的名称。

3. 选择“删除”，然后选择“是”，删除应用程序安全组********。

#### <a name="commands"></a>命令

| 工具 | Command |
| ---- | ------- |
| Azure CLI | [az 网络 asg 删除](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>权限

要执行网络安全组、安全规则和应用程序安全组的任务，您的帐户必须分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或已分配相应权限的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，如下表中列出：

### <a name="network-security-group"></a>网络安全组

| 操作                                                        |   名称                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   获取网络安全组                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   创建或更新网络安全组                             |
| Microsoft.Network/networkSecurityGroups/delete                |   删除网络安全组                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   将网络安全组与子网或网络接口关联 

### <a name="network-security-group-rule"></a>网络安全组规则

| 操作                                                        |   名称                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   获取规则                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   创建或更新规则                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   删除规则                                                         |

### <a name="application-security-group"></a>应用程序安全组

| 操作                                                                     | 名称                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | 将 IP 配置加入到应用程序安全组中|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 将安全规则加入到应用程序安全组中    |
| Microsoft.Network/applicationSecurityGroups/read                           | 获取应用程序安全组                        |
| Microsoft.Network/applicationSecurityGroups/write                          | 创建或更新应用程序安全组           |
| Microsoft.Network/applicationSecurityGroups/delete                         | 删除应用程序安全组                     |

## <a name="next-steps"></a>后续步骤

- 使用[PowerShell](powershell-samples.md)或[Azure CLI](cli-samples.md)示例脚本或 Azure[资源管理器模板](template-samples.md)创建网络或应用程序安全组
- 为虚拟网络创建并应用 [Azure Policy](policy-samples.md)

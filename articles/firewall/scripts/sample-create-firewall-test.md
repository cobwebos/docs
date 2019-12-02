---
title: 创建 Azure 防火墙测试环境
description: 此脚本示例创建防火墙和测试网络环境。 网络具有一个包含三个子网的 VNet。
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6cb0eafce354ba54dd161fee52efe46677ad2765
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195884"
---
# <a name="create-an-azure-firewall-test-environment"></a>创建 Azure 防火墙测试环境

此脚本示例创建防火墙和测试网络环境。 网络有一个 VNet，其中包含三个子网：*AzureFirewallSubnet*、*ServersSubnet* 和 *JumpboxSubnet*。 ServersSubnet 和 JumpboxSubnet 每个中都有一个 2 核 Windows Server。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

防火墙在 AzureFirewallSubnet 中并配置有一个应用程序规则集合，其中包含允许访问 `www.microsoft.com` 的单个规则。

创建了用户定义的一个路由，它引导来自 ServersSubnet 的网络流量穿过应用了防火墙规则的防火墙。

可以通过 Azure [Cloud Shell](https://shell.azure.com/powershell) 或从本地 PowerShell 安装来运行脚本。 

如果在本地运行 PowerShell，则此脚本需要 Azure PowerShell。 要查找已安装的版本，请运行 `Get-Module -ListAvailable Az`。 

如果需要升级，则可以使用 `PowerShellGet`，它内置在 Windows 10 和 Windows Server 2016 中。

> [!NOTE]
>对于其他 Windows 版本，需要先安装 `PowerShellGet`，然后才能使用它。 可以运行 `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` 来确定它是否已安装在你的系统上。 如果输出为空，则需要安装最新的 [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)。

有关详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-Az-ps)

使用 Web 平台安装程序执行的任何现有 Azure PowerShell 安装都将与 PowerShellGet 安装冲突并且需要删除。

请注意，如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源：

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟网络和网络安全组。 下表中的每条命令均链接到特定于命令的文档：

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 创建子网配置对象 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建 Azure 虚拟网络和前端子网。 |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | 创建要分配到网络安全组的安全规则。 |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |创建 NSG 规则，允许或阻止特定子网的特定端口。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 将 NSG 关联到子网。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 创建用于从 Internet 访问 VM 的公共 IP 地址。 |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 创建虚拟网络接口，并将其附加到虚拟网络的前端和后端子网。 |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | 创建 VM 配置。 此配置包括 VM 名称、操作系统和管理凭据等信息。 在创建 VM 期间将使用此配置。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 创建虚拟机。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 |
|[New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall)| 创建新的 Azure 防火墙。|
|[Get-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/get-azfirewall)|获取 Azure 防火墙对象。|
|[New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule)|创建新的 Azure 防火墙应用程序规则。|
|[Set-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/set-azfirewall)|将更改提交到 Azure 防火墙对象。|

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。


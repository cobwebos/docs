---
title: Azure PowerShell 脚本示例 - 通过网络虚拟设备路由流量 | Microsoft Docs
description: Azure PowerShell 脚本示例 - 通过防火墙网络虚拟设备路由流量。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 332d6ecc6becc9f1219c3b90cbbf35f8fa85faa9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>通过网络虚拟设备脚本示例路由流量

该脚本示例创建了包含前端和后端子网的虚拟网络。 它还会创建一个 VM，并启用 IP 转发，在两个子网之间路由流量。 运行脚本后，可将网络软件（例如防火墙应用程序）部署到 VM。

可以通过 Azure [Cloud Shell](https://shell.azure.com/powershell) 或本地 PowerShell 安装来执行脚本。 如果在本地使用 PowerShell，则此脚本需要 AzureRM PowerShell 模块 5.4.1 或更高版本。 要查找已安装的版本，请运行 `Get-Module -ListAvailable AzureRM`。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本


[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源：

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```
## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟网络和网络安全组。 下表中的每条命令均链接到特定于命令的文档：

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | 创建用于存储所有资源的资源组。 |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | 创建 Azure 虚拟网络和前端子网。 |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 创建后端子网和 DMZ 子网。 |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | 创建用于从 Internet 访问 VM 的公共 IP 地址。 |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | 创建虚拟网络接口，并对它启用 IP 转发。 |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | 创建网络安全组 (NSG)。 |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | 创建允许 HTTP 和 HTTPS 端口入站到 VM 的 NSG 规则。 |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)| 将 NSG 和路由表关联到子网。 |
| [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)| 为所有路由创建路由表。 |
| [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)| 创建路由，通过 VM 在子网和 Internet 之间路由流量。 |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | 创建虚拟机并向其附加 NIC。 此命令还指定要使用的虚拟机映像和管理凭据。 |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)  | 删除资源组及其包含的所有资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可在[虚拟网络 PowerShell 示例](../powershell-samples.md)中查找其他虚拟网络 PowerShell 脚本示例。
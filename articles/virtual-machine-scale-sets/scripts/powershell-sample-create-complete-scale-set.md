---
title: Azure PowerShell 示例 - 创建完整的虚拟机规模集
description: 此脚本创建一个运行 Windows Server 2016 的虚拟机规模集，其中配置并创建了各个资源。
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 05/29/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: df2c1b10f0d63e3cafbd6f20805a296710219461
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699549"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>使用 PowerShell 创建完整的虚拟机规模集

此脚本创建运行 Windows Server 2016 的虚拟机规模集。 单个资源是配置和创建的，而不是使用 [New-AzVmss 中提供的内置资源创建选项](powershell-sample-create-simple-scale-set.md)。 运行脚本后，可通过 RDP 访问 VM 实例。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>清理部署
运行以下命令可删除资源组、规模集和所有相关资源。

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 创建子网配置。 在虚拟网络创建过程中将使用此配置。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 创建公共 IP 地址。 |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | 创建负载均衡器的前端 IP 配置。 |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | 创建负载均衡器的后端地址池配置。 |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | 创建负载均衡器的入站 NAT 规则配置。 |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | 创建负载均衡器。 |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | 创建负载均衡器的探测配置。 |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | 创建负载均衡器的规则配置。 |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | 使用所提供的信息更新负载均衡器。 |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | 为规模集 VM 实例创建 IP 配置。 VM 实例将连接到负载均衡器后端池、NAT 池和虚拟网络子网。 |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | 创建规模集配置。 此配置包括要创建的 VM 实例数量、VM SKU（大小）和升级策略模式等信息。 此配置由其他 cmdlet 添加，并在创建规模集时使用。 |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | 定义要用于 VM 实例的映像，并将其添加到规模集配置中。 |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | 定义管理用户名和密码凭据以及 VM 命名前缀。 将这些值添加到规模集配置。 |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | 根据 IP 配置，将虚拟网络接口添加到 VM 实例。 将这些值添加到规模集配置。 |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | 根据规模集配置中提供的信息创建规模集。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 |

## <a name="next-steps"></a>后续步骤
有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

---
title: "Azure PowerShell 脚本示例的流量进行负载平衡到 Vm 以实现高可用性 |Microsoft 文档"
description: "Azure PowerShell 脚本示例的流量进行负载平衡到 Vm 以实现高可用性"
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: c77def8906b151f2cc6e4bbc4188be8ecbeac732
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>流量进行负载平衡到 Vm 以实现高可用性

该脚本示例创建运行多个 Windows 虚拟机配置在一个高可用性和负载平衡的配置所需的一切。 运行脚本后，你将具有三个虚拟机，加入到 Azure 可用性集，并且通过 Azure 负载平衡器可访问。

如果需要安装 Azure PowerShell 中使用的指令中找到[Azure PowerShell 指南](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)，然后运行`Login-AzureRmAccount`若要创建与 Azure 的连接。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[主要](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "快速创建 VM")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令以删除资源组、 VM，以及所有相关的资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令以创建资源组、 虚拟机、 可用性集、 负载平衡器和所有相关的资源。 命令特定文档的表链接中的每个命令。

| 命令 | 注意 |
|---|---|
| [新 AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建资源组中的所有资源的都存储。 |
| [新 AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 创建子网配置。 此配置用于虚拟网络创建过程。 |
| [新 AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | 创建一个 Azure 虚拟网络和子网。 |
| [新 AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)  | 创建具有静态 IP 地址和关联的 DNS 名称的公共 IP 地址。 |
| [新 AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer)  | 创建一个 Azure 负载平衡器。 |
| [新 AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | 创建负载平衡器探测。 负载平衡器探测用于监视每个虚拟机中的负载平衡器集。 如果任何 VM 变得无法访问，流量不会路由到 VM。 |
| [新 AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | 创建负载平衡器规则。 在此示例中，为端口 80 创建一个规则。 根据 HTTP 流量在到达负载平衡器，它被路由到端口 80 中负载平衡器集的虚拟机之一。 |
| [新 AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | 创建负载平衡器网络地址转换 (NAT) 规则。  NAT 规则将负载平衡器端口映射到 VM 上的端口。 在此示例中，为 SSH 流量引导至每个 VM 中的负载平衡器集创建 NAT 规则。  |
| [新 AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | 创建网络安全组 (NSG)，这是 internet 和虚拟机之间的安全边界。 |
| [新 AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | 创建允许入站的流量的 NSG 规则。 在此示例中，为 SSH 流量打开端口 22。 |
| [新 AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | 创建虚拟网络卡，并将其附加到虚拟网络、 子网和 NSG。 |
| [新 AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | 创建一个可用性集。 可用性集确保通过分散物理资源的虚拟机，以便如果失败，则不会影响整个集的应用程序运行时间。 |
| [新 AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | 创建虚拟机配置。 此配置包括 VM 名称、 操作系统和管理凭据等信息。 在 VM 创建期间使用的配置。 |
| [新 AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)  | 创建虚拟机并将其连接到网络卡、 虚拟网络、 子网和 NSG。 此命令还指定要使用和管理凭据的虚拟机映像。  |
| [Remove-azurermresourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅[Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

在找不到其他网络的 PowerShell 脚本示例[Azure 网络概述文档](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)。

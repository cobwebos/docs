---
title: "Azure PowerShell 脚本示例-创建 Windows VM NLB |Microsoft 文档"
description: "Azure PowerShell 脚本示例-创建 Windows VM NLB"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 25bcbbcd1615e01a384825d7bd1582a528e91f71
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>高可用虚拟机之间的负载平衡流量

该脚本示例创建运行多个 Windows Server 2016 虚拟机配置在一个高可用性和负载平衡的配置所需的一切。 运行脚本后，你将具有三个虚拟机，加入到 Azure 可用性集，并且通过 Azure 负载平衡器可访问。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[主要](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "创建 VM NLB")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令以删除资源组、 VM，以及所有相关的资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令以创建部署。 表链接到命令特定文档中的每个项。

| 命令 | 注意 |
|---|---|
| [新 AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建资源组中的所有资源的都存储。 |
| [新 AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 创建子网配置。 此配置用于虚拟网络创建过程。 |
| [新 AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | 创建虚拟网络。 |
| [新 AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | 创建一个公共 IP 地址。 |
| [新 AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | 创建的负载平衡器前端 IP 配置。 |
| [新 AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | 创建的负载平衡器后端地址池配置。 |
| [新 AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | 创建的负载平衡器探测配置。 |
| [新 AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | 创建规则配置的负载平衡器。 |
| [新 AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | 创建负载平衡器入站的 NAT 规则配置。 |
| [新 AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | 创建负载平衡器。 |
| [新 AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | 创建网络安全组规则配置。 此配置用于创建 NSG 时创建的 NSG 规则。 |
| [新 AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | 创建网络安全组。 |
| [Get AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) | 获取子网信息。 创建网络接口时使用此信息。 |
| [新 AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | 创建一个网络接口。 |
| [新 AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | 创建虚拟机配置。 此配置包括 VM 名称、 操作系统和管理凭据等信息。 在 VM 创建期间使用的配置。 |
| [新 AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | 创建虚拟机。 |
|[Remove-azurermresourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组及其内包含的所有资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅[Azure PowerShell 文档](/powershell/azure/overview)。

在找不到其他虚拟机 PowerShell 脚本示例[Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

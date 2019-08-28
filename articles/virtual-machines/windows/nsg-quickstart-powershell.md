---
title: 使用 Azure PowerShell 打开指向 VM 的端口 | Microsoft Docs
description: 了解如何使用 Azure Resource Manager 部署模型和 Azure PowerShell 为 Windows VM 打开端口/创建终结点
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: cd5aab6934e2f9692411e09046722cd59ad5e6a8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089104"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>如何在 Azure 中使用 PowerShell 打开 VM 的端口和终结点
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>快速命令
若要创建网络安全组和 ACL 规则，需要[安装最新版本的 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 还可以[使用 Azure 门户执行这些步骤](nsg-quickstart-portal.md)。

登录到 Azure 帐户：

```powershell
Connect-AzAccount
```

在以下示例中，请将参数名称替换成自己的值。 示例参数名称包括了 myResourceGroup、myNetworkSecurityGroup 和 myVnet。

使用 [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) 创建规则。 以下示例创建一个名为 myNetworkSecurityGroupRule 的规则，以允许端口 80 上的 tcp 流量：

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

接下来，使用 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) 创建网络安全组，并按以下步骤分配刚刚创建的 HTTP 规则。 以下示例创建名为 myNetworkSecurityGroup 的网络安全组：

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

现在我们将网络安全组分配给子网。 以下示例使用 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) 向变量 *$vnet* 分配名为 *myVnet* 的现有虚拟网络：

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

使用 [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig) 将网络安全组关联到子网。 以下示例将名为 mySubnet 的子网与网络安全组相关联：

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

最后，使用 [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) 更新虚拟网络，使更改生效：

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>有关网络安全组的详细信息
利用此处的快速命令，可以让流向 VM 的流量开始正常运行。 网络安全组提供许多出色的功能和粒度来控制资源的访问。 可以[在此处详细了解如何创建网络安全组和 ACL 规则](tutorial-virtual-network.md#secure-network-traffic)。

对于高可用性 Web 应用程序，应将 VM 放置在 Azure 负载均衡器后。 当负载均衡器向 VM 分配流量时，网络安全组可以筛选流量。 有关详细信息，请参阅[如何在 Azure 中均衡 Linux 虚拟机负载以创建高可用性应用程序](tutorial-load-balancer.md)。

## <a name="next-steps"></a>后续步骤
在本示例中，创建了简单的规则来允许 HTTP 流量。 下列文章更介绍了有关创建更详细环境的信息：

* [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)
* [什么是网络安全组？](../../virtual-network/security-overview.md)
* [Azure 资源管理器中负载均衡器的概述](../../load-balancer/load-balancer-arm.md)


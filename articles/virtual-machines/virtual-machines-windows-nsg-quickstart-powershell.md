---
title: "使用 Azure PowerShell 打开指向 VM 的端口 | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 部署模型和 Azure PowerShell 为 Windows VM 打开端口/创建终结点"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 91c2e6f53f8ba490d2943a60c7ad519d856707b9
ms.openlocfilehash: 0d57a27c0862b09e2628520a1e50a0cc56a55881


---
# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>在 Azure 中使用 PowerShell 打开 VM 的端口和终结点
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>快速命令
若要创建网络安全组和 ACL 规则，需要[安装最新版本的 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 还可以[使用 Azure 门户执行这些步骤](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

登录到你的 Azure 帐户：

```powershell
Login-AzureRmAccount
```

在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 `myResourceGroup`、`myNetworkSecurityGroup` 和 `myVnet`。

创建规则。 以下示例创建一个名为 `myNetworkSecurityGroupRule` 的规则，以允许端口 80 上的 TCP 流量：

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

接下来，创建网络安全组，并按以下步骤分配刚刚创建的 HTTP 规则。 以下示例创建一个名为 `myNetworkSecurityGroup` 的网络安全组：

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

现在我们将网络安全组分配给子网。 以下示例为变量 `$vnet` 指定名为 `myVnet` 的现有虚拟网络：

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

将网络安全组与子网相关联。 以下示例将名为 `mySubnet` 的子网与网络安全组相关联：

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

最后，更新虚拟网络以使做出的更改生效：

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>有关网络安全组的详细信息
利用此处的快速命令，可以让流向 VM 的流量开始正常运行。 网络安全组提供许多出色的功能和粒度来控制资源的访问。 可以[在此处详细了解如何创建网络安全组和 ACL 规则](../virtual-network/virtual-networks-create-nsg-arm-ps.md)。

可以将网络安全组和 ACL 规则定义为 Azure Resource Manager 模板的一部分。 详细了解如何[使用模板创建网络安全组](../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果需要使用端口转发将唯一的外部端口映射至 VM 上的内部端口，则需要使用负载均衡器和网络地址转换 (NAT) 规则。 例如，你可能想对外公开 TCP 端口 8080，然后让流量定向到 VM 上的 TCP 端口 80。 可以了解如何[创建面向 Internet 的负载平衡器](../load-balancer/load-balancer-get-started-internet-arm-ps.md)。

## <a name="next-steps"></a>后续步骤
在本示例中，你创建了简单的规则来允许 HTTP 流量。 你可以从下列文章中，找到有关创建更详细环境的信息：

* [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)
* [什么是网络安全组 (NSG)？](../virtual-network/virtual-networks-nsg.md)
* [Azure Resource Manager 中负载平衡器的概述](../load-balancer/load-balancer-arm.md)




<!--HONumber=Feb17_HO2-->



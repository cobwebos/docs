---
title: 使用现有 Azure 负载均衡器配置虚拟机规模集 - Azure PowerShell
description: 了解如何使用现有 Azure 负载均衡器配置虚拟机规模集。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349996"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>使用 Azure PowerShell 配置使用现有 Azure 负载均衡器的虚拟机规模集

在本文中，您将了解如何使用现有的 Azure 负载均衡器配置虚拟机规模集。 

## <a name="prerequisites"></a>先决条件

- Azure 订阅。
- 将部署虚拟机规模集的订阅中的现有标准 sKU 负载均衡器。
- 虚拟机缩放集的 Azure 虚拟网络。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>登录 Azure CLI

登录到 Azure。

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>使用现有负载均衡器部署虚拟机规模集

将括号中的值替换为配置中资源的名称。

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

下面的示例部署虚拟机规模集，该集具有：

- 名为**myVMSS 的**虚拟机规模集
- Azure 负载均衡器名为**myLoad 平衡器**
- 负载均衡器后端池名为 **"我的后端池**"
- 名为**myVnet 的**Azure 虚拟网络
- 名为 **"我的子网"的子网**
- 名为 **"我的资源组"的资源组**

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> 创建比例集后，无法为负载均衡器的运行状况探测使用的负载平衡规则修改后端端口。 要更改端口，可以通过更新 Azure 虚拟机缩放集、更新端口然后再次配置运行状况探测来删除运行状况探测。

## <a name="next-steps"></a>后续步骤

在本文中，您部署了一个虚拟机缩放集与现有的 Azure 负载均衡器。  要了解有关虚拟机缩放集和负载均衡器的更多详细信息，请参阅：

- [什么是 Azure 负载均衡器？](load-balancer-overview.md)
- [什么是虚拟机规模集？](../virtual-machine-scale-sets/overview.md)
                                
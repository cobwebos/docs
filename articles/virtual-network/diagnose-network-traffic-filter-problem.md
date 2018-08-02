---
title: 诊断虚拟机网络流量筛选器问题 | Microsoft Docs
description: 了解如何通过查看虚拟机的有效的安全规则来诊断虚拟机网络流量筛选器问题。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: jdial
ms.openlocfilehash: 67b2babcd19268a61794d123f5aa9780af16976b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364006"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>诊断虚拟机网络流量筛选器问题

本文介绍如何通过查看虚拟机 (VM) 的有效网络安全组 (NSG) 安全规则来诊断网络流量筛选器问题。

使用 NSG 可以控制流入和流出 VM 的流量类型。 可将 NSG 关联到 Azure 虚拟网络中的子网和/或附加到 VM 的网络接口。 应用到网络接口的有效安全规则是关联到网络接口以及网络接口所在子网的 NSG 的聚合。 不同 NSG 中的规则有时互相冲突，影响 VM 的网络连接。 可以查看 NSG 中对 VM 网络接口应用的所有有效安全规则。 如果不熟悉虚拟网络、网络接口或 NSG 的概念，请参阅[虚拟网络概述](virtual-networks-overview.md)、[网络接口](virtual-network-network-interface.md)和[网络安全组概述](security-overview.md)。

## <a name="scenario"></a>场景

尝试通过端口 80 从 Internet 连接到 VM，但连接失败。 若要确定为何无法从 Internet 访问端口 80，可以使用 Azure [门户](#diagnose-using-azure-portal)、[PowerShell](#diagnose-using-powershell) 或 [Azure CLI](#diagnose-using-azure-cli) 查看网络接口的有效安全规则。

以下步骤假设有一个要查看其有效安全规则的现有 VM。 如果没有 VM，请先部署 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 以完成本文中的任务。 本文中的示例适用于名为 *myVM* 的 VM，其中包含名为 *myVMVMNic* 的网络接口。 VM 和网络接口在名为 *myResourceGroup*、位于“美国东部”区域的资源组中。 针对想要诊断其问题的 VM，相应地更改步骤中的值。

## <a name="diagnose-using-azure-portal"></a>使用 Azure 门户诊断

1. 使用拥有[所需权限](virtual-network-network-interface.md#permissions)的 Azure 帐户登录到 Azure [门户](https://portal.azure.com)。
2. 在 Azure 门户顶部的搜索框中输入 VM 的名称。 当 VM 名称显示在搜索结果中时，请选择它。
3. 如下图所示，在“设置”下选择“网络”：

   ![查看安全规则](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   上图中列出的规则适用于名为 **myVMVMNic** 的网络接口。 可以看到两个不同网络安全组中网络接口的“入站端口规则”：
   
   - **mySubnetNSG**：已关联到网络接口所在的子网。
   - **myVMNSG**：已关联到 VM 中名为 **myVMVMNic** 的网络接口。

   如[场景](#scenario)中所述，名为 **DenyAllInBound** 的规则阻止端口 80 从 Internet 与 VM 进行入站通信。 规则中为“源”列出了 *0.0.0.0/0*，其中包括 Internet。 其他更高优先级（较小的数字）的规则都不允许端口 80 入站通信。 若要允许通过端口 80 从 Internet 与 VM 进行入站通信，请参阅[解决问题](#resolve-a-problem)。 若要详细了解安全规则以及 Azure 如何应用这些规则，请参阅[网络安全组](security-overview.md)。

   在图片底部，还可以看到“出站端口规则”。 其下面是网络接口的出站端口规则。 尽管图片中仅显示了每个 NSG 的四个入站规则，但 NSG 包含的规则可能远远超过四个。 在图片中“源”和“目标”下面可以看到“VirtualNetwork”，在“源”下面可以看到“AzureLoadBalancer”。 **VirtualNetwork** 和 **AzureLoadBalancer** 是[服务标记](security-overview.md#service-tags)。 服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。

4. 确保 VM 处于运行状态，然后如上图所示选择“有效安全规则”，以查看下图所示的有效安全规则：

   ![查看有效的安全规则](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   列出的规则与步骤 3 中相同，不过，与网络接口和子网关联的 NSG 有不同的选项卡。 图片中只显示了前 50 个规则。 若要下载包含所有规则的 .csv 文件，请选择“下载”。

   若要查看每个服务标记表示的前缀，请选择一个规则，例如名为 **AllowAzureLoadBalancerInbound** 的规则。 下图显示 **AzureLoadBalancer** 服务标记的前缀：

   ![查看有效的安全规则](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   尽管 **AzureLoadBalancer** 服务标记仅表示一个前缀，但其他服务标记表示多个前缀。

5. 前面的步骤显示了名为 **myVMVMNic** 的网络接口的安全规则，但前面某些图片中也显示了名为 **myVMVMNic2** 的网络接口。 本示例中的 VM 上附加了两个网络接口。 每个网络接口的有效安全规则可能不同。

   若要查看 **myVMVMNic2** 网络接口的规则，请选择它。 如下图所示，关联到网络接口子网的规则与 **myVMVMNic** 网络接口相同，因为这两个网络接口位于同一子网中。 将 NSG 关联到某个子网时，其规则将应用到该子网中的所有网络接口。

   ![查看安全规则](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   与 **myVMVMNic** 网络接口不同，**myVMVMNic2** 网络接口没有关联的网络安全组。 每个网络接口和子网可以有零个或一个关联的 NSG。 关联到每个网络接口或子网的 NSG 可以相同或不同。 可将同一网络安全组关联到选定的任意数量的网络接口和子网。

尽管前面是通过 VM 查看有效安全规则，但也可以通过以下各项查看有效安全规则：
- **单个网络接口**：了解如何[查看网络接口](virtual-network-network-interface.md#view-network-interface-settings)。
- **单个 NSG**：了解如何[查看 NSG](manage-network-security-group.md#view-details-of-a-network-security-group)。

## <a name="diagnose-using-powershell"></a>使用 PowerShell 诊断

可以在 [Azure Cloud Shell](https://shell.azure.com/powershell) 中运行以下命令，或者在计算机上运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 如果在计算机上运行 PowerShell，需要 *AzureRM* PowerShell 模块 6.0.1 或更高版本。 在计算机上运行 `Get-Module -ListAvailable AzureRM`，找到已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需要运行 `Login-AzureRmAccount`，以使用拥有[所需权限](virtual-network-network-interface.md#permissions)的帐户登录到 Azure。

使用 [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup) 获取网络接口的有效安全规则。 以下示例获取资源组 *myResourceGroup* 中名为 *myVMVMNic* 的网络接口的有效安全规则：

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic interface `
  -ResourceGroupName myResourceGroup
```

输出以 json 格式返回。 若要了解输出，请参阅[解释命令输出](#interpret-command-output)。
仅当 NSG 已关联到网络接口和/或该网络接口所在的子网时，才返回输出。 VM 必须处于运行状态。 一个 VM 可以包含多个应用了不同 NSG 的网络接口。 故障排除时，请针对每个网络接口运行该命令。

如果仍遇到连接问题，请参阅[其他诊断](#additional-diagnosis)和[注意事项](#considerations)。

如果不知道网络接口的名称，但知道网络接口所附加到的 VM 的名称，则运行以下命令会返回附加到 VM 的所有网络接口的 ID：

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

将会收到类似于以下示例的输出：

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

在前面的输出中，网络接口名称为 *myVMVMNic*。

## <a name="diagnose-using-azure-cli"></a>使用 Azure CLI 诊断

如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本文需要 Azure CLI 2.0.32 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需要运行 `az login`，并使用拥有[所需权限](virtual-network-network-interface.md#permissions)的帐户登录到 Azure。

使用 [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg) 获取网络接口的有效安全规则。 以下示例获取资源组 *myResourceGroup* 中名为 *myVMVMNic* 的网络接口的有效安全规则：

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

输出以 json 格式返回。 若要了解输出，请参阅[解释命令输出](#interpret-command-output)。
仅当 NSG 已关联到网络接口和/或该网络接口所在的子网时，才返回输出。 VM 必须处于运行状态。 一个 VM 可以包含多个应用了不同 NSG 的网络接口。 故障排除时，请针对每个网络接口运行该命令。

如果仍遇到连接问题，请参阅[其他诊断](#additional-diagnosis)和[注意事项](#considerations)。

如果不知道网络接口的名称，但知道网络接口所附加到的 VM 的名称，则运行以下命令会返回附加到 VM 的所有网络接口的 ID：

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

在返回的输出中，将会看到类似于以下示例的信息：

```azurecli
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

在前面的输出中，网络接口名称为 *myVMVMNic interface*。

## <a name="interpret-command-output"></a>解释命令输出

无论使用 [PowerShell](#diagnose-using-powershell) 还是 [Azure CLI](#diagnose-using-azure-cli) 诊断问题，都会收到包含以下信息的输出：

- **NetworkSecurityGroup**：网络安全组的 ID。
- **Association**：网络安全组是关联到 *NetworkInterface* 还是 *Subnet*。 如果 NSG 关联到两者，则返回的输出将包含每个 NSG 的 **NetworkSecurityGroup**、**Association** 和 **EffectiveSecurityRules**。 如果在关联或取消关联 NSG 之后紧接着运行此命令来查看有效安全规则，则可能需要等待几秒钟时间，更改才会反映在命令输出中。
- **EffectiveSecurityRules**：[创建安全规则](manage-network-security-group.md#create-a-security-rule)中详细解释了每个属性。 带有 *defaultSecurityRules/* 前缀的规则名称是每个 NSG 中存在的默认安全规则。 带有 *securityRules/* 前缀的规则名称是创建的规则。 为 **destinationAddressPrefix** 或 **sourceAddressPrefix** 属性指定[服务标记](security-overview.md#service-tags)（例如 **Internet**、**VirtualNetwork** 和 **AzureLoadBalancer**）的规则也包含 **expandedDestinationAddressPrefix** 属性的值。 **expandedDestinationAddressPrefix** 属性列出服务标记表示的所有地址前缀。

如果输出中列出了重复规则，原因是 NSG 同时关联到了网络接口和子网。 两个 NSG 具有相同的默认规则，如果在两个 NSG 创建相同的规则，则它们可能包含其他重复规则。

如[场景](#scenario)中所述，名为 **defaultSecurityRules/DenyAllInBound** 的规则阻止端口 80 从 Internet 与 VM 进行入站通信。 其他更高优先级（较小的数字）的规则都不允许通过端口 80 从 Internet 进行入站通信。

## <a name="resolve-a-problem"></a>解决问题

无论使用 Azure [门户](#diagnose-using-azure-portal)、[PowerShell](#diagnose-using-powershell) 还是 [Azure CLI](#diagnose-using-azure-cli) 来诊断本文[场景](#scenario)中所述的问题，解决方法都是创建具有以下属性的网络安全规则：

| 属性                | 值                                                                              |
|---------                |---------                                                                           |
| Source                  | 任意                                                                                |
| 源端口范围      | 任意                                                                                |
| 目标             | VM 的 IP 地址、IP 地址范围，或子网中的所有地址。 |
| 目标端口范围 | 80                                                                                 |
| 协议                | TCP                                                                                |
| 操作                  | 允许                                                                              |
| Priority                | 100                                                                                |
| 名称                    | Allow-HTTP-All                                                                     |

创建规则后，允许通过端口 80 从 Internet 进行入站通信，因为该规则的优先级高于名为 *DenyAllInBound* 的默认安全规则（拒绝流量）。 了解如何[创建安全规则](manage-network-security-group.md#create-a-security-rule)。 如果不同的 NSG 已关联到网络接口和子网，则必须在两个 NSG 中创建相同的规则。

当 Azure 处理入站流量时，会先处理关联到子网的 NSG 中的规则（如果有关联的 NSG），然后处理关联到网络接口的 NSG 中的规则。 如果有某个 NSG 关联到网络接口和子网，则必须在两个 NSG 中打开端口，使流量能够抵达 VM。 为便于解决管理和通信问题，我们建议将 NSG 关联到子网，而不要关联到单个网络接口。 如果子网中的 VM 需要不同的安全规则，可使网络接口成为应用程序安全组 (ASG) 的成员，并将某个 ASG 指定为安全规则的源和目标。 详细了解[应用程序安全组](security-overview.md#application-security-groups)。

如果仍然遇到通信问题，请参阅[注意事项](#considerations)和[其他诊断](#additional-dignosis)。

## <a name="considerations"></a>注意事项

排查连接问题时，请注意以下几点：

* 默认安全规则阻止来自 Internet 的入站访问，只允许来自虚拟网络的入站流量。 若要允许来自 Internet 的入站流量，请添加优先级高于默认规则的安全规则。 详细了解[默认安全规则](security-overview.md#default-security-rules)，或如何[添加安全规则](manage-network-security-group.md#create-a-security-rule)。
* 如果已创建对等互连的虚拟网络，则默认情况下，**VIRTUAL_NETWORK** 服务标记会自动扩展，以包含对等互连虚拟网络的前缀。 若要排查与虚拟网络对等连接相关的任何问题，可以查看 **ExpandedAddressPrefix** 列表中的前缀。 详细了解[虚拟网络对等互连](virtual-network-peering-overview.md)和[服务标记](security-overview.md#service-tags)。
* 仅当某个 NSG 已关联到 VM 的网络接口和/或子网，并且 VM 处于运行状态时，才显示网络接口的有效安全规则。
* 如果没有任何 NSG 关联到网络接口或子网，并且向 VM 分配了[公共 IP 地址](virtual-network-public-ip-address.md)，则会打开所有端口，以便在任意位置进行入站和出站访问。 如果 VM 有公共 IP 地址，我们建议将 NSG 应用到子网和网络接口。

## <a name="additional-diagnosis"></a>其他诊断

* 若要运行快速测试来确定是否允许传入或传出 VM 的流量，请使用 Azure 网络观察程序的 [IP 流验证](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)功能。 IP 流验证会告知是允许还是拒绝了流量。 如果被拒绝，IP 流验证会告知哪个安全规则拒绝了流量。
* 如果没有任何安全规则导致 VM 出现网络连接失败，则问题的原因可能是：
  * VM 操作系统中运行的防火墙软件
  * 为虚拟设备或本地流量配置的路由。 Internet 流量可以通过[强制隧道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)重定向到本地网络。 如果通过强制隧道将 Internet 流量定向到虚拟设备或本地，可能无法从 Internet 连接到 VM。 若要了解如何诊断可能影响了从 VM 传出的流量的路由问题，请参阅[诊断虚拟机网络流量路由问题](diagnose-network-routing-problem.md)。

## <a name="next-steps"></a>后续步骤

- 了解[网络安全组](manage-network-security-group.md#work-with-network-security-groups)和[安全规则](manage-network-security-group.md#work-with-security-rules)的所有任务、属性和设置。
- 了解 VM 的[默认安全规则](security-overview.md#default-security-rules)、[服务标记](security-overview.md#service-tags)，以及 [Azure 如何处理入站和出站流量的安全规则](security-overview.md#network-security-groups)。

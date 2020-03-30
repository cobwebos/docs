---
title: 将数据外泄限制为 Azure 存储 - Azure PowerShell
description: 在本文中，您将了解如何使用 Azure PowerShell 使用虚拟网络服务终结点策略限制和限制虚拟网络数据向 Azure 存储资源渗透。
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 673431e2ddfc9a641bb1c640891daac79350cb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78253021"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>使用 Azure PowerShell 使用虚拟网络服务终结点策略管理数据渗出到 Azure 存储帐户

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

虚拟网络服务终结点策略使您能够通过服务终结点从虚拟网络内对 Azure 存储帐户应用访问控制。 这是保护工作负载、管理允许哪些存储帐户以及允许数据渗漏的位置的关键。
在本文中，学习如何：

* 创建虚拟网络。
* 添加子网并为 Azure 存储启用服务终结点。
* 创建两个 Azure 存储帐户，并允许从上面创建的子网进行网络访问。
* 创建服务终结点策略，仅允许访问其中一个存储帐户。
* 将虚拟机 （VM） 部署到子网。
* 确认从子网对允许的存储帐户的访问。
* 确认从子网对不允许的存储帐户的访问被拒绝。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 PowerShell，则本文需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="create-a-virtual-network"></a>创建虚拟网络

创建虚拟网络之前，必须为虚拟网络创建资源组以及本文中创建的所有其他资源。 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建资源组。 以下示例创建名为 *myResourceGroup* 的资源组： 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 创建虚拟网络。 以下示例使用地址前缀 *10.0.0.0/16* 创建一个名为 *myVirtualNetwork* 的虚拟网络。

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>启用服务终结点

在虚拟网络中创建子网。 在此示例中，将创建一个包含用于 *Microsoft.Storage* 的服务终结点且名为 *Private* 的子网： 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>限制子网的网络访问

使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 创建网络安全组安全规则。 以下规则允许对分配给 Azure 存储服务的公共 IP 地址进行出站访问： 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

以下规则拒绝对所有公共 IP 地址的访问。 上一个规则将替代此规则，因为它的优先级更高，上一个规则允许对 Azure 存储的公共 IP 地址进行访问。

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

以下规则允许从任何位置到该子网的远程桌面协议 (RDP) 入站流量。 将允许到该子网的远程桌面连接，以便你可以在后面的步骤中确认对资源的网络访问。

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 创建网络安全组。 以下示例创建名为 *myNsgPrivate* 的网络安全组。

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

使用 [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) 将该网络安全组添加到 *Private* 子网，然后将子网配置写入到虚拟网络。 以下示例将 *myNsgPrivate* 网络安全组关联到 *Private* 子网：

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>限制对 Azure 存储帐户的网络访问

对于通过为服务终结点启用的 Azure 服务创建的资源，限制对其的网络访问时所需的步骤因服务而异。 请参阅各个服务的文档来了解适用于每个服务的具体步骤。 作为示例，本文的剩余部分包括了针对 Azure 存储帐户限制网络访问的步骤。

### <a name="create-two-storage-accounts"></a>创建两个存储帐户

使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 创建 Azure 存储帐户。

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

创建存储帐户后，使用 [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) 将存储帐户的密钥检索到一个变量中：

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

在后面的步骤中将使用此密钥来创建文件共享。 输入 `$storageAcctKey` 并记下值，因为你在后面的步骤中将文件共享映射到 VM 中的驱动器时还需要手动输入该值。

现在重复上述步骤以创建第二个存储帐户。

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

此外，还可以从此帐户中检索存储帐户密钥，以便以后使用来创建文件共享。

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>在每个存储帐户中创建文件共享

使用 [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext) 为存储帐户和密钥创建上下文。 该上下文封装了存储帐户名称和帐户密钥：

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

使用 [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare) 创建一个文件共享：

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>拒绝对存储帐户的所有网络访问

默认情况下，存储帐户接受来自任何网络中的客户端的网络连接。 若要仅允许所选的网络进行访问，请使用 [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset) 将默认操作更改为 *Deny*。 在拒绝网络访问后，将无法从任何网络访问存储帐户。

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>仅从 VNet 子网启用网络访问

使用 [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 检索所创建的虚拟网络，然后使用 [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) 将专用子网对象检索到一个变量中：

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

使用 [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig) 允许从 Private** 子网对存储帐户进行网络访问。

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>应用策略以允许访问有效的存储帐户

为了确保虚拟网络中的用户只能访问安全且允许的 Azure 存储帐户，可以在定义中创建包含允许存储帐户列表的服务终结点策略。 然后，此策略将应用于通过服务终结点连接到存储的虚拟网络子网。

### <a name="create-a-service-endpoint-policy"></a>创建服务终结点策略

本节创建策略定义，其中列出了允许的资源，以便通过服务终结点进行访问

检索第一个（允许的）存储帐户的资源 ID 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

创建策略定义以允许上述资源

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

使用上面创建的策略定义创建服务终结点策略

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>将服务终结点策略关联到虚拟网络子网

创建服务终结点策略后，您将将其与目标子网与 Azure 存储的服务终结点配置相关联。

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>验证对 Azure 存储帐户的访问限制

### <a name="deploy-the-virtual-machine"></a>部署虚拟机

要测试对存储帐户的网络访问，请将 VM 部署在子网中。

使用[New-AzVM](/powershell/module/az.compute/new-azvm)在*专用*子网中创建虚拟机。 运行以下命令时，会提示输入凭据。 输入的值将配置为用于 VM 的用户名和密码。 `-AsJob` 选项会在后台创建 VM，因此可继续执行下一步。

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

将返回类似于以下示例输出的输出：

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>确认对*允许*的存储帐户的访问

使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 返回 VM 的公共 IP 地址。 以下示例返回 *myVmPrivate* VM 的公共 IP 地址：

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

在以下命令中将 `<publicIpAddress>` 替换为前一个命令返回的公共 IP 地址，然后输入以下命令：

```powershell
mstsc /v:<publicIpAddress>
```

此时会创建远程桌面协议 (.rdp) 文件，并下载到计算机。 打开下载的 rdp 文件。 出现提示时，选择“连接”****。 输入在创建 VM 时指定的用户名和密码。 可能需要选择“更多选择”****，然后选择“使用其他帐户”****，以指定在创建 VM 时输入的凭据。 选择“确定”。 你可能会在登录过程中收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接。********

在*myVmPrivate* VM 上，从允许的存储帐户映射 Azure 文件共享，使用 PowerShell 驱动 Z。 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell 将返回类似于以下示例的输出：

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Azure 文件共享已成功映射到驱动器 Z。

关闭与 *myVmPrivate* VM 建立的远程桌面会话。

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>确认对*不允许的*存储帐户的访问被拒绝

在同一*myVmPrivate* VM 上，尝试将 Azure 文件共享映射到驱动器 X。 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

对该共享的访问被拒绝，并且将收到 `New-PSDrive : Access is denied` 错误。 访问被拒绝，因为不允许的存储*帐户不在*服务终结点策略中的允许资源列表中。 

关闭与 *myVmPublic* VM 建立的远程桌面会话。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 将其删除：

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

在本文中，您将服务终结点策略应用于 Azure 存储。" 您创建了 Azure 存储帐户，并且仅从虚拟网络子网对某些存储帐户（因此拒绝其他存储帐户）进行网络访问。 要了解有关服务终结点策略的详细信息，请参阅[服务终结点策略概述](virtual-network-service-endpoint-policies-overview.md)。

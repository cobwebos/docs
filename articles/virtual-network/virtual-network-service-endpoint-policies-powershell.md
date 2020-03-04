---
title: 将数据渗透限制为 Azure 存储-Azure PowerShell
description: 本文介绍如何使用 Azure PowerShell 通过虚拟网络服务终结点策略，将虚拟网络数据渗透限制和限制为 Azure 存储资源。
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
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253021"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>使用 Azure PowerShell 的虚拟网络服务终结点策略管理数据渗透到 Azure 存储帐户

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

利用虚拟网络服务终结点策略，你可以通过服务终结点在虚拟网络中应用对 Azure 存储帐户的访问控制。 这是保护工作负荷的关键，管理允许的存储帐户和允许数据渗透的位置。
在本文中，学习如何：

* 创建虚拟网络。
* 添加子网并启用 Azure 存储的服务终结点。
* 创建两个 Azure 存储帐户，并在上面创建的子网中允许对其进行网络访问。
* 创建服务终结点策略，只允许访问其中一个存储帐户。
* 将虚拟机（VM）部署到子网。
* 从子网确认对允许的存储帐户的访问权限。
* 确认拒绝从子网到不允许的存储帐户的访问。

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

## <a name="restrict-network-access-for-the-subnet"></a>限制对子网的网络访问

创建具有[AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)的网络安全组安全规则。 以下规则允许对分配给 Azure 存储服务的公共 IP 地址进行出站访问： 

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

通过[AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)将网络安全组关联到*专用*子网，然后将子网配置写入虚拟网络。 以下示例将 *myNsgPrivate* 网络安全组关联到 *Private* 子网：

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

使用[AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)创建 Azure 存储帐户。

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

创建存储帐户后，使用[AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey)将存储帐户的密钥检索到变量中：

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

在后面的步骤中将使用此密钥来创建文件共享。 输入 `$storageAcctKey` 并记下值，因为你在后面的步骤中将文件共享映射到 VM 中的驱动器时还需要手动输入该值。

现在请重复上述步骤以创建第二个存储帐户。

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

同时从此帐户中检索存储帐户密钥，以便以后使用来创建文件共享。

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>在每个存储帐户中创建文件共享

使用[AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext)为你的存储帐户和密钥创建上下文。 该上下文封装了存储帐户名称和帐户密钥：

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

使用[AzStorageShare](/powershell/module/az.storage/new-azstorageshare)创建文件共享：

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>拒绝对存储帐户的所有网络访问

默认情况下，存储帐户接受来自任何网络中的客户端的网络连接。 若要限制对所选网络的访问，请使用[AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)将默认操作更改为 "*拒绝*"。 在拒绝网络访问后，将无法从任何网络访问存储帐户。

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

使用[AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)检索创建的虚拟网络，然后使用[AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)将专用子网对象检索到变量中：

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

允许使用[AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig)的*专用*子网从网络访问存储帐户。

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

若要确保虚拟网络中的用户只能访问安全且允许的 Azure 存储帐户，你可以在定义中使用允许的存储帐户列表创建服务终结点策略。 然后，此策略将应用于通过服务终结点连接到存储的虚拟网络子网。

### <a name="create-a-service-endpoint-policy"></a>创建服务终结点策略

本部分使用允许访问服务终结点的资源列表创建策略定义

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

使用前面创建的策略定义创建服务终结点策略

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>将服务终结点策略关联到虚拟网络子网

创建服务终结点策略后，将其与 Azure 存储的服务终结点配置相关联的目标子网。

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

若要测试对存储帐户的网络访问，请在子网中部署 VM。

使用[new-azvm](/powershell/module/az.compute/new-azvm)在*专用*子网中创建虚拟机。 运行以下命令时，会提示输入凭据。 输入的值将配置为用于 VM 的用户名和密码。 `-AsJob` 选项会在后台创建 VM，因此可继续执行下一步。

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

### <a name="confirm-access-to-the-allowed-storage-account"></a>确认对*允许*的存储帐户的访问权限

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

此时会创建远程桌面协议 (.rdp) 文件，并下载到计算机。 打开下载的 rdp 文件。 出现提示时，选择“连接”。 输入在创建 VM 时指定的用户名和密码。 可能需要选择“更多选择”，然后选择“使用其他帐户”，以指定在创建 VM 时输入的凭据。 选择“确定”。 你可能会在登录过程中收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接。

在*myVmPrivate* VM 上，使用 PowerShell 将 Azure 文件共享从允许的存储帐户映射到驱动器 Z。 

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

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>确认拒绝对*不允许*的存储帐户的访问

在相同的*myVmPrivate* VM 上，尝试将 Azure 文件共享映射到驱动器 X。 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

对该共享的访问被拒绝，并且将收到 `New-PSDrive : Access is denied` 错误。 访问被拒绝，因为存储帐户*notallowedaccount*不在服务终结点策略的 "允许的资源" 列表中。 

关闭与 *myVmPublic* VM 建立的远程桌面会话。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 将其删除：

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

本文介绍如何通过 Azure 虚拟网络服务终结点将服务终结点策略应用于 Azure 存储。 已创建 Azure 存储帐户，并且只能从虚拟网络子网访问特定存储帐户（并因此被拒绝其他存储帐户）。 若要详细了解服务终结点策略，请参阅[服务终结点策略概述](virtual-network-service-endpoint-policies-overview.md)。

---
title: 限制对 PaaS 资源的网络访问-Azure PowerShell
description: 本文介绍如何使用 Azure PowerShell 通过虚拟网络服务终结点限制对 Azure 资源（例如 Azure 存储和 Azure SQL 数据库）的网络访问。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1d0cf65bb39dbda2b7451c50629ff8949c5507cb
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185540"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>使用 PowerShell 通过虚拟网络服务终结点限制对 PaaS 资源的网络访问

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

通过虚拟网络服务终结点，可将某些 Azure 服务资源限制为仅允许某个虚拟网络子网通过网络进行访问。 还可以删除对资源的 Internet 访问。 服务终结点提供从虚拟网络到受支持 Azure 服务的直接连接，使你能够使用虚拟网络的专用地址空间访问 Azure 服务。 通过服务终结点发往 Azure 资源的流量始终保留在 Microsoft Azure 主干网络上。 在本文中，学习如何：

* 创建包含一个子网的虚拟网络
* 添加子网并启用服务终结点
* 创建 Azure 资源并且仅允许从一个子网对其进行网络访问
* 将虚拟机 (VM) 部署到每个子网
* 确认从某个子网对资源的访问
* 确认已拒绝从某个子网和 Internet 来访问资源

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 PowerShell，则本文需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="create-a-virtual-network"></a>创建虚拟网络

创建虚拟网络之前，必须为虚拟网络创建资源组以及本文中创建的所有其他资源。 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建资源组。 以下示例创建名为 *myResourceGroup* 的资源组： 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 创建虚拟网络。 以下示例使用地址前缀 *10.0.0.0/16* 创建一个名为 *myVirtualNetwork* 的虚拟网络。

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 创建子网配置。 以下示例为名为 *Public* 的子网创建子网配置：

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

通过使用 [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) 将子网配置写入虚拟网络，在虚拟网络中创建子网：

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>启用服务终结点

只能为支持服务终结点的服务启用服务终结点。 使用 [Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice) 查看某个 Azure 位置中可用的启用了服务终结点的服务。 以下示例返回 *eastus* 区域中可用的启用了服务终结点的服务列表。 随着更多的 Azure 服务启用服务终结点，返回的服务列表将随时间增大。

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

在虚拟网络中创建另一个子网。 在此示例中，将创建一个包含用于 *Microsoft.Storage* 的服务终结点且名为 *Private* 的子网： 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>限制子网的网络访问

使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 创建网络安全组安全规则。 以下规则允许对分配给 Azure 存储服务的公用 IP 地址进行出站访问： 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

以下规则拒绝对所有公用 IP 地址的访问。 上一个规则将替代此规则，因为它的优先级更高，上一个规则允许对 Azure 存储的公用 IP 地址进行访问。

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
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

使用 *Set-AzVirtualNetworkSubnetConfig* 将该网络安全组添加到 [Private](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) 子网，然后将子网配置写入到虚拟网络。 以下示例将 *myNsgPrivate* 网络安全组关联到 *Private* 子网：

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>限制对资源的网络访问

对于通过为服务终结点启用的 Azure 服务创建的资源，限制对其的网络访问时所需的步骤因服务而异。 请参阅各个服务的文档来了解适用于每个服务的具体步骤。 作为示例，本文的剩余部分包括了针对 Azure 存储帐户限制网络访问的步骤。

### <a name="create-a-storage-account"></a>创建存储帐户

使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 创建 Azure 存储帐户。 将 `<replace-with-your-unique-storage-account-name>` 替换为在所有 Azure 位置中唯一的、长度为 3-24 个字符且仅使用数字和小写字母的名称。

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

创建存储帐户后，使用 [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) 将存储帐户的密钥检索到一个变量中：

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

在后面的步骤中将使用此密钥来创建文件共享。 输入 `$storageAcctKey` 并记下值，因为你在后面的步骤中将文件共享映射到 VM 中的驱动器时还需要手动输入该值。

### <a name="create-a-file-share-in-the-storage-account"></a>在存储帐户中创建文件共享

使用 [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext) 为存储帐户和密钥创建上下文。 该上下文封装了存储帐户名称和帐户密钥：

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

使用 [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare) 创建一个文件共享：

$share = New-AzStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>拒绝对存储帐户的所有网络访问

默认情况下，存储帐户接受来自任何网络中的客户端的网络连接。 若要仅允许所选的网络进行访问，请使用 *Update-AzStorageAccountNetworkRuleSet* 将默认操作更改为 [Deny](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)。 在拒绝网络访问后，将无法从任何网络访问存储帐户。

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>启用从子网的网络访问

使用 [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 检索所创建的虚拟网络，然后使用 [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) 将专用子网对象检索到一个变量中：

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

使用 *Add-AzStorageAccountNetworkRule* 允许从 Private[](/powershell/module/az.network/add-aznetworksecurityruleconfig) 子网对存储帐户进行网络访问。

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>创建虚拟机

若要测试对存储帐户的网络访问，请向每个子网部署 VM。

### <a name="create-the-first-virtual-machine"></a>创建第一台虚拟机

使用 *New-AzVM* 在 Public[](/powershell/module/az.compute/new-azvm) 子网中创建虚拟机。 运行以下命令时，会提示输入凭据。 输入的值将配置为用于 VM 的用户名和密码。 `-AsJob` 选项会在后台创建 VM，因此可继续执行下一步。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

将返回类似于以下示例输出的输出：

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>创建第二台虚拟机

在 *Private* 子网中创建一台虚拟机：

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Azure 需要花费几分钟时间来创建 VM。 在 Azure 完成创建 VM 并将输出返回到 PowerShell 之前，请不要继续执行下一步骤。

## <a name="confirm-access-to-storage-account"></a>确认对存储帐户的访问

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

在 *myVmPrivate* VM 上，使用 PowerShell 将 Azure 文件共享映射到驱动器 Z。 在运行下面的命令之前，将 `<storage-account-key>` 和 `<storage-account-name>` 替换为在[创建存储帐户](#create-a-storage-account)中提供或检索的值。

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell 将返回类似于以下示例的输出：

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Azure 文件共享已成功映射到驱动器 Z。

确认 VM 没有到任何其他公用 IP 地址的出站连接：

```powershell
ping bing.com
```

你不会收到回复，因为除了分配给 Azure 存储服务的地址之外，关联到 *Private* 子网的网络安全组不允许对其他公用 IP 地址的出站访问。

关闭与 *myVmPrivate* VM 建立的远程桌面会话。

## <a name="confirm-access-is-denied-to-storage-account"></a>确认已拒绝对存储帐户的访问

获取 *myVmPublic* VM 的公共 IP 地址：

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

在以下命令中将 `<publicIpAddress>` 替换为前一个命令返回的公共 IP 地址，然后输入以下命令： 

```powershell
mstsc /v:<publicIpAddress>
```

在 *myVmPublic* VM 上，尝试将 Azure 文件共享映射到驱动器 Z。在运行下面的命令之前，将 `<storage-account-key>` 和 `<storage-account-name>` 替换为在[创建存储帐户](#create-a-storage-account)中提供或检索的值。

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

对该共享的访问被拒绝，并且将收到 `New-PSDrive : Access is denied` 错误。 访问被拒绝，因为 *myVmPublic* VM 部署在 *Public* 子网中。 *Public* 子网没有为 Azure 存储启用服务终结点，并且存储帐户仅允许来自 *Private* 子网的网络访问，不允许来自 *Public* 子网的网络访问。

关闭与 *myVmPublic* VM 建立的远程桌面会话。

从计算机中，尝试使用以下命令查看存储帐户中的文件共享：

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

拒绝访问，并收到*AzStorageFile：远程服务器返回错误：（403）禁止访问。HTTP 状态代码： 403-HTTP 错误消息：此请求无权执行此操作*错误，因为你的计算机不在*MyVirtualNetwork*虚拟网络的*专用*子网中。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 将其删除：

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

在本文中，已为虚拟网络子网启用了服务终结点。 我们已了解，可为通过多个 Azure 服务部署的资源启用服务终结点。 我们创建了一个 Azure 存储帐户并将该存储帐户限制为仅可供某个虚拟网络子网中的资源进行网络访问。 若要详细了解服务终结点，请参阅[服务终结点概述](virtual-network-service-endpoints-overview.md)和[管理子网](virtual-network-manage-subnet.md)。

如果帐户中有多个虚拟网络，可将两个虚拟网络连接到一起，使每个虚拟网络中的资源可以相互通信。 若要了解如何操作，请参阅[连接虚拟网络](tutorial-connect-virtual-networks-powershell.md)。

---
ms.assetid: ''
title: 配置 Azure Key Vault 防火墙和虚拟网络
description: 配置 Key Vault 防火墙和虚拟网络的分步说明
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: 6315434c1e8acc82e02f5c9e5ae8ab2d1cacc887
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302047"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>配置 Azure Key Vault 防火墙和虚拟网络

本指南分步介绍如何配置 Key Vault 防火墙和虚拟网络，以限制对密钥保管库的访问。 通过 [Key Vault 的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)可将密钥保管库的访问限制为指定虚拟网络和/或一组 IPv4（Internet 协议版本 4）地址范围。

> [!IMPORTANT]
> 防火墙和虚拟网络规则生效后，只在调用者请求来自允许的虚拟网络或 IPV4 地址范围时，才能执行所有 Key Vault [数据平面](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)操作。 从 Azure 门户访问密钥保管库时，这同样适用。 虽然用户可从 Azure 门户浏览到密钥保管库，但如果其客户端计算机不在允许列表中，则可能无法列出密钥/机密/证书。 这也会影响其他 Azure 服务的“Key Vault 选取器”。 如果防火墙规则阻止了用户的客户端计算机，则用户可以查看密钥保管库列表，但不能查看列表密钥。

## <a name="azure-portal"></a>Azure 门户

1. 导航到要保护的密钥保管库。
2. 单击“防火墙和虚拟网络”。
3. 单击“允许的访问来源”下的“所选网络”。
4. 若要将现有虚拟网络添加到防火墙和虚拟网络规则，请单击“+ 添加现有虚拟网络”。
5. 在弹出的新边栏选项卡中，选择可访问此密钥保管库的订阅、虚拟网络和子网。 如果所选虚拟网络和子网未启用服务终结点，则会出现一条说明“以下网络未启用服务终结点...”的消息。 确认要为列出的虚拟网络和子网启用服务终结点后，单击“启用”。 此操作最多可能需要 15 分钟才能生效。
6. 还可添加新的虚拟网络和子网，然后通过单击“+ 添加新的虚拟网络”并按照后续提示操作，为新创建的虚拟网络和子网启用服务终结点。
7. 在“IP 网络”下，可通过采用 [CIDR（无类域间路由）表示法](https://tools.ietf.org/html/rfc4632)键入 IPv4 地址范围或单个 IP 地址来添加 IPv4 地址范围。
8. 单击“ **保存**”。

## <a name="azure-cli-20"></a>Azure CLI 2.0

1. [安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 并[登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)。

2. 列出可用虚拟网络规则，如果尚未为此密钥保管库设置任何规则，则列表为空。
```azurecli
az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
```

3. 在现有虚拟网络和子网上启用 Key Vault 的服务终结点
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
```

4. 为虚拟网络和子网添加网络规则
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
```

5. 添加 IP 地址范围，确定允许的流量来源
```azurecli
az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
```

6. 如果所有受信服务都需要访问此密钥保管库，请将“绕过”设置为 AzureServices
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. 最后一步，也是重要的一步，将默认操作设置为“拒绝”，以启用网络规则
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Azure PowerShell

1. 安装最新的 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) 并[登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。

2. 列出可用虚拟网络规则，如果尚未为此密钥保管库设置任何规则，则列表为空。
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. 在现有虚拟网络和子网上启用 Key Vault 的服务终结点
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. 为虚拟网络和子网添加网络规则
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. 添加 IP 地址范围，确定允许的流量来源
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. 如果所有受信服务都需要访问此密钥保管库，请将“绕过”设置为 AzureServices
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. 最后一步，也是重要的一步，将默认操作设置为“拒绝”，以启用网络规则
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>参考

* Azure CLI 2.0 命令 - [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell cmdlet - [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault)、[Add-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule)、[Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule)[Update-AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>后续步骤

* [Key Vault 的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)
* [保护密钥保管库](key-vault-secure-your-key-vault.md)
---
title: 配置 Azure 密钥保管库防火墙和虚拟网络 - Azure 密钥保管库
description: 配置 Key Vault 防火墙和虚拟网络的分步说明
services: key-vault
author: amitbapat
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: a6f2e899e8be39abdefaf9d4f524eae457673c1a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694407"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>配置 Azure Key Vault 防火墙和虚拟网络

本文分步介绍如何配置 Azure Key Vault 防火墙和虚拟网络，以限制对 Key Vault 的访问。 通过 [Key Vault 的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)可将访问限制为指定虚拟网络和一系列 IPv4（Internet 协议版本 4）地址范围。

> [!IMPORTANT]
> 防火墙规则生效后，只在用户请求来自允许的虚拟网络或 IPv4 地址范围时，才能执行 Key Vault [数据平面](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)操作。 从 Azure 门户访问 Key Vault 时，这同样适用。 虽然用户可从 Azure 门户浏览到 Key Vault，但如果其客户端计算机不在允许列表中，则可能无法列出密钥、机密或证书。 这也会影响其他 Azure 服务的 Key Vault 选取器。 如果防火墙规则阻止了用户的客户端计算机，则用户可以查看 Key Vault 列表，但不能查看列表密钥。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

下面介绍了如何使用 Azure 门户配置 Key Vault 防火墙和虚拟网络：

1. 浏览要保护的 Key Vault。
2. 选择“防火墙和虚拟网络”。
3. 在“允许的访问来源”下，选择“所选网络”。
4. 若要将现有虚拟网络添加到防火墙和虚拟网络规则，请选择“+ 添加现有虚拟网络”。
5. 在打开的新边栏选项卡中，选择可访问此 Key Vault 的订阅、虚拟网络和子网。 如果虚拟网络和选择的子网没有启用服务终结点，确认想要启用服务终结点，并选择“启用”。 此操作最多可能需要 15 分钟才能生效。
6. 在“IP 网络”下，可通过采用 [CIDR（无类域间路由）表示法](https://tools.ietf.org/html/rfc4632)键入 IPv4 地址范围或单个 IP 地址来添加 IPv4 地址范围。
7. 选择“保存”。

还可添加新的虚拟网络和子网，然后通过选择“+ 添加新的虚拟网络”，为新创建的虚拟网络和子网启用服务终结点。 然后遵照提示操作。

## <a name="use-the-azure-cli"></a>使用 Azure CLI 

下面介绍了如何使用 Azure CLI 配置 Key Vault 防火墙和虚拟网络

1. [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 并[登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)。

2. 列出可用的虚拟网络规则。 如果尚未设置此 Key Vault 的任何规则，该列表将为空。
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. 在现有虚拟网络和子网上启用 Key Vault 的服务终结点。
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. 为虚拟网络和子网添加网络规则。
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. 添加允许通信的 IP 地址范围。
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. 如果所有受信服务都可以访问此 Key Vault，请将 `bypass` 设置为 `AzureServices`。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. 将默认操作设置为 `Deny`，以启用网络规则。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下面介绍了如何使用 PowerShell 配置 Key Vault 防火墙和虚拟网络：

1. 安装最新的 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 并[登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。

2. 列出可用的虚拟网络规则。 如果尚未设置此密钥保管库的任何规则，该列表将为空。
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. 在现有虚拟网络和子网上启用 Key Vault 的服务终结点。
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. 为虚拟网络和子网添加网络规则。
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. 添加允许通信的 IP 地址范围。
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. 如果所有受信服务都可以访问此 Key Vault，请将 `bypass` 设置为 `AzureServices`。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. 将默认操作设置为 `Deny`，以启用网络规则。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>参考

* Azure CLI 命令：[az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell cmdlets：[Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>后续步骤

* [Key Vault 的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)
* [保护密钥保管库](key-vault-secure-your-key-vault.md)

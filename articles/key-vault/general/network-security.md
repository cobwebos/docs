---
title: 配置 Azure 密钥保管库防火墙和虚拟网络 - Azure 密钥保管库
description: 配置 Key Vault 防火墙和虚拟网络的分步说明
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8617b0b71e58d22ccd2cf753e4ddc862932f68da
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536049"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>配置 Azure Key Vault 防火墙和虚拟网络

本文分步介绍如何配置 Azure Key Vault 防火墙和虚拟网络，以限制对 Key Vault 的访问。 通过 [Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)可将访问限制为指定虚拟网络和一系列 IPv4（Internet 协议版本 4）地址范围。

> [!IMPORTANT]
> 防火墙规则生效后，只在用户请求来自允许的虚拟网络或 IPv4 地址范围时，才能执行 Key Vault [数据平面](secure-your-key-vault.md#data-plane-access-control)操作。 从 Azure 门户访问 Key Vault 时，这同样适用。 虽然用户可从 Azure 门户浏览到 Key Vault，但如果其客户端计算机不在允许列表中，则可能无法列出密钥、机密或证书。 这也会影响其他 Azure 服务的 Key Vault 选取器。 如果防火墙规则阻止了用户的客户端计算机，则用户可以查看 Key Vault 列表，但不能查看列表密钥。

> [!NOTE]
> 注意以下配置限制：
> * 最多允许 127 条虚拟网络规则和 127 条 IPv4 规则。 
> * 不支持使用“/31”或“/32”前缀大小的小型地址范围。 改为使用单独的 IP 地址规则配置这些范围。
> * IP 网络规则仅适用于公共 IP 地址。 IP 规则不允许为专用网络保留的 IP 地址范围（如 RFC 1918 中所定义）。 专用网络包括以 **10.** 、**172.16-31** 和 **192.168.** 开头的地址。 
> * 目前仅支持 IPv4 地址。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

下面介绍了如何使用 Azure 门户配置 Key Vault 防火墙和虚拟网络：

1. 浏览要保护的 Key Vault。
2. 选择“网络”，然后选择“防火墙和虚拟网络”信息栏 。
3. 在“允许的访问来源”下，选择“所选网络”。
4. 若要将现有虚拟网络添加到防火墙和虚拟网络规则，请选择“+ 添加现有虚拟网络”。
5. 在打开的新边栏选项卡中，选择可访问此 Key Vault 的订阅、虚拟网络和子网。 如果虚拟网络和选择的子网没有启用服务终结点，确认想要启用服务终结点，并选择“启用”。 此操作最多可能需要 15 分钟才能生效。
6. 在“IP 网络”下，可通过采用 [CIDR（无类域间路由）表示法](https://tools.ietf.org/html/rfc4632)键入 IPv4 地址范围或单个 IP 地址来添加 IPv4 地址范围。
7. 如果要允许 Microsoft 信任的服务跳过 Key Vault 防火墙，请选择“是”。 有关当前 Key Vault 信任的服务的完整列表，请参阅以下链接。 [Azure Key Vault 信任的服务](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
7. 选择“保存” 。

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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
* ARM 模板参考：[Azure Key Vault ARM 模板参考](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI 命令：[az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell cmdlets：[Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>后续步骤

* [Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)
* [保护密钥保管库](secure-your-key-vault.md)

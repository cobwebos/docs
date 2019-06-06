---
title: 使用 Azure PowerShell 加密 Azure 规模集磁盘 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 来加密 Windows 虚拟机规模集中的 VM 实例和附加的磁盘
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: cynthn
ms.openlocfilehash: a582a4787a4b215d82dcbff60be8853793f92c32
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728372"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>加密 OS 磁盘和附加的数据磁盘中的虚拟机规模集使用 Azure PowerShell

虚拟机规模集支持 Azure 磁盘加密 (ADE)，可让用户使用行业标准的加密技术来保护静态数据。 可为 Windows 和 Linux 虚拟机规模集启用加密。 有关详细信息，请参阅[适用于 Windows 和 Linux 的 Azure 磁盘加密](../security/azure-security-disk-encryption.md)。

下述项支持 Azure 磁盘加密：
- 使用托管磁盘创建的规模集，本机（或未托管）磁盘规模集不受支持。
- Windows 规模集中的 OS 和数据卷。 可对 Windows 规模集的 OS 和数据卷禁用加密功能。
- Linux 规模集中的数据卷。 对于 Linux 规模集目前不支持 OS 磁盘加密。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>创建支持磁盘加密的 Azure Key Vault

Azure 密钥保管库可以存储能够在应用程序和服务中安全实现的密钥、机密或密码。 使用软件保护将加密密钥存储在 Azure 密钥保管库中，或者，可在已通过 FIPS 140-2 级别 2 标准认证的硬件安全模块 (HSM) 中导入或生成密钥。 这些加密密钥用于加密和解密附加到 VM 的虚拟磁盘。 可以控制这些加密密钥，以及审核对它们的使用。

使用 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) 创建一个密钥保管库。 若要将 Key Vault 用于磁盘加密，请设置 *EnabledForDiskEncryption* 参数。 以下示例还会定义资源组名称、Key Vault 名称和位置的变量。 请提供自己的唯一 Key Vault 名称：

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>使用现有的密钥保管库

仅当你要将现有的 Key Vault 用于磁盘加密时，才需要执行此步骤。 如果在上一部分中创建了 Key Vault，请跳过此步骤。

可以使用 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy)，在规模集所在的同一订阅和区域中启用现有的 Key Vault 进行磁盘加密。 按如下所示，在 *$vaultName* 变量中定义现有 Key Vault 的名称：


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>创建规模集

首先，使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 实例的管理员用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```

现在，使用 [New-AzVmss](/powershell/module/az.compute/new-azvmss) 创建虚拟机规模集。 若要将流量分配到单独的 VM 实例，则还要创建负载均衡器。 负载均衡器包含的规则可在 TCP 端口 80 上分配流量，并允许 TCP 端口 3389 上的远程桌面流量，以及 TCP 端口 5985 上的 PowerShell 远程流量：

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>启用加密功能

若要在规模集中加密 VM 实例，请先使用 [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault) 获取有关 Key Vault URI 和资源 ID 的某些信息。 然后，可以使用 [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension) 结合这些变量来启动加密过程：


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

出现提示时，请键入 *y*，以便继续对规模集 VM 实例执行磁盘加密过程。

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>使用 KEK 包装密钥启用加密

加密虚拟机规模集时，还可以增强安全性使用密钥加密密钥。

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

> [!NOTE]
>  磁盘加密 keyvault 参数的值的语法是完整标识符字符串：</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> 密钥加密密钥参数的值的语法是作为 in KEK 的完整 URI:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>查看加密进度

若要检查磁盘加密状态，请使用 [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption)：


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

加密 VM 实例后，*EncryptionSummary* 代码会报告 *ProvisioningState/succeeded*，如以下示例输出所示：

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>禁用加密功能

如果不再希望使用加密的 VM 实例磁盘，可以使用 [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) 禁用加密，如下所示：


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>后续步骤

- 在本文中，我们已使用 Azure PowerShell 加密了虚拟机规模集。 也可以使用 [Azure CLI](virtual-machine-scale-sets-encrypt-disks-cli.md)，或适用于 [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) 或 [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) 的模板。
- 如果您想要拥有 Azure 磁盘加密应用预配另一个扩展后，可以使用[扩展序列化](virtual-machine-scale-sets-extension-sequencing.md)。 可以使用[这些示例](../security/azure-security-disk-encryption-extension-sequencing.md#sample-azure-templates)若要开始。

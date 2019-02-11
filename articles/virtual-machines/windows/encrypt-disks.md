---
title: 加密 Azure Windows VM 上的磁盘 | Microsoft Docs
description: 使用 Azure PowerShell 加密 Windows VM 上的虚拟磁盘以增强安全性
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 738bdd303d6e8e41df179021ebca521100ace325
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828071"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>加密 Windows VM 上的虚拟磁盘
为了增强虚拟机 (VM) 的安全性以及符合性，可以加密 Azure 中的虚拟磁盘。 磁盘是使用 Azure Key Vault 中受保护的加密密钥加密的。 可以控制这些加密密钥，以及审核对它们的使用。 本文介绍如何使用 Azure PowerShell 加密 Windows VM 上的虚拟磁盘。 还可[使用 Azure CLI 加密 Linux VM](../linux/encrypt-disks.md)。

## <a name="overview-of-disk-encryption"></a>磁盘加密概述
Windows VM 上的虚拟磁盘使用 BitLocker 进行静态加密。 加密 Azure 中的虚拟磁盘不会产生费用。 使用软件保护将加密密钥存储在 Azure Key Vault 中，或者，可在已通过 FIPS 140-2 级别 2 标准认证的硬件安全模块 (HSM) 中导入或生成密钥。 加密密钥用于加密和解密附加到 VM 的虚拟磁盘。 可以控制这些加密密钥，以及审核对它们的使用。 

加密 VM 的过程如下：

1. 在 Azure 密钥保管库中创建加密密钥。
1. 配置可用于加密磁盘的加密密钥。
1. 为虚拟磁盘启用磁盘加密。
1. 从 Azure Key Vault 请求所需的加密密钥。
1. 使用提供的加密密钥加密虚拟磁盘。


## <a name="requirements-and-limitations"></a>要求和限制

磁盘加密支持的方案和要求

* 在来自 Azure 市场映像或自定义 VHD 映像的新 Windows VM 上启用加密。
* 在现有的 Azure Windows VM 上启用加密。
* 在使用存储空间配置的 Windows VM 上启用加密。
* 在 Windows VM 的 OS 和数据驱动器上禁用加密。
* 标准层 VM，例如 A、D、DS、G 和 GS 系列 VM。

    > [!NOTE]
    > 所有资源（包括 Key Vault、存储帐户和 VM）必须在同一个 Azure 区域和订阅中。

以下方案目前不支持磁盘加密：

* 基本层 VM。
* 使用经典部署模型创建的 VM。
* 在已加密的 VM 上更新加密密钥。
* 与本地密钥管理服务集成。


## <a name="create-an-azure-key-vault-and-keys"></a>创建 Azure Key Vault 和密钥
在开始之前，请确保已安装了 Azure PowerShell 模块的最新版本。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 在以下命令示例中，请将所有示例参数（例如 *myResourceGroup*、*myKeyVault*、*myVM* 等等）替换为自己的名称、位置和密钥值。

第一步是创建用于存储加密密钥的 Azure 密钥保管库。 Azure Key Vault 可以存储能够在应用程序和服务中安全实现的密钥、机密或密码。 对于虚拟磁盘加密，可以创建 Key Vault 来存储用于加密或解密虚拟磁盘的加密密钥。 

在 Azure 订阅中使用 [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) 启用 Azure Key Vault 提供程序，然后使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建一个资源组。 以下示例在美国东部位置创建名为 myResourceGroup 的资源组：

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

包含加密密钥和关联的计算资源（例如存储和 VM 本身）的 Azure Key Vault 必须位于同一区域。 使用 [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) 创建 Azure Key Vault，并启用该 Key Vault 进行磁盘加密。 指定 keyVaultName 的唯一 Key Vault 名称，如下所示：

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

可以使用软件或硬件安全模型 (HSM) 保护来存储加密密钥。  标准 Key Vault 仅存储受软件保护的密钥。 使用 HSM 时需要高级 Key Vault，这会产生额外的费用。 若要创建高级 Key Vault，请在上一步中添加 *-Sku "Premium"* 参数。 由于我们创建的是标准密钥保管库，以下示例使用了受软件保护的密钥。 

对于这两种保护模型，在启动 VM 解密虚拟磁盘时，都需要向 Azure 平台授予请求加密密钥的访问权限。 使用 [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) 在 Key Vault 中创建加密密钥。 以下示例创建名为 myKey 的密钥：

```azurepowershell-interactive
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>创建虚拟机
要测试加密过程，请使用 [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) 创建 VM。 以下示例使用 Windows Server 2016 Datacenter 映像创建名为 myVM 的 VM。 系统提示输入凭据时，请输入用于 VM 的用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>加密虚拟机
使用 [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) 和 Azure Key Vault 密钥加密 VM。 以下示例将检索所有密钥信息，然后对名为 myVM 的 VM 进行加密：

```azurepowershell-interactive
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

接受提示以继续进行 VM 加密。 此过程中将重启 VM。 加密过程完成并重启 VM 后，使用 [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) 查看加密状态：

```azurepowershell-interactive
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

输出类似于以下示例：

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>后续步骤
* 有关 Azure Key Vault 管理的详细信息，请参阅[为虚拟机设置 Key Vault](key-vault-setup.md)。
* 有关磁盘加密的详细信息，例如准备要上载到 Azure 的已加密自定义 VM，请参阅 [Azure Disk Encryption](../../security/azure-security-disk-encryption.md)\（Azure 磁盘加密）。

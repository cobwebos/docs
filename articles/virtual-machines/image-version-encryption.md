---
title: 预览-创建使用自己的密钥加密的映像版本
description: 使用客户托管的加密密钥，在共享映像库中创建映像版本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/06/2020
ms.author: cynthn
ms.openlocfilehash: aeacfdc07e5349dfce45b209da1d78bddf870f33
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83269574"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>预览：使用客户托管的密钥加密映像

库映像存储为托管磁盘，因此使用服务器端加密自动对它们进行加密。 服务器端加密使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，这是一个最强的可用块密码，符合 FIPS 140-2。 有关 Azure 托管磁盘底层加密模块的详细信息，请参见[加密 API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

你可以依赖于平台管理的密钥来加密映像，也可以使用自己的密钥来管理加密。 如果选择使用自己的密钥管理加密，则可以指定*客户托管的密钥*，用于加密和解密映像中的所有磁盘。 

使用客户托管密钥的服务器端加密使用 Azure Key Vault。 您可以将[您的 rsa 密钥](../key-vault/keys/hsm-protected-keys.md)导入 Key Vault 或在 Azure Key Vault 中生成新的 rsa 密钥。

若要将客户托管密钥用于映像，首先需要 Azure Key Vault。 然后创建磁盘加密集。 然后在创建映像版本时使用磁盘加密集。

有关创建和使用磁盘加密集的详细信息，请参阅[客户托管密钥](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)。

## <a name="limitations"></a>限制

使用客户托管密钥加密共享映像库映像时，有几个限制：  

- 加密密钥集必须与映像在同一订阅和区域中。

- 你无法共享使用客户托管密钥的映像。 

- 不能将使用客户托管密钥的映像复制到其他区域。

- 使用自己的密钥来加密磁盘或映像后，不能返回到使用平台管理的密钥来加密这些磁盘或映像。


> [!IMPORTANT]
> 使用客户托管的密钥进行加密目前处于公共预览阶段。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="powershell"></a>PowerShell

对于公共预览版，首先需要注册该功能。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

完成注册需要几分钟时间。 使用 AzProviderFeature 检查功能注册的状态。

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

当 RegistrationState 返回 "已注册" 时，你可以转到下一步。

请检查提供程序注册。 确保返回 `Registered` 。

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

如果未返回 `Registered` ，请使用以下内容来注册提供程序：

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

若要为映像版本指定磁盘加密设置为，请将[AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)与参数一起使用 `-TargetRegion` 。 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @{$region1}


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>创建 VM

可以从共享映像库创建 VM，并使用客户管理的密钥来加密磁盘。 语法与从映像创建[通用化](vm-generalized-image-version-powershell.md)或[专用](vm-specialized-image-version-powershell.md)VM 相同，需要使用扩展参数集，并将其添加 `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` 到 VM 配置。

对于数据磁盘，你需要 `-DiskEncryptionSetId $setID` 在使用[AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk)时添加参数。


## <a name="cli"></a>CLI 

对于公共预览版，首先需要注册该功能。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

检查功能注册的状态。

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

返回后 `"state": "Registered"` ，可以继续执行下一步。

检查你的注册。

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

如果未注册，请运行以下内容：

```azurecli-interactive
az provider register -n Microsoft.Compute
```


若要为映像版本指定磁盘加密设置为，请将[az image 画廊 create-image 版本](/cli/azure/sig/image-version#az-sig-image-version-create)与参数一起使用 `--target-region-encryption` 。 的格式 `--target-region-encryption` 是用于对 OS 和数据磁盘进行加密的以空格分隔的密钥列表。 它应如下所示： `<encryption set for the OS disk>,<Lun number of the data disk>, <encryption set for the data disk>, <Lun number for the second data disk>, <encryption set for the second data disk>`。 

如果 OS 磁盘的源是托管磁盘或 VM，请使用 `--managed-image` 指定映像版本的源。 在此示例中，源是包含 OS 磁盘的托管映像以及 LUN 0 上的数据磁盘。 OS 磁盘将用 DiskEncryptionSet1 加密，数据磁盘将通过 DiskEncryptionSet2 进行加密。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

如果 OS 磁盘的源是快照，请使用 `--os-snapshot` 来指定 os 磁盘。 如果有还应作为映像版本一部分的数据磁盘快照，请添加使用来指定 LUN 的数据磁盘快照 `--data-snapshot-luns` ，并 `--data-snapshots` 指定快照。

在此示例中，源是磁盘快照。 有一个操作系统磁盘，还存在一个位于 LUN 0 上的数据磁盘。 OS 磁盘将用 DiskEncryptionSet1 加密，数据磁盘将通过 DiskEncryptionSet2 进行加密。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot"
   --data-snapshot-luns 0
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot"
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>创建 VM

可以从共享映像库创建 VM，并使用客户管理的密钥来加密磁盘。 语法与从映像创建[通用化](vm-generalized-image-version-cli.md)或[专用](vm-specialized-image-version-cli.md)VM 相同，只需添加 `--os-disk-encryption-set` 具有加密集的 ID 的参数。 对于数据磁盘，请添加 `--data-disk-encryption-sets` 以空格分隔的数据磁盘磁盘加密集列表。


## <a name="portal"></a>门户

在门户中创建映像版本时，可以使用 "**加密**" 选项卡输入有关存储加密集的信息。

1. 在 "**创建映像版本**" 页上，选择 "**加密**" 选项卡。
2. 在 "**加密类型**" 中，选择 "**使用客户管理的密钥进行静态加密**"。 
3. 对于映像中的每个磁盘，从下拉菜单中选择要使用的**磁盘加密集**。 

### <a name="create-the-vm"></a>创建 VM

可以从共享映像库创建 VM，并使用客户管理的密钥来加密磁盘。 当你在门户中创建 VM 时，请在 "**磁盘**" 选项卡上，选择 "静态加密" 以及 "**加密类型**的**客户托管密钥**"。 然后，可以从下拉端选择加密集。

## <a name="next-steps"></a>后续步骤

了解有关[服务器端磁盘加密](/windows/disk-encryption.md)的详细信息。

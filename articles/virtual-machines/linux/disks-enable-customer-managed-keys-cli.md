---
title: Azure CLI-通过 SSE 托管磁盘启用客户管理的密钥
description: 使用 Azure CLI 在托管磁盘上启用客户管理的密钥。
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 2acc95190454de2ffdd81a05a6e6cf166a9a6549
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814122"
---
# <a name="use-the-azure-cli-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>使用 Azure CLI 对托管磁盘的客户托管密钥启用服务器端加密

Azure 磁盘存储使你能在对托管磁盘使用服务器端加密 (SSE) 时管理自己的密钥（如果你选择）。 有关使用客户管理的密钥的 SSE 以及其他托管磁盘加密类型的概念信息，请参阅磁盘加密文章的[客户管理的密钥](disk-encryption.md#customer-managed-keys)部分。

## <a name="restrictions"></a>限制

目前，客户托管密钥具有以下限制：

- 如果为磁盘启用了此功能，则无法禁用它。
    如果需要解决此问题，则必须[复制所有数据](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)到完全不同的托管磁盘（未使用客户托管密钥）。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="set-up-your-azure-key-vault-and-diskencryptionset"></a>设置 Azure Key Vault 和 DiskEncryptionSet 资源

首先，必须设置 Azure Key Vault 和 diskencryptionset 资源。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

创建并配置这些资源之后，可以使用它们来保护托管磁盘。 以下链接包含示例脚本，每个脚本都有各自的方案，可用于保护托管磁盘。

## <a name="examples"></a>示例

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用市场映像创建 VM，并使用客户托管密钥加密 OS 和数据磁盘

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="encrypt-existing-managed-disks"></a>加密现有托管磁盘 

不得将现有磁盘附加到正在运行的 VM，以便可以使用以下脚本加密这些磁盘：

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用市场映像创建虚拟机规模集，并使用客户托管密钥加密 OS 和数据磁盘

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>创建一个使用客户托管密钥的服务器端加密进行了加密的空磁盘，并将其附加到 VM

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=westcentralus
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>更改 DiskEncryptionSet 的密钥，以轮替引用 DiskEncryptionSet 的所有资源的密钥

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>查找磁盘的服务器端加密状态

[!INCLUDE [virtual-machines-disks-encryption-status-cli](../../../includes/virtual-machines-disks-encryption-status-cli.md)]

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识（Azure Active Directory (Azure AD) 的一项功能）。 配置客户托管密钥时，实际上会自动将托管标识分配给你的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个目录，则不会将与托管磁盘关联的托管标识传输到新租户，因此客户管理的密钥可能不再有效。 有关详细信息，请参阅[在 Azure AD 目录之间转移订阅](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="next-steps"></a>后续步骤

- [探索 Azure 资源管理器模板以使用客户管理密钥创建加密磁盘](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [使用启用了客户托管密钥的磁盘来复制计算机](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [使用 PowerShell 设置 VMware VM 到 Azure 的灾难恢复](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [使用 PowerShell 和 Azure 资源管理器为 Hyper-V VM 设置到 Azure 的灾难恢复](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
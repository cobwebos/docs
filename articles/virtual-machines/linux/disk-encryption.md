---
title: Azure 托管磁盘的服务器端加密 - Azure CLI
description: Azure 存储在将数据保存到存储群集之前会对其进行静态加密，以此保护数据。 您可以依赖 Microsoft 管理的密钥来加密托管磁盘，也可以使用客户管理的密钥使用您自己的密钥管理加密。
author: roygara
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 88d25083a1105023279f3907a4573319fabe087c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520774"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure 托管磁盘的服务器端加密

默认情况下，在将数据保存到云时，Azure 托管磁盘会自动加密数据。 服务器端加密可保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。 Azure 托管磁盘中的数据将使用 256 位 [AES 加密法](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强大块加密法之一）以透明方式进行加密，并符合 FIPS 140-2 规范。   

加密不会影响托管磁盘的性能。 加密不会产生额外的费用。

有关 Azure 托管磁盘基础的加密模块的详细信息，请参阅加密[API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>关于加密密钥管理

您可以依赖平台管理的密钥来加密托管磁盘，也可以使用自己的密钥管理加密。 如果选择使用自己的密钥来管理加密，则可以指定*客户管理的密钥*，用于加密和解密托管磁盘中的所有数据。 

以下部分更详细地介绍了每个密钥管理选项。

## <a name="platform-managed-keys"></a>平台托管的密钥

默认情况下，托管磁盘使用平台托管的加密密钥。 自 2017 年 6 月 10 日起，所有写入现有托管磁盘的新托管磁盘、快照、映像和新数据都使用平台托管密钥自动加密静态。

## <a name="customer-managed-keys"></a>客户管理的密钥

您可以选择使用自己的密钥在每个托管磁盘级别管理加密。 使用客户托管密钥的托管磁盘的服务器端加密提供了 Azure 密钥保管库的集成体验。 您可以将[RSA 密钥](../../key-vault/key-vault-hsm-protected-keys.md)导入密钥保管库，或在 Azure 密钥保管库中生成新的 RSA 密钥。 Azure 托管磁盘使用[信封加密](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)以完全透明的方式处理加密和解密。 它使用基于[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 的数据加密密钥 （DEK） 加密数据，而密钥又使用密钥进行保护。 您必须授予对密钥保管库中的托管磁盘的访问权限，才能使用密钥加密和解密 DEK。 这允许您完全控制数据和密钥。 您可以随时禁用密钥或撤消对托管磁盘的访问权限。 还可以使用 Azure 密钥保管库监视审核加密密钥使用情况，以确保只有托管磁盘或其他受信任的 Azure 服务才能访问密钥。

对于高级 SSD、标准 SSD 和标准 HDD：当您禁用或删除密钥时，任何使用该密钥的磁盘的 VM 将自动关闭。 在此之后，除非再次启用密钥或您分配新密钥，否则 VM 将不可用。

对于超级磁盘，当您禁用或删除密钥时，使用该密钥的任何具有超磁盘的 VM 不会自动关闭。 取消分配并重新启动 VM 后，磁盘将停止使用密钥，然后 VM 将无法重新联机。 要使 VM 重新联机，必须分配新密钥或启用现有密钥。

下图显示了托管磁盘如何使用 Azure 活动目录和 Azure 密钥保管库使用客户管理的密钥发出请求：

![托管磁盘和客户管理密钥工作流。 管理员创建 Azure 密钥保管库，然后创建磁盘加密集，并设置磁盘加密集。 该集与 VM 相关联，它允许磁盘利用 Azure AD 进行身份验证](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


下面的列表更详细地解释了该关系图：

1. Azure 密钥保管库管理员创建密钥保管库资源。
1. 密钥保管库管理员将 RSA 密钥导入密钥保管库或在密钥保管库中生成新的 RSA 密钥。
1. 该管理员创建磁盘加密集资源的实例，指定 Azure 密钥保管库 ID 和密钥 URL。 磁盘加密集是为简化托管磁盘的密钥管理而引入的新资源。 
1. 创建磁盘加密集时，在 Azure 活动目录 （AD） 中创建[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，并与磁盘加密集关联。 
1. 然后，Azure 密钥保管库管理员授予托管标识权限，以在密钥保管库中执行操作。
1. VM 用户通过将磁盘与磁盘加密集相关联来创建磁盘。 VM 用户还可以通过将现有资源的密钥与磁盘加密集相关联，使用客户管理的现有资源密钥启用服务器端加密。 
1. 托管磁盘使用托管标识向 Azure 密钥保管库发送请求。
1. 对于读取或写入数据，托管磁盘向 Azure 密钥保管库发送请求以加密（包装）和解密（解包）数据加密密钥，以便对数据执行加密和解密。 

若要撤销对客户管理的密钥的访问权限，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 和 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤销访问权限会实际阻止对存储帐户中所有数据的访问，因为 Azure 存储帐户无法访问加密密钥。

### <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>限制

目前，客户管理的密钥具有以下限制：

- 如果为磁盘启用了此功能，则无法禁用它。
    如果需要解决此问题，则必须[将所有数据复制到](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)不使用客户托管密钥的完全不同的托管磁盘。
- 仅支持大小为 2048 的["软"和"硬"RSA 密钥](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types)，没有其他键或大小。
- 使用服务器端加密和客户管理的密钥加密的自定义映像创建的磁盘必须使用相同的客户管理密钥进行加密，并且必须在同一订阅中。
- 使用服务器端加密和客户管理的密钥加密的磁盘创建的快照必须使用相同的客户管理密钥进行加密。
- 在共享映像库中，无法使用使用服务器端加密和客户管理密钥加密的自定义映像。
- 与客户管理的密钥（Azure 密钥保管库、磁盘加密集、VM、磁盘和快照）相关的所有资源必须位于同一订阅和区域中。
- 使用客户管理的密钥加密的磁盘、快照和映像无法移动到其他订阅。
- 如果使用 Azure 门户创建磁盘加密集，则当前无法使用快照。
- 使用客户托管密钥加密的托管磁盘也不能使用 Azure 磁盘加密进行加密。

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>设置 Azure 密钥保管库和磁盘加密集

1. 确保已安装了最新的 [Azure CLI](/cli/azure/install-az-cli2) 并已使用 [az login](/cli/azure/reference-index) 登录到 Azure 帐户。

1. 创建 Azure 密钥保管库和加密密钥的实例。

    创建密钥保管库实例时，必须启用软删除和清除保护。 软删除可确保密钥保管库在给定保留期（默认为 90 天）保留已删除的密钥。 清除保护可确保在保留期结束之前不能永久删除已删除的密钥。 这些设置可保护您不因意外删除而丢失数据。 使用密钥保管库加密托管磁盘时，这些设置是必需的。

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=WestCentralUS
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    创建磁盘加密集的实例。 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    授予磁盘加密集对密钥保管库的资源访问权限。 

        > [!NOTE]
        > Azure 可能需要几分钟才能在 Azure 活动目录中创建磁盘加密集的标识。 如果在运行以下命令时遇到"找不到活动目录对象"之类的错误，请等待几分钟，然后重试。

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用应用商店映像创建 VM，使用客户管理的密钥加密操作系统和数据磁盘

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-unattached-managed-disks"></a>加密现有的未连接托管磁盘 

您现有的磁盘不得连接到正在运行的 VM，以便您使用以下脚本对其进行加密：

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用应用商店映像创建虚拟机规模集，使用客户管理的密钥加密操作系统和数据磁盘

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>使用使用客户管理的密钥使用服务器端加密创建加密的空磁盘，并将其附加到 VM

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=WestCentralUS
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识，后者是 Azure Active Directory (Azure AD) 的一项功能。 配置客户管理的密钥时，托管标识将自动分配给所覆盖的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个 Azure AD 目录，则与托管磁盘关联的托管标识不会传输到新租户，因此客户托管密钥可能不再工作。 有关详细信息，请参阅在[Azure AD 目录之间传输订阅](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识，后者是 Azure Active Directory (Azure AD) 的一项功能。 配置客户管理的密钥时，托管标识将自动分配给所覆盖的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个 Azure AD 目录，则与托管磁盘关联的托管标识不会传输到新租户，因此客户托管密钥可能不再工作。 有关详细信息，请参阅在[Azure AD 目录之间传输订阅](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>服务器端加密与 Azure 磁盘加密

[虚拟机和虚拟机扩展集的 Azure 磁盘加密](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)利用 Windows 的[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)功能和 Linux 的[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能在来宾 VM 中使用客户管理的密钥加密托管磁盘。  使用客户托管密钥的服务器端加密通过加密存储服务中的数据，使你能够将任何 OS 类型和映像用于 VM，从而改进了 ADE。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure 资源管理器模板，以便使用客户管理的密钥创建加密磁盘](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什么是 Azure Key Vault？](../../key-vault/key-vault-overview.md)
- [使用启用客户管理的密钥磁盘复制计算机](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [使用 PowerShell 设置 VMware VM 到 Azure 的灾难恢复](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [使用 PowerShell 和 Azure 资源管理器对 Hyper-V VM 设置到 Azure 的灾难恢复](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)

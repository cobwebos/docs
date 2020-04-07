---
title: Azure 托管磁盘的服务器端加密 - PowerShell
description: Azure 存储在将数据保存到存储群集之前会对其进行静态加密，以此保护数据。 您可以依赖 Microsoft 管理的密钥来加密托管磁盘，也可以使用客户管理的密钥使用您自己的密钥管理加密。
author: roygara
ms.date: 04/02/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 25b8df0d8565686737b33aac16d4bf698ce43280
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757207"
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

您可以选择使用自己的密钥在每个托管磁盘级别管理加密。 使用客户托管密钥的托管磁盘的服务器端加密提供了 Azure 密钥保管库的集成体验。 您可以将[RSA 密钥](../../key-vault/key-vault-hsm-protected-keys.md)导入密钥保管库，或在 Azure 密钥保管库中生成新的 RSA 密钥。 

Azure 托管磁盘使用[信封加密](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)以完全透明的方式处理加密和解密。 它使用基于[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 的数据加密密钥 （DEK） 加密数据，而密钥又使用密钥进行保护。 存储服务生成数据加密密钥，并使用 RSA 加密使用客户管理的密钥对其进行加密。 信封加密允许您根据合规性策略定期轮换（更改）密钥，而不会影响 VM。 旋转密钥时，存储服务使用新的客户管理密钥重新加密数据加密密钥。 

您必须授予对密钥保管库中的托管磁盘的访问权限，才能使用密钥加密和解密 DEK。 这允许您完全控制数据和密钥。 您可以随时禁用密钥或撤消对托管磁盘的访问权限。 还可以使用 Azure 密钥保管库监视审核加密密钥使用情况，以确保只有托管磁盘或其他受信任的 Azure 服务才能访问密钥。

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
    如果需要解决此问题，则必须[将所有数据复制到](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)不使用客户托管密钥的完全不同的托管磁盘。
- 仅支持大小为 2080 的["软"和"硬"RSA 密钥](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types)，没有其他键或大小。
- 使用服务器端加密和客户管理的密钥加密的自定义映像创建的磁盘必须使用相同的客户管理密钥进行加密，并且必须在同一订阅中。
- 使用服务器端加密和客户管理的密钥加密的磁盘创建的快照必须使用相同的客户管理密钥进行加密。
- 在共享映像库中，无法使用使用服务器端加密和客户管理密钥加密的自定义映像。
- 与客户管理的密钥（Azure 密钥保管库、磁盘加密集、VM、磁盘和快照）相关的所有资源必须位于同一订阅和区域中。
- 使用客户管理的密钥加密的磁盘、快照和映像无法移动到其他订阅。
- 如果使用 Azure 门户创建磁盘加密集，则当前无法使用快照。
- 使用客户托管密钥加密的托管磁盘也不能使用 Azure 磁盘加密进行加密。

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>设置 Azure 密钥保管库和磁盘加密集

1. 请确保您已安装最新的 Azure [PowerShell 版本](/powershell/azure/install-az-ps)，并且已登录使用 Connect-AzAccount 登录到 Azure 帐户

1. 创建 Azure 密钥保管库和加密密钥的实例。

    创建密钥保管库实例时，必须启用软删除和清除保护。 软删除可确保密钥保管库在给定保留期（默认为 90 天）保留已删除的密钥。 清除保护可确保在保留期结束之前不能永久删除已删除的密钥。 这些设置可保护您不因意外删除而丢失数据。 使用密钥保管库加密托管磁盘时，这些设置是必需的。

    > [!IMPORTANT]
    > 不要骆驼的情况下，如果这样做，在 Azure 门户中为资源分配其他磁盘时可能会遇到问题。
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    创建磁盘加密集的实例。 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    授予磁盘加密集对密钥保管库的资源访问权限。

        > [!NOTE]
        > Azure 可能需要几分钟才能在 Azure 活动目录中创建磁盘加密集的标识。 如果在运行以下命令时遇到"找不到活动目录对象"之类的错误，请等待几分钟，然后重试。
        
        ```powershell
        $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
         
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
         
        New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用应用商店映像创建 VM，使用客户管理的密钥加密操作系统和数据磁盘

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>使用使用客户管理的密钥使用服务器端加密创建加密的空磁盘，并将其附加到 VM

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-unattached-managed-disks"></a>加密现有的未连接托管磁盘 

您现有的磁盘不得连接到正在运行的 VM，以便您使用以下脚本对其进行加密：

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用应用商店映像创建虚拟机规模集，使用客户管理的密钥加密操作系统和数据磁盘

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>更改磁盘加密集的键以旋转引用磁盘加密集的所有资源的密钥

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>查找磁盘服务器端加密的状态

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识，后者是 Azure Active Directory (Azure AD) 的一项功能。 配置客户管理的密钥时，托管标识将自动分配给所覆盖的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个 Azure AD 目录，则与托管磁盘关联的托管标识不会传输到新租户，因此客户托管密钥可能不再工作。 有关详细信息，请参阅在[Azure AD 目录之间传输订阅](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识，后者是 Azure Active Directory (Azure AD) 的一项功能。 配置客户管理的密钥时，托管标识将自动分配给所覆盖的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个 Azure AD 目录，则与托管磁盘关联的托管标识不会传输到新租户，因此客户托管密钥可能不再工作。 有关详细信息，请参阅在[Azure AD 目录之间传输订阅](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>服务器端加密与 Azure 磁盘加密

[Azure 磁盘加密](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)利用 Windows 的[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)功能和 Linux 的[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能在来宾 VM 中使用客户托管密钥对托管磁盘进行加密。  使用客户托管密钥的服务器端加密通过加密存储服务中的数据，使你能够将任何 OS 类型和映像用于 VM，从而改进了 ADE。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure 资源管理器模板，以便使用客户管理的密钥创建加密磁盘](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什么是 Azure Key Vault？](../../key-vault/key-vault-overview.md)
- [使用启用客户管理的密钥磁盘复制计算机](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [使用 PowerShell 设置 VMware VM 到 Azure 的灾难恢复](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [使用 PowerShell 和 Azure 资源管理器对 Hyper-V VM 设置到 Azure 的灾难恢复](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)

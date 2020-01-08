---
title: Azure 托管磁盘的服务器端加密-PowerShell
description: Azure 存储在将数据保存到存储群集之前，通过静态加密来保护数据。 你可以依赖于 Microsoft 托管的密钥来加密托管磁盘，或者可以使用客户管理的密钥来管理使用你自己的密钥进行加密。
author: roygara
ms.date: 12/13/2019
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: c84c3b5b7e299cdd83f10746adb31ec539c2badd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460088"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure 托管磁盘的服务器端加密

默认情况下，在将数据保存到云时，Azure 托管磁盘会自动加密数据。 服务器端加密可保护你的数据，并可帮助你满足组织的安全性和符合性承诺。 Azure 托管磁盘中的数据以透明方式使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强阻止密码之一）进行加密，且符合 FIPS 140-2。   

加密不会影响托管磁盘的性能。 加密不会产生额外的费用。

有关 Azure 托管磁盘基础的加密模块的详细信息，请参阅[加密 API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>关于加密密钥管理

你可以依赖于平台托管的密钥来加密托管磁盘，也可以使用自己的密钥（公共预览版）管理加密。 如果选择使用自己的密钥管理加密，则可以指定*客户托管的密钥*，用于加密和解密托管磁盘中的所有数据。 

以下部分更详细地介绍了密钥管理的每个选项。

## <a name="platform-managed-keys"></a>平台托管密钥

默认情况下，托管磁盘使用平台托管的加密密钥。 从2017年6月10日起，写入到现有托管磁盘的所有新的托管磁盘、快照、映像和新数据都将通过平台管理的密钥自动进行静态加密。 

## <a name="customer-managed-keys-public-preview"></a>客户管理的密钥（公共预览版）

你可以选择在每个托管磁盘的级别管理加密，以及你自己的密钥。 使用客户托管密钥的托管磁盘的服务器端加密提供与 Azure Key Vault 的集成体验。 您可以将[您的 rsa 密钥](../../key-vault/key-vault-hsm-protected-keys.md)导入 Key Vault 或在 Azure Key Vault 中生成新的 rsa 密钥。 Azure 托管磁盘使用[信封加密](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)以完全透明的方式处理加密和解密。 它使用基于[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 的数据加密密钥（DEK）对数据进行加密，进而使用密钥进行保护。 必须授予对 Key Vault 中托管磁盘的访问权限，才能使用密钥来加密和解密 DEK。 这允许你完全控制数据和密钥。 你可以随时禁用密钥或撤消对托管磁盘的访问权限。 你还可以使用 Azure Key Vault 监视来审核加密密钥的使用情况，以确保仅托管磁盘或其他受信任的 Azure 服务访问你的密钥。

下图显示了托管磁盘如何使用 Azure Active Directory 和 Azure Key Vault 来使用客户管理的密钥发出请求：

![托管磁盘客户管理的密钥工作流](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


以下列表更详细地介绍了关系图：

1. Azure Key Vault 管理员创建密钥保管库资源。
1. Key vault 管理员会将其 RSA 密钥导入 Key Vault 或在 Key Vault 中生成新的 RSA 密钥。
1. 此管理员创建磁盘加密集资源的实例，指定 Azure Key Vault ID 和密钥 URL。 磁盘加密集是为简化托管磁盘的密钥管理而引入的新资源。 
1. 创建磁盘加密集时，将在 Azure Active Directory （AD）中创建一个[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，并将其与磁盘加密集相关联。 
1. 然后，Azure 密钥保管库管理员授予托管标识权限，以在密钥保管库中执行操作。
1. VM 用户通过将磁盘与磁盘加密集相关联来创建磁盘。 VM 用户还可以通过将现有资源的服务器端加密与磁盘加密集相关联，为现有资源的客户托管密钥启用服务器端加密。 
1. 托管磁盘使用托管标识将请求发送到 Azure Key Vault。
1. 对于读取或写入数据，托管磁盘会将请求发送到 Azure Key Vault，以便对数据加密密钥进行加密（wrap）和解密（解包），以便对数据进行加密和解密。 

若要撤消对客户管理的密钥的访问权限，请参阅[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/)和[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 有效地吊销访问权限会阻止对存储帐户中所有数据的访问，因为 Azure 存储无法访问加密密钥。

### <a name="supported-scenarios-and-restrictions"></a>支持的方案和限制

预览期间，仅支持以下方案：

- 使用 Azure Marketplace 映像创建虚拟机（VM），并使用客户管理的密钥通过服务器端加密来加密 OS 磁盘。
- 创建使用服务器端加密和客户管理的密钥加密的自定义映像。
- 使用自定义映像创建 VM，并使用服务器端加密和客户管理的密钥对 OS 磁盘进行加密。
- 创建使用服务器端加密和客户管理的密钥加密的数据磁盘。
- 创建使用服务器端加密和客户管理的密钥加密的快照。
- 创建用服务器端加密和客户管理的密钥加密的虚拟机规模集。

预览还具有下列限制：

- 仅在美国西部提供。
- 使用服务器端加密和客户托管密钥加密的自定义映像创建的磁盘必须使用相同的客户托管密钥进行加密，且必须位于同一订阅中。
- 从用服务器端加密和客户管理的密钥加密的磁盘创建的快照必须用相同的客户托管密钥进行加密。
- 使用服务器端加密和客户管理的密钥加密的自定义映像不能用于共享映像库。
- Key Vault 必须与客户托管的密钥位于同一订阅和区域中。
- 用客户管理的密钥加密的磁盘、快照和映像不能移到另一个订阅。

### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>设置 Azure Key Vault 和 DiskEncryptionSet

1.  创建 Azure Key Vault 和加密密钥的实例。

    创建 Key Vault 实例时，必须启用软删除和清除保护。 软删除可确保 Key Vault 在给定的保留期（默认为90天）内保存已删除的密钥。 清除保护确保在保留期结束之前，无法永久删除已删除的密钥。 由于意外删除，这些设置可防止丢失数据。 使用 Key Vault 加密托管磁盘时，这些设置是必需的。

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

1.  创建 DiskEncryptionSet 的实例。 
    
    ```powershell
    $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned

    $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
    ```

1.  向 DiskEncryptionSet 资源授予对密钥保管库的访问权限。

    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
     
    New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -  ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
    ```

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用 Marketplace 映像创建 VM，使用客户管理的密钥对 OS 和数据磁盘进行加密

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

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>使用客户托管密钥的服务器端加密创建加密的空磁盘，并将其附加到 VM

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

Update-AzVM -ResourceGroupName $rgName -VM $vm

```


> [!IMPORTANT]
> 客户托管的密钥依赖于 Azure 资源的托管标识，一项功能 Azure Active Directory （Azure AD）。 配置客户管理的密钥时，会自动将托管标识分配给你的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个目录，则与托管磁盘关联的托管标识不会传输到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅在[Azure AD 目录之间传输订阅](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>服务器端加密与 Azure 磁盘加密

[Azure 磁盘加密](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)利用 Windows 的[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)功能和 Linux 的[dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能，将托管磁盘与来宾 VM 中客户托管的密钥进行加密。  使用客户托管密钥的服务器端加密可改善 ADE，因为这样可以通过对存储服务中的数据进行加密来使用 Vm 的任何 OS 类型和映像。

## <a name="next-steps"></a>后续步骤

- [探索 Azure 资源管理器模板，以便通过客户托管的密钥创建加密磁盘](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什么是 Azure 密钥保管库？](../../key-vault/key-vault-overview.md)
---
title: 为 Windows IaaS VM 启用 Azure 磁盘加密 | Microsoft Docs
description: 本文提供有关如何为 Windows IaaS VM 启用 Microsoft Azure 磁盘加密的说明。
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 8b3905c8-844f-4ec7-ad95-b386e9843053
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 772ab272603d0f8e0badf899c439592b20b7c8a3
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392574"
---
#  <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>为 Windows IaaS VM 启用 Azure 磁盘加密 

可启用多种磁盘加密方案，具体步骤因方案而异。 以下部分更详细介绍了适用于 Windows IaaS VM 的方案。 在使用磁盘加密之前，需要先完成 [Azure 磁盘加密先决条件](/articles/security/azure-security-disk-encryption-prerequisites.md)。 

加密磁盘之前创建[快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)和/或备份。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 加密之前，需要备份包含托管磁盘的 VM。 备份之后，可以通过指定 -skipVmBackup 参数，使用 Set-AzureRmVMDiskEncryptionExtension cmdlet 来加密托管磁盘。 有关如何备份和还原已加密 VM 的详细信息，请参阅 [Azure 备份](../backup/backup-azure-vms-encryption.md)一文。 

>[!WARNING]
>为确保加密机密不会跨过区域边界，Azure 磁盘加密需要将 Key Vault 和 VM 共置在同一区域。 在要加密的 VM 所在的同一区域中创建并使用 Key Vault。 


## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>在通过市场创建的新 IaaS VM 上启用加密。
可以使用资源管理器模板从 Azure 市场为新的 IaaS Windows VM 启用磁盘加密。 该模板使用 Windows Server 2012 库映像创建新的加密 Windows VM。

1. 在[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)中，单击“部署到 Azure”。

2. 选择订阅、资源组、资源组位置、参数、法律条款和协议。 单击“购买”，部署已启用加密的新 IaaS VM。

3. 部署模板后，使用首选的方法验证 VM 加密状态：
     - 在 Azure CLI 中使用 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 命令进行验证。 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - 在 Azure PowerShell 中使用 [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) cmdlet 进行验证。 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     -  在门户中选择 VM，然后单击“设置”标题下面的“磁盘”验证加密状态。 在“加密”下面的图表中，可以看到是否已启用加密。 
           ![Azure 门户 - 已启用磁盘加密](./media/azure-security-disk-encryption/disk-encryption-fig2.png)下表列出了市场方案中使用 Azure AD 客户端 ID 的新 VM 的资源管理器模板参数：

| 参数 | Description | 
| --- | --- |
| adminUserName | 虚拟机的管理员用户名。 |
| adminPassword | 虚拟机的管理员用户密码。 |
| newStorageAccountName | 用于存储 OS 和数据 VHD 的存储帐户的名称。 |
| vmSize | VM 的大小 目前仅支持标准 A、D 和 G 系列。 |
| virtualNetworkName | VM NIC 所属的 VNet 的名称。 |
| subnetName | VM NIC 所属的 VNet 中子网的名称。 |
| AADClientID | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端 ID。 |
| AADClientSecret | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端机密。 |
| keyVaultURL | BitLocker 密钥应上传到的 Key Vault 的 URL。 可使用 `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName).VaultURI` cmdlet 或 Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri` 获取该 URL。 |
| keyEncryptionKeyURL | 用于加密生成的 BitLocker 密钥的密钥加密密钥的 URL（可选）。 </br> </br>KeyEncryptionKeyURL 是可选参数。 可使用自己的 KEK 在 Key Vault 中进一步保护数据加密密钥（密码）。 |
| keyVaultResourceGroup | Key Vault 的资源组。 |
| vmName | 要对其执行加密操作的 VM 的名称。 |


## <a name="bkmk_RunningWinVM"></a>在现有或正在运行的 IaaS Windows VM 上启用加密
在此方案中，可以使用模板、PowerShell cmdlet 或 CLI 命令启用加密。 以下部分更详细地介绍了如何启用 Azure 磁盘加密。 

>[!IMPORTANT]
 >启用 Azure 磁盘加密之前，必须在其外部创建基于托管磁盘的 VM 实例的快照和/或备份。 可以从门户创建托管磁盘的快照，也可以使用 [Azure 备份](../backup/backup-azure-vms-encryption.md)。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 备份后，可以通过指定 -skipVmBackup 参数，使用 Set-AzureRmVMDiskEncryptionExtension cmdlet 来加密托管磁盘。 在未备份基于托管磁盘的 VM 且未指定此参数的情况下，对 VM 使用 Set-AzureRmVMDiskEncryptionExtension 命令会失败。 
>
>加密或禁用加密可能导致 VM 重新启动。 
>

### <a name="bkmk_RunningWinVMPSH"></a>使用 Azure PowerShell 在现有或正在运行的 VM 上启用加密 
使用 [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) cmdlet 在 Azure 中运行的 IaaS 虚拟机上启用加密。 若要了解如何使用 PowerShell cmdlet 通过 Azure 磁盘加密启用加密，请参阅博客文章 [Explore Azure Disk Encryption with Azure PowerShell - Part 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx)（了解如何使用 Azure PowerShell 启用 Azure 磁盘加密 - 第 1 部分）和 [Explore Azure Disk Encryption with Azure PowerShell - Part 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)（了解如何使用 Azure PowerShell 启用 Azure 磁盘加密 - 第 2 部分）。

-  **使用客户端机密加密正在运行的 VM：** 以下脚本初始化变量并运行 Set-AzureRmVMDiskEncryptionExtension cmdlet。 作为先决条件，应已事先创建资源组、VM、Key Vault、AAD 应用和客户端机密。 将 MySecureRg、MySecureVM、MySecureVault、My-AAD-client-ID 和 My-AAD-client-secret 替换为自己的值。
     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **使用 KEK 包装客户端机密，以加密正在运行的 VM：** Azure 磁盘加密允许在 Key Vault 中指定一个现有密钥，用于包装启用加密时生成的磁盘加密机密。 指定密钥加密密钥后，Azure 磁盘加密会使用该密钥包装加密机密，然后将机密写入 Key Vault。 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **验证磁盘是否已加密：** 若要检查 IaaS VM 的加密状态，请使用 [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) cmdlet。 
     ```azurepowershell-interactive
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet。 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>使用 Azure CLI 在现有或正在运行的 VM 上启用加密
使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令在 Azure 中运行的 IaaS 虚拟机上启用加密。

-  **使用客户端机密加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **使用 KEK 包装客户端机密，以加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **验证磁盘是否已加密：** 若要检查 IaaS VM 的加密状态，请使用 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 命令。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >启用 Azure 磁盘加密之前，必须在其外部创建基于托管磁盘的 VM 实例的快照和/或备份。 可以从门户创建托管磁盘的快照，也可以使用 [Azure 备份](../backup/backup-azure-vms-encryption.md)。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 备份后，可以通过指定 -skipVmBackup 参数，使用 Set-AzureRmVMDiskEncryptionExtension cmdlet 来加密托管磁盘。 在未备份基于托管磁盘的 VM 且未指定此参数的情况下，对 VM 使用此命令会失败。 
>
>加密或禁用加密可能导致 VM 重新启动。 

### <a name="bkmk_RunningWinVMwRM"></a>使用资源管理器模板
可以通过使用[资源管理器模板加密正在运行的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)，在 Azure 中现有或正在运行的 IaaS Windows VM 上启用磁盘加密。


1. 在 Azure 快速入门模板中，单击“部署到 Azure”。

2. 选择订阅、资源组、资源组位置、参数、法律条款和协议。 单击“购买”，在现有或正在运行的 IaaS VM 上启用加密。

下表列出了使用 Azure AD 客户端 ID 的现有或正在运行的 VM 的 资源管理器模板参数：

| 参数 | Description |
| --- | --- |
| AADClientID | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端 ID。 |
| AADClientSecret | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端机密。 |
| KeyVaultName | BitLocker 密钥应上传到的 Key Vault 的名称。 可以使用 cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` 或 Azure CLI 命令 `az keyvault list --resource-group "MySecureGroup" 获取该名称 |Convertfrom-JSON`|
|  keyEncryptionKeyURL | 用于加密所生成 BitLocker 密钥的密钥加密密钥的 URL。 如果在 UseExistingKek 下拉列表中选择“nokek”，则此参数为可选参数。 如果在 UseExistingKek 下拉列表中选择“kek”，则必须输入 _keyEncryptionKeyURL_ 值。 |
| volumeType | 要对其执行加密操作的卷的类型。 有效值为“OS”、“Data”和“All”。 |
| sequenceVersion | BitLocker 操作的序列版本。 每当在同一个 VM 上执行磁盘加密操作时，此版本号便会递增。 |
| vmName | 要对其执行加密操作的 VM 的名称。 |


## <a name="bkmk_VHDpre"></a>通过客户加密的 VHD 和加密密钥新建的 IaaS VM
在此方案中，可以通过使用资源管理器模板、PowerShell cmdlet 或 CLI 命令启用加密。 以下部分详细介绍了资源管理器模板和 CLI 命令。 

参考附录中的说明来准备可在 Azure 中使用的预加密映像。 创建映像后，可使用下一部分中的步骤创建加密的 Azure VM。

* [准备预加密的 Windows VHD](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [准备预加密的 Linux VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >启用 Azure 磁盘加密之前，必须在其外部创建基于托管磁盘的 VM 实例的快照和/或备份。 可以从门户创建托管磁盘的快照，也可以使用 [Azure 备份](../backup/backup-azure-vms-encryption.md)。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 备份后，可以通过指定 -skipVmBackup 参数，使用 Set-AzureRmVMDiskEncryptionExtension cmdlet 来加密托管磁盘。 在未备份基于托管磁盘的 VM 且未指定此参数的情况下，对 VM 使用 Set-AzureRmVMDiskEncryptionExtension 命令会失败。 
>
>加密或禁用加密可能导致 VM 重新启动。 



### <a name="bkmk_VHDprePSH"></a>使用 Azure PowerShell 加密包含预加密 VHD 的 VM
可以使用 PowerShell cmdlet [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples) 在加密的 VHD 上启用磁盘加密。 以下示例显示了一些常用参数。 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```


### <a name="bkmk_VHDpreRM"></a>使用资源管理器模板加密包含预加密 VHD 的 IaaS VM 
也可通过[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)在加密 VHD 上启用磁盘加密。

1. 在 Azure 快速入门模板中，单击“部署到 Azure”。

2. 选择订阅、资源组、资源组位置、参数、法律条款和协议。 单击“创建”，在新 IaaS VM 上启用加密。

下表列出了加密 VHD 的资源管理器模板参数：

| 参数 | Description |
| --- | --- |
| newStorageAccountName | 用于存储加密 OS VHD 的存储帐户的名称。 应已在与 VM 相同的资源组和相同的位置中创建此存储帐户。 |
| osVhdUri | 存储帐户中 OS VHD 的 URI。 |
| osType | OS 产品类型 (Windows/Linux)。 |
| virtualNetworkName | VM NIC 所属的 VNet 的名称。 应已在与 VM 相同的资源组和相同的位置中创建此名称。 |
| subnetName | VM NIC 所属的 VNet 中子网的名称。 |
| vmSize | VM 的大小 目前仅支持标准 A、D 和 G 系列。 |
| keyVaultResourceID | 在 Azure 资源管理器中标识 Key Vault 资源的资源 ID。 可以使用 PowerShell cmdlet `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` 或 Azure CLI 命令 `az keyvault show --name "MySecureVault" --query id` 获取该 ID|
| keyVaultSecretUrl | 在 Key Vault 中设置的磁盘加密密钥的 URL。 |
| keyVaultKekUrl | 用于加密生成的磁盘加密密钥的密钥加密密钥的 URL。 |
| vmName | IaaS VM 的名称。 |

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新添加的数据磁盘上启用加密
可以[使用 PowerShell](../virtual-machines/windows/attach-disk-ps.md) 或[通过 Azure 门户](../virtual-machines/windows/attach-managed-disk-portal.md)将新磁盘添加到 Windows VM。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>使用 Azure PowerShell 在新添加的磁盘上启用加密
 使用 Powershell 加密 Windows VM 的新磁盘时，应指定新的序列版本。 序列版本必须唯一。 以下脚本生成序列版本的 GUID。 在某些情况下，Azure 磁盘加密扩展可能会自动加密新添加的数据磁盘。 如果发生这种情况，我们建议结合新序列版本再次运行 Set-AzureRmVmDiskEncryptionExtension cmdlet。
 

-  **使用客户端机密加密正在运行的 VM：** 以下脚本初始化变量并运行 Set-AzureRmVMDiskEncryptionExtension cmdlet。 作为先决条件，应已事先创建资源组、VM、Key Vault、AAD 应用和客户端机密。 将 MySecureRg、MySecureVM、MySecureVault、My-AAD-client-ID 和 My-AAD-client-secret 替换为自己的值。 -VolumeType 参数设置为数据磁盘而不是 OS 磁盘。 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId  –SequenceVersion $sequenceVersion;
    ```
- **使用 KEK 包装客户端机密，以加密正在运行的 VM：** Azure 磁盘加密允许在 Key Vault 中指定一个现有密钥，用于包装启用加密时生成的磁盘加密机密。 指定密钥加密密钥后，Azure 磁盘加密会使用该密钥包装加密机密，然后将机密写入 Key Vault。 如果加密的是数据磁盘而不是 OS 磁盘，可能需要添加 -VolumeType 参数。 

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>使用 Azure CLI 在新添加的磁盘上启用加密
 运行 Azure CLI 命令来启用加密时，命令会自动提供新的序列版本。 
-  **使用客户端机密加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **使用 KEK 包装客户端机密，以加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault"--key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>使用 Azure AD 基于客户端证书的身份验证启用加密。
可以结合或不结合 KEK 使用客户端证书身份验证。 脚本要求满足 [Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)。 在使用 PowerShell 脚本之前，应已将证书上传到 Key Vault 并已将其部署到 VM。 如果同时使用 KEK，该 KEK 应该存在。 有关详细信息，请参阅先决条件文章的 [Azure AD 基于证书的身份验证](azure-security-disk-encryption-prerequisites.md#bkmk_Cert)部分。


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>在 Azure PowerShell 中使用基于证书的身份验证启用加密

```powershell
## Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = ‘MySecureVM’;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>在 Azure PowerShell 中使用基于证书的身份验证和 KEK 启用加密

```powershell
# Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>禁用加密功能
可以使用 Azure PowerShell、Azure CLI 或资源管理器模板禁用加密。 

- **使用 Azure PowerShell 禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet。 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

- **使用 Azure CLI 禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **使用资源管理器模板禁用加密：** 

    1. 单击[在正在运行的 Windows VM 上禁用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)模板中的“部署到 Azure”。
    2. 选择订阅、资源组、位置、VM、法律条款和协议。
    3.  单击“购买”，在正在运行的 Windows VM 上禁用磁盘加密。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [启用适用于 Linux 的 Azure 磁盘加密](azure-security-disk-encryption-linux.md)
---
title: 适用于 Windows Vm 的 Azure 磁盘 Azure AD 加密（以前的版本）（& a）
description: 本文提供有关如何为 Windows IaaS VM 启用 Microsoft Azure 磁盘加密的说明。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: d06be85e4d18bc0867835a307b44ec8813c79d7d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245001"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>适用于 Windows Vm 的 Azure 磁盘 Azure AD 加密（以前的版本）（& a）

**新版本的 Azure 磁盘加密无需提供 Azure AD 应用程序参数即可启用 VM 磁盘加密。使用新版本，在执行启用加密步骤时，不再需要提供 Azure AD 凭据。所有新 VM 必须使用新版本在没有 Azure AD 应用程序参数的情况下进行加密。若要查看使用新版本启用 VM 磁盘加密的说明，请参阅[适用于 Windows VMS 的 Azure 磁盘加密](disk-encryption-windows.md)。已使用 Azure AD 应用程序参数加密的 VM 仍受支持，应继续使用 AAD 语法进行维护。**


可启用多种磁盘加密方案，具体步骤因方案而异。 以下部分更详细介绍了适用于 Windows IaaS VM 的方案。 在使用磁盘加密之前，需要先完成 [Azure 磁盘加密先决条件](disk-encryption-overview-aad.md)。 


>[!IMPORTANT]
> - 在对磁盘进行加密之前，你应[拍摄快照](snapshot-copy-managed-disk.md)和/或创建备份。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 加密之前，需要备份包含托管磁盘的 VM。 进行备份后，可以使用[AzVMDiskEncryptionExtension cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension)通过指定-skipVmBackup 参数来加密托管磁盘。 有关如何备份和还原已加密 Vm 的详细信息，请参阅[备份和还原加密的 AZURE VM](../../backup/backup-azure-vms-encryption.md)。 
>
> - 加密或禁用加密可能会导致 VM 重新启动。

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>在通过市场创建的新 IaaS VM 上启用加密。
可以使用资源管理器模板从 Azure 市场为新的 IaaS Windows VM 启用磁盘加密。 该模板使用 Windows Server 2012 库映像创建新的加密 Windows VM。

1. 在[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)中，单击“部署到 Azure”。

2. 选择订阅、资源组、资源组位置、参数、法律条款和协议。 单击“购买”，部署已启用加密的新 IaaS VM。

3. 部署模板后，使用首选的方法验证 VM 加密状态：
     - 在 Azure CLI 中使用 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 命令进行验证。 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - 使用[AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet 验证 Azure PowerShell。 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  在门户中选择 VM，然后单击“设置”标题下面的“磁盘”验证加密状态。 在“加密”下面的图表中，可以看到是否已启用加密。 
           @no__t 0Azure 门户-已启用磁盘加密 @ no__t-1

下表列出了市场方案中使用 Azure AD 客户端 ID 的新 VM 的资源管理器模板参数：

| 参数 | 描述 | 
| --- | --- |
| adminUserName | 虚拟机的管理员用户名。 |
| adminPassword | 虚拟机的管理员用户密码。 |
| newStorageAccountName | 用于存储 OS 和数据 VHD 的存储帐户的名称。 |
| vmSize | VM 的大小。 目前仅支持标准 A、D 和 G 系列。 |
| virtualNetworkName | VM NIC 所属的 VNet 的名称。 |
| subnetName | VM NIC 所属的 VNet 中子网的名称。 |
| AADClientID | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端 ID。 |
| AADClientSecret | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端机密。 |
| keyVaultURL | BitLocker 密钥应上传到的 Key Vault 的 URL。 可使用 `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` cmdlet 或 Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri` 获取该 URL。 |
| keyEncryptionKeyURL | 用于加密生成的 BitLocker 密钥的密钥加密密钥的 URL（可选）。 </br> </br>KeyEncryptionKeyURL 是可选参数。 可使用自己的 KEK 在 Key Vault 中进一步保护数据加密密钥（密码）。 |
| keyVaultResourceGroup | Key Vault 的资源组。 |
| vmName | 要对其执行加密操作的 VM 的名称。 |


## <a name="bkmk_RunningWinVM"></a>在现有或正在运行的 IaaS Windows VM 上启用加密
在此方案中，可以使用模板、PowerShell cmdlet 或 CLI 命令启用加密。 以下部分更详细地介绍了如何启用 Azure 磁盘加密。 


### <a name="bkmk_RunningWinVMPSH"></a>使用 Azure PowerShell 在现有或正在运行的 VM 上启用加密 
使用[AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Cmdlet 在 Azure 中正在运行的 IaaS 虚拟机上启用加密。 若要了解如何使用 PowerShell cmdlet 通过 Azure 磁盘加密启用加密，请参阅博客文章 [Explore Azure Disk Encryption with Azure PowerShell - Part 1](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx)（了解如何使用 Azure PowerShell 启用 Azure 磁盘加密 - 第 1 部分）和 [Explore Azure Disk Encryption with Azure PowerShell - Part 2](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)（了解如何使用 Azure PowerShell 启用 Azure 磁盘加密 - 第 2 部分）。

-  **使用客户端机密加密正在运行的 VM：** 下面的脚本初始化变量并运行 AzVMDiskEncryptionExtension cmdlet。 作为先决条件，应已事先创建资源组、VM、Key Vault、AAD 应用和客户端机密。 将 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault、我的 AAD 客户端 ID 和你的 AAD 客户端密码替换为你的值。
     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **使用 KEK 包装客户端机密，以加密正在运行的 VM：** Azure 磁盘加密允许在 Key Vault 中指定一个现有密钥，用于包装启用加密时生成的磁盘加密机密。 指定密钥加密密钥后，Azure 磁盘加密会使用该密钥包装加密机密，然后将机密写入 Key Vault。 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **验证磁盘是否已加密：** 若要检查 IaaS VM 的加密状态，请使用[AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet。 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>使用 Azure CLI 在现有或正在运行的 VM 上启用加密
使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令在 Azure 中运行的 IaaS 虚拟机上启用加密。

- **使用客户端机密加密正在运行的 VM：**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **使用 KEK 包装客户端机密，以加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **验证磁盘是否已加密：** 若要查看 IaaS VM 的加密状态，请使用[az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="bkmk_RunningWinVMwRM"></a>使用资源管理器模板
可以通过使用[资源管理器模板加密正在运行的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)，在 Azure 中现有或正在运行的 IaaS Windows VM 上启用磁盘加密。


1. 在 Azure 快速入门模板中，单击“部署到 Azure”。

2. 选择订阅、资源组、资源组位置、参数、法律条款和协议。 单击“购买”，在现有或正在运行的 IaaS VM 上启用加密。

下表列出了使用 Azure AD 客户端 ID 的现有或正在运行的 VM 的 资源管理器模板参数：

| 参数 | 描述 |
| --- | --- |
| AADClientID | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端 ID。 |
| AADClientSecret | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端机密。 |
| KeyVaultName | BitLocker 密钥应上传到的 Key Vault 的名称。 可使用 cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` 或 Azure CLI 命令 `az keyvault list --resource-group "MySecureGroup"` 获取该名称|
|  keyEncryptionKeyURL | 用于加密所生成 BitLocker 密钥的密钥加密密钥的 URL。 如果在 UseExistingKek 下拉列表中选择“nokek”，则此参数为可选参数。 如果在 UseExistingKek 下拉列表中选择“kek”，则必须输入 _keyEncryptionKeyURL_ 值。 |
| volumeType | 要对其执行加密操作的卷的类型。 有效值为“OS”、“Data”和“All”。 |
| sequenceVersion | BitLocker 操作的序列版本。 每当在同一个 VM 上执行磁盘加密操作时，此版本号便会递增。 |
| vmName | 要对其执行加密操作的 VM 的名称。 |


## <a name="bkmk_VHDpre"></a>通过客户加密的 VHD 和加密密钥新建的 IaaS VM
在此方案中，可以通过使用资源管理器模板、PowerShell cmdlet 或 CLI 命令启用加密。 以下部分详细介绍了资源管理器模板和 CLI 命令。 

参考附录中的说明来准备可在 Azure 中使用的预加密映像。 创建映像后，可使用下一部分中的步骤创建加密的 Azure VM。

* [准备预加密的 Windows VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="bkmk_VHDprePSH"></a>使用 Azure PowerShell 加密包含预加密 VHD 的 VM
可以使用 PowerShell cmdlet [AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)在加密的 VHD 上启用磁盘加密。 以下示例显示了一些常用参数。 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新添加的数据磁盘上启用加密
可以[使用 PowerShell](attach-disk-ps.md) 或[通过 Azure 门户](attach-managed-disk-portal.md)将新磁盘添加到 Windows VM。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>使用 Azure PowerShell 在新添加的磁盘上启用加密
 使用 Powershell 加密 Windows VM 的新磁盘时，应指定新的序列版本。 序列版本必须唯一。 以下脚本生成序列版本的 GUID。 在某些情况下，Azure 磁盘加密扩展可能会自动加密新添加的数据磁盘。 新磁盘处于联机状态后，在 VM 重新启动时，通常会出现自动加密的情况。 这通常是由于之前在 VM 上运行磁盘加密时将卷类型指定为“全部”。 如果在新添加的数据磁盘上进行自动加密，我们建议使用新的序列版本再次运行 AzVmDiskEncryptionExtension cmdlet。 如果新数据磁盘已自动加密，但并不希望进行加密，请先解密所有驱动器，然后使用为卷类型指定 OS 的新序列版本重新进行加密。 
 

-  **使用客户端机密加密正在运行的 VM：** 下面的脚本初始化变量并运行 AzVMDiskEncryptionExtension cmdlet。 作为先决条件，应已事先创建资源组、VM、Key Vault、AAD 应用和客户端机密。 将 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault、我的 AAD 客户端 ID 和你的 AAD 客户端密码替换为你的值。 本示例使用“All”作为 -VolumeType 参数，其中包含 OS 卷和 Data 卷。 如果只想加密 OS 卷，请使用“OS”作为 -VolumeType 参数。 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **使用 KEK 包装客户端机密，以加密正在运行的 VM：** Azure 磁盘加密允许在 Key Vault 中指定一个现有密钥，用于包装启用加密时生成的磁盘加密机密。 指定密钥加密密钥后，Azure 磁盘加密会使用该密钥包装加密机密，然后将机密写入 Key Vault。 本示例使用“All”作为 -VolumeType 参数，其中包含 OS 卷和 Data 卷。 如果只想加密 OS 卷，请使用“OS”作为 -VolumeType 参数。 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>使用 Azure CLI 在新添加的磁盘上启用加密
  运行 Azure CLI 命令来启用加密时，命令会自动提供新的序列版本。 volume-yype 参数的可接受值为 All、OS 和 Data。 如果只想加密 VM 的一种磁盘，则可能需要将 volume-type 参数更改为 OS 或 Data。 示例使用“All”作为 volume-type 参数。 

-  **使用客户端机密加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **使用 KEK 包装客户端机密，以加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>使用 Azure AD 基于客户端证书的身份验证启用加密。
可以结合或不结合 KEK 使用客户端证书身份验证。 在使用 PowerShell 脚本之前，应已将证书上传到 Key Vault 并已将其部署到 VM。 如果同时使用 KEK，该 KEK 应该存在。 有关详细信息，请参阅先决条件文章的 [Azure AD 基于证书的身份验证](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional)部分。


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>在 Azure PowerShell 中使用基于证书的身份验证启用加密

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>在 Azure PowerShell 中使用基于证书的身份验证和 KEK 启用加密

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>禁用加密功能
可以使用 Azure PowerShell、Azure CLI 或资源管理器模板禁用加密。 

- **使用 Azure PowerShell 禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **使用 Azure CLI 禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **使用资源管理器模板禁用加密：** 

    1. 单击[在正在运行的 Windows VM 上禁用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)模板中的“部署到 Azure”。
    2. 选择订阅、资源组、位置、VM、法律条款和协议。
    3.  单击“购买”，在正在运行的 Windows VM 上禁用磁盘加密。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure 磁盘加密概述](disk-encryption-overview.md)

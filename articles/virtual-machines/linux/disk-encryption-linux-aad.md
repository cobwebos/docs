---
title: 使用 Azure AD 应用 Linux IaaS VM 的 Azure 磁盘加密（以前的版本）
description: 本文提供有关如何为 Linux IaaS VM 启用 Microsoft Azure 磁盘加密的说明。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970610"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>使用 Linux Vm 上的 Azure AD 启用 Azure 磁盘加密（以前的版本）

新版本的 Azure 磁盘加密消除了提供 Azure Active Directory （Azure AD）应用程序参数以启用 VM 磁盘加密的要求。 使用新版本，在执行启用加密步骤时，不再需要提供 Azure AD 凭据。 在不使用新版本的情况下，必须使用 Azure AD 的应用程序参数对所有新的 Vm 进行加密。 有关如何使用新版本启用 VM 磁盘加密的说明，请参阅适用于[LINUX vm 的 Azure 磁盘加密](disk-encryption-linux.md)。 已使用 Azure AD 应用程序参数加密的 VM 仍受支持，应继续使用 AAD 语法进行维护。

您可以启用多种磁盘加密方案，步骤可能因情况而异。 以下部分详细介绍了适用于 Linux 基础结构即服务（IaaS） Vm 的更多方案。 你只能对[受支持的 VM 大小和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)的虚拟机应用磁盘加密。 还必须满足以下先决条件：

- [Vm 的其他要求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [网络和组策略](disk-encryption-overview-aad.md#networking-and-group-policy)
- [加密密钥存储要求](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

在对磁盘进行加密之前，拍摄[快照](snapshot-copy-managed-disk.md)、进行备份或同时执行这两项。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 加密之前，需要备份包含托管磁盘的 VM。 进行备份后，可以使用 AzVMDiskEncryptionExtension cmdlet 通过指定-skipVmBackup 参数来加密托管磁盘。 有关如何备份和还原已加密 Vm 的详细信息，请参阅[Azure 备份](../../backup/backup-azure-vms-encryption.md)。 

>[!WARNING]
 > - 如果以前对[Azure AD 应用使用 Azure 磁盘加密](disk-encryption-overview-aad.md)来加密此 vm，则必须继续使用此选项来加密 vm。 无法在此加密的 VM 上使用[Azure 磁盘加密](disk-encryption-overview.md)，因为这不是受支持的方案，因此尚不支持切换掉此加密 vm 的 Azure AD 应用程序。
 > - 若要确保加密机密不跨越区域边界，Azure 磁盘加密需要密钥保管库，并将 Vm 放在同一区域中。 创建并使用与要加密的 VM 位于同一区域中的密钥保管库。
 > - 加密 Linux OS 卷时，此过程可能需要几个小时。 通常，Linux OS 卷所需的时间超过了用于加密的数据量。
> - 加密 Linux OS 卷时，VM 应被视为不可用。 我们强烈建议你在进行加密的同时避免 SSH 登录，以避免阻止在加密过程中需要访问的任何打开的文件。 若要检查进度，请使用[AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus)或[vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 对于 30 GB 的 OS 卷，此过程可能需要花费几个小时，另外还需要额外的时间来加密数据卷。 数据卷加密时间与数据卷的大小和数量成正比，除非使用 "**加密格式**" 选项。 
 > - 在 Linux VM 上，仅支持对数据卷禁用加密。 如果 OS 卷已加密，则不支持在数据或 OS 卷上。 

 

## <a name="bkmk_RunningLinux"></a>在现有或正在运行的 IAAS Linux VM 上启用加密

在此方案中，可以通过使用 Azure 资源管理器模板、PowerShell cmdlet 或 Azure CLI 命令来启用加密。 

>[!IMPORTANT]
 >在启用 Azure 磁盘加密之前，必须获取快照或备份之外的基于托管磁盘的 VM 实例。 你可以从 Azure 门户获取托管磁盘的快照，或者可以使用[Azure 备份](../../backup/backup-azure-vms-encryption.md)。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 进行备份后，使用 AzVMDiskEncryptionExtension cmdlet 通过指定-skipVmBackup 参数来加密托管磁盘。 在进行备份并指定此参数之前，AzVMDiskEncryptionExtension 命令对基于托管磁盘的 Vm 失败。 
>
>加密或禁用加密可能会导致 VM 重新启动。 
>

### <a name="bkmk_RunningLinuxCLI"></a>使用 Azure CLI 在现有或正在运行的 Linux VM 上启用加密 
可以通过安装和使用[Azure CLI 2.0](/cli/azure)命令行工具，在加密的 VHD 上启用磁盘加密。 可以在浏览器中结合 [Azure Cloud Shell](../../cloud-shell/overview.md) 使用这些 cmdlet，或者将它们安装在本地计算机上并在任何 PowerShell 会话中使用。 若要在 Azure 中现有或正在运行的 IaaS Linux VM 上启用加密，请使用以下 CLI 命令：

使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令在 Azure 中运行的 IaaS 虚拟机上启用加密。

-  **使用客户端机密对正在运行的 VM 进行加密：**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **使用 KEK 加密正在运行的 VM 以包装客户端密钥：**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > 磁盘加密-keyvault 参数的值语法为完整的标识符字符串：/subscriptions/[订阅 id-guid]/resourceGroups/[]/providers/Microsoft.KeyVault/vaults/[keyvault]。</br> </br> 密钥加密-密钥参数值的语法是 KEK 的完整 URI，如下所示： https：//[keyvault]./key/[kekname]/[KEK 唯一 id]。

- **验证磁盘是否已加密：** 若要查看 IaaS VM 的加密状态，请使用[az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 只允许对 Linux VM 的数据卷禁用加密。
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"></a>使用 PowerShell 在现有或正在运行的 Linux VM 上启用加密
使用[AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Cmdlet 在 Azure 中正在运行的 IaaS 虚拟机上启用加密。 在对磁盘进行加密之前，拍摄[快照](snapshot-copy-managed-disk.md)或使用[AZURE 备份](../../backup/backup-azure-vms-encryption.md)创建 VM 的备份。 已在 PowerShell 脚本中指定-skipVmBackup 参数，用于对正在运行的 Linux VM 进行加密。

- **使用客户端机密对正在运行的 VM 进行加密：** 以下脚本将初始化变量并运行 AzVMDiskEncryptionExtension cmdlet。 资源组、VM、密钥保管库、Azure AD 应用和客户端密码应已创建为必备组件。 将 MyVirtualMachineResourceGroup、MyKeyVaultResourceGroup、MySecureVM、MySecureVault、我的 AAD 客户端 ID 和你的 AAD 客户端密码替换为你的值。 修改-将 volumetype 参数，以指定要加密哪些磁盘。

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **使用 KEK 加密正在运行的 VM 以包装客户端密钥：** Azure 磁盘加密允许你指定密钥保管库中的现有密钥，以包装启用加密时生成的磁盘加密机密。 指定密钥加密密钥时，Azure 磁盘加密将使用该密钥在写入到密钥保管库之前包装加密机密。 修改-将 volumetype 参数，以指定要加密哪些磁盘。 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > 磁盘加密-keyvault 参数的值语法为完整的标识符字符串：/subscriptions/[订阅 id-guid]/resourceGroups/[KVresource]/providers/Microsoft.KeyVault/vaults/[keyvault]。</br> </br>
  密钥加密-密钥参数值的语法是 KEK 的完整 URI，如下所示： https：//[keyvault]./key/[kekname]/[KEK 唯一 id]。 
    
- **验证磁盘是否已加密：** 若要检查 IaaS VM 的加密状态，请使用[AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet。 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 只允许对 Linux VM 的数据卷禁用加密。
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"></a>使用模板在现有或正在运行的 IAAS Linux VM 上启用加密

可通过[资源管理器模板模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) 在 Azure 中为现有或正在运行的 IaaS Linux VM 启用磁盘加密。

1. 选择 Azure 快速入门模板上的 "**部署到 azure** "。

2. 选择订阅、资源组、资源组位置、参数、法律条款和协议。 选择 "**创建**" 以在现有或正在运行的 IaaS VM 上启用加密。

下表列出了使用 Azure AD 客户端 ID 的现有或正在运行的 VM 的资源管理器模板参数：

| 参数 | 说明 |
| --- | --- |
| AADClientID | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端 ID。 |
| AADClientSecret | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端机密。 |
| KeyVaultName | 密钥应上传到的 Key Vault 的名称。 可以使用 Azure CLI 命令 `az keyvault show --name "MySecureVault" --query KVresourceGroup` 获取该名称。 |
|  keyEncryptionKeyURL | 用于加密所生成密钥的密钥加密密钥的 URL。 如果在**UseExistingKek**下拉列表中选择 " **nokek** "，则此参数是可选的。 如果在**UseExistingKek**下拉列表中选择 " **kek** "，则必须输入_keyEncryptionKeyURL_值。 |
| volumeType | 要对其执行加密操作的卷的类型。 支持的有效值为 " _OS_ " 或 "_全部_"。 （请参阅前面的先决条件部分中的操作系统和数据磁盘支持的 Linux 发行版及其版本。） |
| sequenceVersion | BitLocker 操作的序列版本。 每当在同一个 VM 上执行磁盘加密操作时，此版本号便会递增。 |
| vmName | 要对其执行加密操作的 VM 的名称。 |
| 通行短语 | 键入强密码作为数据加密密钥。 |



## <a name="bkmk_EFA"></a>将 EncryptFormatAll 功能用于 Linux IaaS vm 上的数据磁盘
EncryptFormatAll 参数可减少 Linux 数据磁盘加密的时间。 满足特定条件的分区将格式化（及其当前的文件系统）。 然后，将它们重新装载到执行命令之前的位置。 如果要排除满足条件的数据磁盘，可以在运行该命令之前卸载它。

 运行此命令后，先前装载的所有驱动器都将进行格式化。 然后从现在为空的驱动器开始加密层。 选中此选项后，附加到 VM 的暂时资源磁盘也会加密。 如果临时驱动器重置，Azure 磁盘加密解决方案将在下一次机会时重新格式化并重新为 VM 重新加密。

>[!WARNING]
> 当 VM 的数据卷上需要数据时，不应使用 EncryptFormatAll。 可以通过卸载来从加密中排除磁盘。 首先尝试测试 VM 上的 EncryptFormatAll 参数，了解功能参数及其含义，然后再在生产 VM 上尝试该参数。 EncryptFormatAll 选项设置数据磁盘的格式，因此，其上的所有数据都将丢失。 在继续操作之前，请验证要排除的所有磁盘是否均已正确卸载。 </br></br>
 >如果在更新加密设置时设置此参数，则可能会在实际加密之前导致重新启动。 在这种情况下，还需要从 fstab 文件中删除不想格式化的磁盘。 同样，在启动加密操作之前，应将需要加密格式的分区添加到 fstab 文件。 

### <a name="bkmk_EFACriteria"></a> EncryptFormatAll 条件
参数会遍历所有分区，并对它们进行加密，只要它们满足以下*所有*条件： 
- 不是根/OS/启动分区
- 尚未加密
- 不是 BEK 卷
- 不是 RAID 卷
- 不是 LVM 卷
- 已装载

加密组成 RAID 或 LVM 卷而不是 RAID 或 LVM 卷的磁盘。

### <a name="bkmk_EFATemplate"></a>在模板中使用 EncryptFormatAll 参数
若要使用 EncryptFormatAll 选项，请使用任何已预先存在的 Azure 资源管理器模板来加密 Linux VM，并更改 AzureDiskEncryption 资源的**EncryptionOperation**字段。

1. 例如，使用[资源管理器模板加密正在运行的 Linux IaaS VM](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)。 
2. 选择 Azure 快速入门模板上的 "**部署到 azure** "。
3. 将**EncryptionOperation**字段从**EnableEncryption**更改为**EnableEncryptionFormatAl**。
4. 选择订阅、资源组、资源组位置、其他参数、法律条款和协议。 选择 "**创建**" 以在现有或正在运行的 IaaS VM 上启用加密。


### <a name="bkmk_EFAPSH"></a>将 EncryptFormatAll 参数与 PowerShell cmdlet 一起使用
将[AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Cmdlet 与 EncryptFormatAll 参数一起使用。

**使用客户端机密和 EncryptFormatAll 加密正在运行的 VM：** 例如，以下脚本将初始化变量，并使用 EncryptFormatAll 参数运行 AzVMDiskEncryptionExtension cmdlet。 资源组、VM、密钥保管库、Azure AD 应用和客户端密码应已创建为必备组件。 将 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault、我的 AAD 客户端 ID 和你的 AAD 客户端密码替换为你的值。
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"></a>将 EncryptFormatAll 参数与逻辑卷管理器（LVM）配合使用 
我们建议采用 LVM-on-crypt 设置。 对于以下所有示例，请将设备路径和装载点替换为你的用例。 可按如下所述完成此设置：

- 添加构成 VM 的数据磁盘。
- 格式化、装载这些磁盘并将其添加到 fstab 文件。

    1. 格式化新添加的磁盘。 此处使用了 Azure 生成的符号链接。 使用符号链接可避免设备名更改所造成的问题。 有关详细信息，请参阅[解决设备名称问题](troubleshoot-device-names-problems.md)。
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. 装载磁盘。
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. 添加到 fstab。
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. 通过-EncryptFormatAll 运行 AzVMDiskEncryptionExtension PowerShell cmdlet 以加密这些磁盘。
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. 在这些新磁盘的顶层设置 LVM。 请注意，VM 在完成启动后，加密的驱动器会解锁。 因此，后续的 LVM 装载必定会延迟。




## <a name="bkmk_VHDpre"></a>通过客户加密的 VHD 和加密密钥创建的新 IaaS vm
在此方案中，可以通过使用资源管理器模板、PowerShell cmdlet 或 CLI 命令启用加密。 以下部分详细介绍了资源管理器模板和 CLI 命令。 

参考附录中的说明来准备可在 Azure 中使用的预加密映像。 创建映像后，可使用下一部分中的步骤创建加密的 Azure VM。

* [准备预加密的 Linux VHD](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >在启用 Azure 磁盘加密之前，必须获取快照或备份之外的基于托管磁盘的 VM 实例。 你可以从门户拍摄托管磁盘的快照，或者可以使用[Azure 备份](../../backup/backup-azure-vms-encryption.md)。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 进行备份后，使用 AzVMDiskEncryptionExtension cmdlet 通过指定-skipVmBackup 参数来加密托管磁盘。 在进行备份并指定此参数之前，AzVMDiskEncryptionExtension 命令对基于托管磁盘的 Vm 失败。 
>
>加密或禁用加密可能会导致 VM 重新启动。



### <a name="bkmk_VHDprePSH"></a>使用 Azure PowerShell 通过预加密 Vhd 对 IaaS vm 进行加密 
可以使用 PowerShell cmdlet [AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)在加密的 VHD 上启用磁盘加密。 下面的示例提供了一些常见参数。 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新添加的数据磁盘上启用加密
可以通过使用[az vm disk attach](add-disk.md)或[Azure 门户](attach-disk-portal.md)来添加新的数据磁盘。 在加密之前，需要先装载新附加的数据磁盘。 你必须请求数据驱动器的加密，因为在进行加密时，该驱动器将不可用。 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>使用 Azure CLI 在新添加的磁盘上启用加密
 如果 VM 之前已通过 "所有" 进行了加密，则--volume 类型参数应始终为 "所有"。 All 包括 OS 和数据磁盘。 如果以前使用 "OS" 卷类型对 VM 进行了加密，则--volume 类型参数应更改为 "全部"，以便包括 OS 和新的数据磁盘。 如果 VM 只是以 "数据" 的类型进行加密，则它可以保留数据，如此处所示。 向 VM 添加新的数据磁盘并将其附加到没有足够的加密准备。 启用加密之前，新附加的磁盘还必须格式化并正确地装载到 VM 中。 在 Linux 上，必须使用[永久性块设备名称](troubleshoot-device-names-problems.md)在/etc/fstab 中装载磁盘。 

与 Powershell 语法不同，CLI 不要求你在启用加密时提供唯一的序列版本。 CLI 自动生成并使用自己唯一的序列版本值。

-  **使用客户端机密对正在运行的 VM 进行加密：** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **使用 KEK 加密正在运行的 VM 以包装客户端密钥：**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>使用 Azure PowerShell 在新添加的磁盘上启用加密
 使用 Powershell 为 Linux 加密新磁盘时，需要指定新的序列版本。 序列版本必须唯一。 下面的脚本为序列版本生成 GUID。 
 

-  **使用客户端机密对正在运行的 VM 进行加密：** 以下脚本将初始化变量并运行 AzVMDiskEncryptionExtension cmdlet。 资源组、VM、密钥保管库、Azure AD 应用和客户端密码应已创建为必备组件。 将 MyVirtualMachineResourceGroup、MyKeyVaultResourceGroup、MySecureVM、MySecureVault、我的 AAD 客户端 ID 和你的 AAD 客户端密码替换为你的值。 -VolumeType 参数设置为数据磁盘而不是 OS 磁盘。 如果以前使用 "OS" 或 "全部" 卷类型对 VM 进行了加密，则应将-将 volumetype 参数更改为 "全部"，以便包括 OS 和新的数据磁盘。

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
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **使用 KEK 加密正在运行的 VM 以包装客户端密钥：** Azure 磁盘加密允许你指定密钥保管库中的现有密钥，以包装启用加密时生成的磁盘加密机密。 指定密钥加密密钥时，Azure 磁盘加密将使用该密钥在写入到密钥保管库之前包装加密机密。 -VolumeType 参数设置为数据磁盘而不是 OS 磁盘。 如果以前使用 "OS" 或 "全部" 卷类型对 VM 进行了加密，则应将-将 volumetype 参数更改为 "全部"，以便包括 OS 和新的数据磁盘。

     ```azurepowershell
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
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> 磁盘加密-keyvault 参数的值语法为完整的标识符字符串：/subscriptions/[订阅 id-guid]/resourceGroups/[]/providers/Microsoft.KeyVault/vaults/[keyvault]。 </br> </br>
密钥加密-密钥参数值的语法是 KEK 的完整 URI，如下所示： https：//[keyvault]./key/[kekname]/[KEK 唯一 id]。

## <a name="disable-encryption-for-linux-vms"></a>为 Linux VM 禁用加密
您可以使用 Azure PowerShell、Azure CLI 或资源管理器模板禁用加密。 

>[!IMPORTANT]
>在 Linux VM 上，仅支持对数据卷禁用 Azure 磁盘加密。 如果 OS 卷已加密，则不支持在数据或 OS 卷上。 

- **通过 Azure PowerShell 禁用磁盘加密：** 若要禁用加密，请使用[AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **使用 Azure CLI 禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **使用资源管理器模板禁用加密：** 若要禁用加密，请使用 "[在正在运行的 LINUX VM 上禁用加密](https://aka.ms/decrypt-linuxvm)" 模板。
     1. 选择 "**部署到 Azure**"。
     2. 选择订阅、资源组、位置、VM、法律条款和协议。
     3. 选择 "**购买**"，禁用正在运行的 Windows VM 上的磁盘加密。 


## <a name="next-steps"></a>后续步骤

- [适用于 Linux 的 Azure 磁盘加密概述](disk-encryption-overview-aad.md)
- [使用 Azure AD 为 Azure 磁盘加密创建和配置密钥保管库（以前的版本）](disk-encryption-key-vault-aad.md)

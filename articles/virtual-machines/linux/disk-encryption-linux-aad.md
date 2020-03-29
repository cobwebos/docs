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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970610"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>在 Linux VM 上使用 Azure AD 启用 Azure 磁盘加密（以前版本）

Azure 磁盘加密的新版本消除了提供 Azure 活动目录 （Azure AD） 应用程序参数以启用 VM 磁盘加密的要求。 使用新版本，在执行启用加密步骤时，不再需要提供 Azure AD 凭据。 使用新版本，必须对所有新 VM 进行加密，而无需 Azure AD 应用程序参数。 有关如何使用新版本启用 VM 磁盘加密的说明，请参阅[Linux VMS 的 Azure 磁盘加密](disk-encryption-linux.md)。 已使用 Azure AD 应用程序参数加密的 VM 仍受支持，应继续使用 AAD 语法进行维护。

您可以启用许多磁盘加密方案，并且步骤可能因方案而异。 以下各节更详细地介绍 Linux 基础架构作为服务 （IaaS） VM 的方案。 只能对具有[支持的 VM 大小和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)的虚拟机应用磁盘加密。 还必须满足以下先决条件：

- [VM 的其他要求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [网络和组策略](disk-encryption-overview-aad.md#networking-and-group-policy)
- [加密密钥存储要求](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

在加密磁盘之前，先拍摄[快照](snapshot-copy-managed-disk.md)、进行备份或两者兼而有之。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 加密之前，需要备份包含托管磁盘的 VM。 进行备份后，可以使用 Set-AzVMDisk 加密扩展 cmdlet 通过指定 -skipVmBackup 参数来加密托管磁盘。 有关如何备份和还原加密 VM 的详细信息，请参阅[Azure 备份](../../backup/backup-azure-vms-encryption.md)。 

>[!WARNING]
 > - 如果以前将[Azure 磁盘加密与 Azure AD 应用一起使用](disk-encryption-overview-aad.md)以加密此 VM，则必须继续使用此选项对 VM 进行加密。 不能在此加密 VM 上使用[Azure 磁盘加密](disk-encryption-overview.md)，因为这是受支持的方案，这意味着尚不支持出于此加密 VM 从 Azure AD 应用程序切换。
 > - 为了确保加密机密不会跨越区域边界，Azure 磁盘加密需要密钥保管库和 VM 在同一区域中共存。 创建并使用与 VM 位于同一区域的密钥保管库进行加密。
 > - 加密 Linux 操作系统卷时，此过程可能需要几个小时。 Linux 操作系统卷加密的时间比数据卷长是正常的。
> - 加密 Linux 操作系统卷时，应认为 VM 不可用。 我们强烈建议您在加密过程中避免 SSH 登录，以避免阻止加密过程中需要访问的任何打开的文件。 要检查进度，请使用[获取-AzVM 磁盘加密状态](/powershell/module/az.compute/get-azvmdiskencryptionstatus)或[vm 加密显示命令](/cli/azure/vm/encryption#az-vm-encryption-show)。 对于 30 GB 的操作系统卷，此过程需要几个小时，此外还有额外的时间来加密数据卷。 数据卷加密时间与数据卷的大小和数量成正比，除非使用**加密格式所有**选项。 
 > - 在 Linux VM 上，仅支持对数据卷禁用加密。 如果操作系统卷已加密，则数据或操作系统卷不支持它。 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a>在现有或正在运行的 IaaS Linux VM 上启用加密

在这种情况下，可以使用 Azure 资源管理器模板、PowerShell cmdlet 或 Azure CLI 命令启用加密。 

>[!IMPORTANT]
 >在启用 Azure 磁盘加密之前，必须在快照或备份基于磁盘的托管 VM 实例之前进行备份。 可以从 Azure 门户拍摄托管磁盘的快照，也可以使用[Azure 备份](../../backup/backup-azure-vms-encryption.md)。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 进行备份后，使用 Set-AzVMDisk 加密扩展 cmdlet 通过指定 -skipVmBackup 参数来加密托管磁盘。 Set-AzVMDisk加密扩展命令对基于磁盘的托管 VM 失败，直到进行备份并指定此参数。 
>
>加密或禁用加密可能会导致 VM 重新启动。 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>使用 Azure CLI 在现有或正在运行的 Linux VM 上启用加密 
您可以通过安装和使用[Azure CLI 2.0](/cli/azure)命令行工具在加密的 VHD 上启用磁盘加密。 可以在浏览器中结合 [Azure Cloud Shell](../../cloud-shell/overview.md) 使用这些 cmdlet，或者将它们安装在本地计算机上并在任何 PowerShell 会话中使用。 若要在 Azure 中现有或正在运行的 IaaS Linux VM 上启用加密，请使用以下 CLI 命令：

使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令在 Azure 中运行的 IaaS 虚拟机上启用加密。

-  **使用客户端密钥加密正在运行的 VM：**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **使用 KEK 对正在运行的 VM 进行加密以包装客户端密钥：**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > 磁盘加密密钥库参数的值的语法是完整的标识符字符串：/订阅/[订阅-id-guid]/资源组组/[资源组名称]/提供程序/Microsoft.KeyVault/vault/[密钥库名称]。</br> </br> 密钥加密密钥参数的值的语法是 KEK 的完整 URI，如： https：//[密钥库名称]vault.azure.net/密钥/[kekname]/[kek-唯一-id]。

- **验证磁盘是否加密：** 要检查 IaaS VM 的加密状态，请使用[az vm 加密显示](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 只允许对 Linux VM 的数据卷禁用加密。
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a>使用 PowerShell 在现有或正在运行的 Linux VM 上启用加密
使用 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 在 Azure 中运行的 IaaS 虚拟机上启用加密。 在加密磁盘之前，使用[Azure 备份](../../backup/backup-azure-vms-encryption.md)拍摄[快照](snapshot-copy-managed-disk.md)或备份 VM。 已在 PowerShell 脚本中指定 -skipVmBackup 参数以加密正在运行的 Linux VM。

- **使用客户端密钥加密正在运行的 VM：** 以下脚本初始化变量并运行 Set-AzVM 磁盘加密扩展 cmdlet。 资源组、VM、密钥保管库、Azure AD 应用和客户端机密应已创建为先决条件。 将 MyVirtualMachineResourceGroup、MyKeyVaultResourceGroup、MySecureVM、MySecureVault、My-AAD-client-ID 和 My-AAD-client-secret 替换为自己的值。 修改 -VolumeType 参数，以指定要加密哪些磁盘。

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
- **使用 KEK 对正在运行的 VM 进行加密以包装客户端密钥：** Azure 磁盘加密允许您在密钥保管库中指定现有密钥，以包装启用加密时生成的磁盘加密机密。 指定密钥加密密钥后，Azure 磁盘加密将使用该密钥在写入密钥保管库之前隐藏加密机密。 修改 -VolumeType 参数，以指定要加密哪些磁盘。 

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
  > 磁盘加密密钥保管参数的值的语法是完整的标识符字符串：/订阅/[订阅-id-guid]/资源组/[KV资源组名称]/提供程序/Microsoft.KeyVault/vault/[密钥库名称]。</br> </br>
  密钥加密密钥参数的值的语法是 KEK 的完整 URI，如： https：//[密钥库名称]vault.azure.net/密钥/[kekname]/[kek-唯一-id]。 
    
- **验证磁盘是否加密：** 要检查 IaaS VM 的加密状态，请使用[获取-AzVm 磁盘加密状态](/powershell/module/az.compute/get-azvmdiskencryptionstatus)cmdlet。 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 只允许对 Linux VM 的数据卷禁用加密。
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a>使用模板在现有或正在运行的 IaaS Linux VM 上启用加密

可通过[资源管理器模板模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) 在 Azure 中为现有或正在运行的 IaaS Linux VM 启用磁盘加密。

1. 在 Azure 快速启动模板上选择 **"部署到 Azure"。**

2. 选择订阅、资源组、资源组位置、参数、法律条款和协议。 选择 **"创建**"可在现有或正在运行的 IaaS VM 上启用加密。

下表列出了使用 Azure AD 客户端 ID 的现有或正在运行的 VM 的资源管理器模板参数：

| 参数 | 描述 |
| --- | --- |
| AADClientID | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端 ID。 |
| AADClientSecret | 有权将机密写入 Key Vault 的 Azure AD 应用程序的客户端机密。 |
| KeyVaultName | 密钥应上传到的 Key Vault 的名称。 可以使用 Azure CLI 命令 `az keyvault show --name "MySecureVault" --query KVresourceGroup` 获取该名称。 |
|  keyEncryptionKeyURL | 用于加密所生成密钥的密钥加密密钥的 URL。 如果在 **"使用现有 Kek"** 下拉列表中选择**nokek，** 则此参数是可选的。 如果在 **"使用现有 Kek"** 下拉列表中选择**kek，** 则必须输入_键加密 KeyURL_值。 |
| volumeType | 要对其执行加密操作的卷的类型。 支持的有效值为_操作系统_或_全部_。 （请参阅前面先决条件部分中支持的 Linux 发行版及其操作系统和数据磁盘版本。 |
| sequenceVersion | BitLocker 操作的序列版本。 每当在同一个 VM 上执行磁盘加密操作时，此版本号便会递增。 |
| vmName | 要对其执行加密操作的 VM 的名称。 |
| 通行短语 | 键入强密码作为数据加密密钥。 |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>在 Linux IaaS VM 上对数据磁盘使用加密格式所有功能
EncryptFormatAll 参数可以减少加密 Linux 数据磁盘所需的时间。 满足特定条件的分区设置格式（及其当前文件系统）。 然后，它们被重新安装回命令执行之前的位置。 如果要排除满足条件的数据磁盘，可以在运行命令之前将其卸载。

 运行此命令后，以前装载的任何驱动器都设置格式化。 然后，加密层从现在空的驱动器顶部开始。 选择此选项后，附加到 VM 的短暂资源磁盘也会加密。 如果临时驱动器被重置，则下次由 Azure 磁盘加密解决方案对其进行重新格式化和重新加密 VM。

>[!WARNING]
> 当 VM 的数据卷上需要数据时，不应使用加密格式所有。 您可以通过卸载磁盘从加密中排除磁盘。 在生产 VM 上尝试之前，请先在测试 VM 上试用加密格式All 参数，以了解功能参数及其含义。 "加密格式所有"选项格式化数据磁盘，因此数据磁盘上的所有数据都将丢失。 在继续操作之前，请验证要排除的任何磁盘是否正确卸载。 </br></br>
 >如果在更新加密设置时设置此参数，则可能导致在实际加密之前重新启动。 在这种情况下，您还希望从 fstab 文件中删除不希望格式化的磁盘。 同样，在启动加密操作之前，应将要加密格式化的分区添加到 fstab 文件中。 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a>EncryptFormatAll 的条件
参数遍经所有分区并加密它们，只要它们满足以下*所有*条件： 
- 不是根/OS/启动分区
- 尚未加密
- 不是 BEK 卷
- 不是 RAID 卷
- 不是 LVM 卷
- 已装载

加密组成 RAID 或 LVM 卷而不是 RAID 或 LVM 卷的磁盘。

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a>结合模板使用 EncryptFormatAll 参数
要使用"加密格式全部"选项，请使用加密 Linux VM 的任何预先存在的 Azure 资源管理器模板，并更改 AzureDisk 加密资源的**加密操作**字段。

1. 例如，使用[资源管理器模板加密正在运行的 Linux IaaS VM](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)。 
2. 在 Azure 快速启动模板上选择 **"部署到 Azure"。**
3. 将**加密操作**字段从**启用加密**更改为**启用加密格式**。
4. 选择订阅、资源组、资源组位置、其他参数、法律条款和协议。 选择 **"创建**"可在现有或正在运行的 IaaS VM 上启用加密。


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a>结合 PowerShell cmdlet 使用 EncryptFormatAll 参数
结合 EncryptFormatAll 参数使用 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet。

**使用客户端密钥和加密格式对所有正在运行的 VM 进行加密：** 例如，以下脚本初始化变量，并使用加密格式All参数运行 Set-AzVMDisk加密扩展 cmdlet。 资源组、VM、密钥保管库、Azure AD 应用和客户端机密应已创建为先决条件。 将 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault、My-AAD-client-ID 和 My-AAD-client-secret 替换为自己的值。
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a>结合逻辑卷管理器 (LVM) 使用 EncryptFormatAll 参数 
我们建议采用 LVM-on-crypt 设置。 对于以下所有示例，请将设备路径和装载点替换为任何适合您的用例。 可按如下所述完成此设置：

- 添加构成 VM 的数据磁盘。
- 格式化、装载这些磁盘并将其添加到 fstab 文件。

    1. 格式化新添加的磁盘。 此处使用了 Azure 生成的符号链接。 使用符号链接可避免设备名更改所造成的问题。 有关详细信息，请参阅[排除设备名称问题的疑难解答](troubleshoot-device-names-problems.md)。
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. 装载磁盘。
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. 添加到 fstab。
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. 结合 -EncryptFormatAll 运行 Set-AzVMDiskEncryptionExtension PowerShell cmdlet，以加密这些磁盘。
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. 在这些新磁盘的顶层设置 LVM。 请注意，VM 在完成启动后，加密的驱动器会解锁。 因此，后续的 LVM 装载必定会延迟。




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>通过客户加密的 VHD 和加密密钥新建的 IaaS VM
在此方案中，可以通过使用资源管理器模板、PowerShell cmdlet 或 CLI 命令启用加密。 以下部分详细介绍了资源管理器模板和 CLI 命令。 

参考附录中的说明来准备可在 Azure 中使用的预加密映像。 创建映像后，可使用下一部分中的步骤创建加密的 Azure VM。

* [准备预加密的 Linux VHD](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >在启用 Azure 磁盘加密之前，必须在快照或备份基于磁盘的托管 VM 实例之前进行备份。 可以从门户拍摄托管磁盘的快照，也可以使用[Azure 备份](../../backup/backup-azure-vms-encryption.md)。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 进行备份后，使用 Set-AzVMDisk 加密扩展 cmdlet 通过指定 -skipVmBackup 参数来加密托管磁盘。 Set-AzVMDisk加密扩展命令对基于磁盘的托管 VM 失败，直到进行备份并指定此参数。 
>
>加密或禁用加密可能会导致 VM 重新启动。



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a>使用 Azure PowerShell 加密包含预加密 VHD 的 IaaS VM 
可以使用 PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) 在加密的 VHD 上启用磁盘加密。 下面的示例提供了一些常见参数。 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新添加的数据磁盘上启用加密
可以使用[az vm 磁盘附加](add-disk.md)或通过[Azure 门户](attach-disk-portal.md)添加新数据磁盘。 在加密之前，需要先装载新附加的数据磁盘。 您必须请求数据驱动器的加密，因为在加密进行期间，驱动器将不可用。 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>使用 Azure CLI 在新添加的磁盘上启用加密
 如果 VM 以前使用"全部"加密，则 --体积类型参数应保持全部。 All 包括 OS 和数据磁盘。 如果 VM 以前使用"OS"的卷类型进行加密，则应将 --卷类型参数更改为"全部"，以便同时包含操作系统和新数据磁盘。 如果 VM 仅使用"数据"的卷类型进行加密，则可以保留数据，如下所示。 向 VM 添加新数据磁盘并附加新数据磁盘不足以为加密做好准备。 在启用加密之前，还必须格式化新连接的磁盘并将其正确安装在 VM 中。 在 Linux 上，磁盘必须装入 /etc/fstab 中，带有[持久块设备名称](troubleshoot-device-names-problems.md)。 

与 Powershell 语法相比，CLI 不需要您在启用加密时提供唯一的序列版本。 CLI 自动生成并使用自己唯一的序列版本值。

-  **使用客户端密钥加密正在运行的 VM：** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **使用 KEK 对正在运行的 VM 进行加密以包装客户端密钥：**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>使用 Azure PowerShell 在新添加的磁盘上启用加密
 当您使用 Powershell 加密 Linux 的新磁盘时，需要指定新的序列版本。 序列版本必须唯一。 以下脚本为序列版本生成 GUID。 
 

-  **使用客户端密钥加密正在运行的 VM：** 以下脚本初始化变量并运行 Set-AzVM 磁盘加密扩展 cmdlet。 资源组、VM、密钥保管库、Azure AD 应用和客户端机密应已创建为先决条件。 将 MyVirtualMachineResourceGroup、MyKeyVaultResourceGroup、MySecureVM、MySecureVault、My-AAD-client-ID 和 My-AAD-client-secret 替换为自己的值。 -VolumeType 参数设置为数据磁盘而不是 OS 磁盘。 如果 VM 以前使用"OS"或"全部"的卷类型进行加密，则应将 -VolumeType 参数更改为"全部"，以便同时包含操作系统和新数据磁盘。

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
- **使用 KEK 对正在运行的 VM 进行加密以包装客户端密钥：** Azure 磁盘加密允许您在密钥保管库中指定现有密钥，以包装启用加密时生成的磁盘加密机密。 指定密钥加密密钥后，Azure 磁盘加密将使用该密钥在写入密钥保管库之前隐藏加密机密。 -VolumeType 参数设置为数据磁盘而不是 OS 磁盘。 如果 VM 以前使用"OS"或"全部"的卷类型进行加密，则应将 -VolumeType 参数更改为"全部"，以便同时包含操作系统和新数据磁盘。

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
> 磁盘加密密钥库参数的值的语法是完整的标识符字符串：/订阅/[订阅-id-guid]/资源组组/[资源组名称]/提供程序/Microsoft.KeyVault/vault/[密钥库名称]。 </br> </br>
密钥加密密钥参数的值的语法是 KEK 的完整 URI，如： https：//[密钥库名称]vault.azure.net/密钥/[kekname]/[kek-唯一-id]。

## <a name="disable-encryption-for-linux-vms"></a>为 Linux VM 禁用加密
可以使用 Azure PowerShell、Azure CLI 或资源管理器模板禁用加密。 

>[!IMPORTANT]
>在 Linux VM 上，仅支持对数据卷禁用 Azure 磁盘加密。 如果操作系统卷已加密，则数据或操作系统卷不支持它。 

- **使用 Azure PowerShell 禁用磁盘加密：** 要禁用加密，请使用[禁用 AzureRmVM 磁盘加密](/powershell/module/az.compute/disable-azvmdiskencryption)cmdlet。 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **使用 Azure CLI 禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **使用资源管理器模板禁用加密：** 要禁用加密，请使用正在运行的 Linux VM 模板[上的禁用加密](https://aka.ms/decrypt-linuxvm)。
     1. 选择 **"部署到 Azure**"。
     2. 选择订阅、资源组、位置、VM、法律条款和协议。
     3. 选择 **"购买"** 以禁用正在运行的 Windows VM 上的磁盘加密。 


## <a name="next-steps"></a>后续步骤

- [适用于 Linux 的 Azure 磁盘加密概述](disk-encryption-overview-aad.md)
- [使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（以前版本）](disk-encryption-key-vault-aad.md)

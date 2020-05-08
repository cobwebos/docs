---
title: Windows VM 上的 Azure 磁盘加密方案
description: 本文介绍如何为各种方案的 Windows Vm 启用 Microsoft Azure 磁盘加密
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 4509c62b15eb06c89fe80555a26773fdd3876e66
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790892"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Windows VM 上的 Azure 磁盘加密方案

适用于 Windows 虚拟机（Vm）的 Azure 磁盘加密使用 Windows 的 BitLocker 功能提供操作系统磁盘和数据磁盘的完整磁盘加密。 此外，当将 volumetype 参数为 All 时，它提供临时磁盘的加密。

Azure 磁盘加密[与 Azure Key Vault 集成](disk-encryption-key-vault.md)，帮助你控制和管理磁盘加密密钥与机密。 有关该服务的概述，请参阅[适用于 Windows VM 的 Azure 磁盘加密](disk-encryption-overview.md)。

只能对具有[支持的 VM 大小和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)的虚拟机应用磁盘加密。 还必须满足以下先决条件：

- [网络要求](disk-encryption-overview.md#networking-requirements)
- [组策略要求](disk-encryption-overview.md#group-policy-requirements)
- [加密密钥存储要求](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - 如果之前是使用 Azure 磁盘加密与 Azure AD 来加密 VM，则必须继续使用此选项来加密 VM。 有关详细信息，请参阅[使用 Azure AD 进行的 Azure 磁盘加密（以前的版本）](disk-encryption-overview-aad.md)。 
>
> - 在对磁盘进行加密之前，应该[创建快照](snapshot-copy-managed-disk.md)和/或备份。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 加密之前，需要备份包含托管磁盘的 VM。 备份之后，可以通过指定 -skipVmBackup 参数，使用 [Set-AzVMDiskEncryptionExtension cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension) 来加密托管磁盘。 有关如何备份和还原加密型 VM 的详细信息，请参阅[备份和还原加密型 Azure VM](../../backup/backup-azure-vms-encryption.md)。 
>
> - 加密或禁用加密可能导致 VM 重新启动。

## <a name="install-tools-and-connect-to-azure"></a>安装工具并连接到 Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>在现有或正在运行的 Windows VM 上启用加密
在此方案中，可以使用资源管理器模板、PowerShell cmdlet 或 CLI 命令启用加密。 如果需要虚拟机扩展的架构信息，请参阅[适用于 Windows 扩展的 Azure 磁盘加密](../extensions/azure-disk-enc-windows.md)一文。

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>使用 Azure PowerShell 在现有或正在运行的 VM 上启用加密 
使用 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 在 Azure 中运行的 IaaS 虚拟机上启用加密。 

-  **加密正在运行的 VM：** 以下脚本初始化变量并运行 Set-AzVMDiskEncryptionExtension cmdlet。 先决条件是事先创建资源组、VM 和密钥保管库。 请将 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM 和 MySecureVault 替换为自己的值。

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **使用 KEK 加密正在运行的 VM：** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **验证磁盘是否已加密：** 若要检查 IaaS VM 的加密状态，请使用[AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet。 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 当 OS 和数据磁盘都已加密时，无法按预期在 Windows VM 上禁用数据磁盘加密。 请改为在所有磁盘上禁用加密。

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>使用 Azure CLI 在现有或正在运行的 VM 上启用加密
使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令在 Azure 中运行的 IaaS 虚拟机上启用加密。

- **加密正在运行的 VM：**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **使用 KEK 加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **验证磁盘是否已加密：** 若要查看 IaaS VM 的加密状态，请使用[az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 当 OS 和数据磁盘都已加密时，无法按预期在 Windows VM 上禁用数据磁盘加密。 请改为在所有磁盘上禁用加密。

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>使用资源管理器模板

可以通过使用[资源管理器模板加密正在运行的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)，在 Azure 中现有或正在运行的 IaaS Windows VM 上启用磁盘加密。


1. 在 Azure 快速入门模板中，单击“部署到 Azure”。 

2. 选择订阅、资源组、位置、设置、法律条款和协议。 单击“购买”，在现有或正在运行的 IaaS VM 上启用加密。****

下表列出了现有或正在运行的 VM 的资源管理器模板参数：

| 参数 | 说明 |
| --- | --- |
| vmName | 运行加密操作的 VM 的名称。 |
| KeyVaultName | BitLocker 密钥应上传到的 Key Vault 的名称。 可使用 cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` 或 Azure CLI 命令 `az keyvault list --resource-group "MyKeyVaultResourceGroup"` 获取该名称|
| keyVaultResourceGroup | 包含密钥保管库的资源组的名称|
|  keyEncryptionKeyURL | 密钥加密密钥的 URL，格式为&lt;https://keyvault&gt;. vault.azure.net/key/。&lt;&gt; 如果不想要使用 KEK，请将此字段留空。 |
| volumeType | 要对其执行加密操作的卷的类型。 有效值为“OS”  、“Data”  和“All”  。 
| forceUpdateTag | 每次操作需要强制运行时，传入一个像 GUID 这样的唯一值。 |
| resizeOSDisk | 在拆分系统卷之前，是否应调整 OS 分区大小以占用整个 OS VHD。 |
| location | 所有资源的位置。 |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>通过客户加密的 VHD 和加密密钥新建的 IaaS VM

在此方案中，可以使用 PowerShell cmdlet 或 CLI 命令通过预加密的 VHD 和关联的加密密钥来创建新的 VM。 

请按照[准备预加密的 Windows VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd) 中的说明操作。 创建映像后，可使用下一部分中的步骤创建加密的 Azure VM。


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>使用 Azure PowerShell 加密包含预加密 VHD 的 VM
可以使用 PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) 在加密的 VHD 上启用磁盘加密。 以下示例显示了一些常用参数。 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新添加的数据磁盘上启用加密
可以[使用 PowerShell](attach-disk-ps.md) 或[通过 Azure 门户](attach-managed-disk-portal.md)将新磁盘添加到 Windows VM。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>使用 Azure PowerShell 在新添加的磁盘上启用加密
 使用 PowerShell 为 Windows Vm 加密新磁盘时，应指定新的序列版本。 序列版本必须唯一。 以下脚本生成序列版本的 GUID。 在某些情况下，Azure 磁盘加密扩展可能会自动加密新添加的数据磁盘。 新磁盘处于联机状态后，在 VM 重新启动时，通常会出现自动加密的情况。 这通常是由于之前在 VM 上运行磁盘加密时将卷类型指定为“全部”。 如果新添加的数据磁盘上发生自动加密的情况，我们建议结合新序列版本再次运行 Set-AzVmDiskEncryptionExtension cmdlet。 如果新数据磁盘已自动加密，但并不希望进行加密，请先解密所有驱动器，然后使用为卷类型指定 OS 的新序列版本重新进行加密。 
  
 

-  **加密正在运行的 VM：** 以下脚本初始化变量并运行 Set-AzVMDiskEncryptionExtension cmdlet。 先决条件是事先创建资源组、VM 和密钥保管库。 请将 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM 和 MySecureVault 替换为自己的值。 本示例使用“All”作为 -VolumeType 参数，其中包含 OS 卷和 Data 卷。 如果只想加密 OS 卷，请使用“OS”作为 -VolumeType 参数。 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **使用 KEK 加密正在运行的 VM：** 本示例使用“All”作为 -VolumeType 参数，其中包含 OS 卷和 Data 卷。 如果只想加密 OS 卷，请使用“OS”作为 -VolumeType 参数。

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>使用 Azure CLI 在新添加的磁盘上启用加密
 运行 Azure CLI 命令来启用加密时，命令会自动提供新的序列版本。 本示例使用“All”作为 volume-type 参数。 如果只加密 OS 磁盘，则可能需要将 volume-type 参数更改为 OS。 与 PowerShell 语法不同，CLI 不要求用户在启用加密时提供唯一的序列版本。 CLI 自动生成并使用自己唯一的序列版本值。   

-  **加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **使用 KEK 加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>禁用加密功能
可以使用 Azure PowerShell、Azure CLI 或资源管理器模板禁用加密。 当 OS 和数据磁盘都已加密时，无法按预期在 Windows VM 上禁用数据磁盘加密。 请改为在所有磁盘上禁用加密。

- **使用 Azure PowerShell 禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **使用 Azure CLI 禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **使用资源管理器模板禁用加密：** 

    1. 单击[在正在运行的 Windows VM 上禁用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)模板中的“部署到 Azure”。****
    2. 选择订阅、资源组、位置、VM、卷类型、法律条款和协议。
    3.  单击“购买”，在正在运行的 Windows VM 上禁用磁盘加密。  

## <a name="unsupported-scenarios"></a>不支持的方案

Azure 磁盘加密不支持以下方案、功能和技术：

- 加密通过经典 VM 创建方法创建的基本层 VM。
- 加密使用基于软件的 RAID 系统配置的 VM。
- 加密配置了存储空间直通 (S2D) 的 VM，或配置了 Windows 存储空间的、版本低于 2016 的 Windows Server。
- 与本地密钥管理系统集成。
- Azure 文件（共享文件系统）。
- 网络文件系统 (NFS)。
- 动态卷。
- Windows Server 容器，为每个容器创建动态卷。
- 临时 OS 磁盘。
- 加密共享/分布式文件系统，包括但不限于 DFS、GFS、DRDB 和 CephFS。
- 将已加密的 Vm 移动到其他订阅。
- Gen2 VM（请参阅：[Azure 对第 2 代 VM 的支持](generation-2.md#generation-1-vs-generation-2-capabilities)）
- Lsv2 系列 Vm （请参阅： [Lsv2 系列](../lsv2-series.md)）

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密概述](disk-encryption-overview.md)
- [Azure 磁盘加密示例脚本](disk-encryption-sample-scripts.md)
- [Azure 磁盘加密疑难解答](disk-encryption-troubleshooting.md)

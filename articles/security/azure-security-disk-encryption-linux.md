---
title: 为 Linux IaaS VM 启用 Azure 磁盘加密
description: 本文提供有关如何为 Linux IaaS VM 启用 Microsoft Azure 磁盘加密的说明。
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/17/2018
ms.custom: seodec18
ms.openlocfilehash: 749c139e35118ac8b83281bd255b152f61accc0d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542554"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>为 Linux IaaS VM 启用 Azure 磁盘加密 

可启用多种磁盘加密方案，具体步骤因方案而异。 以下部分更加详细地介绍了适用于 Linux IaaS VM 的方案。 在使用磁盘加密之前，需要满足 [Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)，同时，应该查看[适用于 Linux IaaS VM 的其他先决条件](azure-security-disk-encryption-prerequisites.md#bkmk_LinuxPrereq)部分。

加密磁盘之前创建[快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)和/或备份。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 加密之前，需要备份包含托管磁盘的 VM。 备份之后，可以通过指定 -skipVmBackup 参数，使用 Set-AzureRmVMDiskEncryptionExtension cmdlet 来加密托管磁盘。 有关如何备份和还原已加密 VM 的详细信息，请参阅 [Azure 备份](../backup/backup-azure-vms-encryption.md)一文。 

>[!WARNING]
> - 如果你以前使用 [Azure 磁盘加密与 Azure AD 应用](azure-security-disk-encryption-prerequisites-aad.md)来加密此 VM，则必须继续使用此选项来加密 VM。 你无法在此加密的 VM 上使用 [Azure 磁盘加密](azure-security-disk-encryption-prerequisites.md)，因为这不是受支持的方案，这意味着尚不支持从 AAD 应用程序切换此加密的 VM。
 > - Azure 磁盘加密要求 Key Vault 和 VM 共存于同一区域中。 在要加密的 VM 所在的同一区域中创建并使用 Key Vault。
> - 加密 Linux OS 卷的过程可能需要几个小时。 加密 Linux OS 卷所需的时间比加密数据卷要长，这是正常的。 
> - 在 Linux VM 上，仅支持对数据卷禁用加密。 如果 OS 卷已加密，则不支持对数据卷或 OS 卷禁用加密。  


## <a name="bkmk_RunningLinux"></a>在现有或正在运行的 IaaS Linux VM 上启用加密
在此方案中，可以使用资源管理器模板、PowerShell cmdlet 或 CLI 命令启用加密。 如果需要虚拟机扩展的架构信息，请参阅[适用于 Linux 扩展的 Azure 磁盘加密](../virtual-machines/extensions/azure-disk-enc-linux.md)一文。

>[!IMPORTANT]
 >启用 Azure 磁盘加密之前，必须在其外部创建基于托管磁盘的 VM 实例的快照和/或备份。 可以从门户创建托管磁盘的快照，也可以使用 [Azure 备份](../backup/backup-azure-vms-encryption.md)。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 备份后，可以通过指定 -skipVmBackup 参数，使用 Set-AzureRmVMDiskEncryptionExtension cmdlet 来加密托管磁盘。 在未备份基于托管磁盘的 VM 且未指定此参数的情况下，对 VM 使用 Set-AzureRmVMDiskEncryptionExtension 命令会失败。 
>
>加密或禁用加密可能导致 VM 重新启动。 
>

### <a name="bkmk_RunningLinuxCLI"></a>使用 Azure CLI 在现有或正在运行的 Linux VM 上启用加密 
可通过安装并使用 [Azure CLI 2.0](/cli/azure) 命令行工具在加密的 VHD 上启用磁盘加密。 可以在浏览器中结合 [Azure Cloud Shell](../cloud-shell/overview.md) 使用这些 cmdlet，或者将它们安装在本地计算机上并在任何 PowerShell 会话中使用。 若要在 Azure 中现有或正在运行的 IaaS Linux VM 上启用加密，请使用以下 CLI 命令：

使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令在 Azure 中运行的 IaaS 虚拟机上启用加密。

-  **加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **使用 KEK 加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **验证磁盘是否已加密：** 若要检查 IaaS VM 的加密状态，请使用 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 命令。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 只允许对 Linux VM 的数据卷禁用加密。

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"></a>使用 PowerShell 在现有或正在运行的 Linux VM 上启用加密
使用 [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) cmdlet 在 Azure 中运行的 IaaS 虚拟机上启用加密。 

-  **加密正在运行的 VM：** 以下脚本初始化变量并运行 Set-AzureRmVMDiskEncryptionExtension cmdlet。 先决条件是事先创建资源组、VM 和密钥保管库。 将 MySecureRg、MySecureVM 和 MySecureVault 替换为你的值。 如果加密的是数据磁盘而不是 OS 磁盘，可能需要添加 -VolumeType 参数。 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **使用 KEK 加密正在运行的 VM：** 如果加密的是数据磁盘而不是 OS 磁盘，可能需要添加 -VolumeType 参数。 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **验证磁盘是否已加密：** 若要检查 IaaS VM 的加密状态，请使用 [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) cmdlet。 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet。 只允许对 Linux VM 的数据卷禁用加密。
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"></a>使用模板在现有或正在运行的 IaaS Linux VM 上启用加密

可通过[资源管理器模板模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad) 在 Azure 中为现有或正在运行的 IaaS Linux VM 启用磁盘加密。

1. 在 Azure 快速入门模板中，单击“部署到 Azure”。

2. 选择订阅、资源组、资源组位置、参数、法律条款和协议。 单击“创建”，在现有或正在运行的 IaaS VM 上启用加密。

下表列出了现有的或正在运行的 VM 的资源管理器模板参数：

| 参数 | Description |
| --- | --- |
| vmName | 运行加密操作的 VM 的名称。 |
| KeyVaultName | BitLocker 密钥应上传到的 Key Vault 的名称。 可以使用 cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` 或 Azure CLI 命令 `az keyvault list --resource-group "MySecureGroup" 获取该名称 |ConvertFrom-JSON`|
| keyVaultResourceGroup | 包含密钥保管库的资源组的名称|
|  keyEncryptionKeyURL | 用于加密所生成 BitLocker 密钥的密钥加密密钥的 URL。 如果在 UseExistingKek 下拉列表中选择“nokek”，则此参数为可选参数。 如果在 UseExistingKek 下拉列表中选择“kek”，则必须输入 _keyEncryptionKeyURL_ 值。 |
| volumeType | 要对其执行加密操作的卷的类型。 有效值为“OS”、“Data”和“All”。 
| forceUpdateTag | 每次操作需要强制运行时，传入一个像 GUID 这样的唯一值。 |
| resizeOSDisk | 在拆分系统卷之前，是否应调整 OS 分区大小以占用整个 OS VHD。 |
| 位置 | 所有资源的位置。 |



## <a name="encrypt-virtual-machine-scale-sets"></a>加密虚拟机规模集
使用 [Azure 虚拟机规模集](../virtual-machine-scale-sets/overview.md)可以创建并管理一组完全相同的、负载均衡的 VM。 可以根据需求或定义的计划自动增减 VM 实例的数目。 使用 CLI 或 Azure PowerShell 加密虚拟机规模集。

[此处](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss)提供了一个用于 Linux 规模集数据磁盘加密的批处理文件示例。 此示例创建一个资源组（Linux 规模集），装载一个 5 GB 的数据磁盘，并对虚拟机规模集进行加密。

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>使用 Azure CLI 注册磁盘加密预览版

要对虚拟机规模集预览版使用 Azure 磁盘加密功能，需要使用 [az feature register](/cli/azure/feature#az-feature-register) 自行注册订阅。 只需在首次使用磁盘加密预览版功能时执行以下步骤：

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

传播注册请求最多可能需要 10 分钟。 可以通过 [az feature show](/cli/azure/feature#az-feature-show) 查看注册状态。 `State` 报告“已注册”时，请使用 [az provider register](/cli/azure/provider#az-provider-register) 重新注册 Mirosoft.Compute 提供程序：

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

###  <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>使用 Azure CLI 加密虚拟机规模集
使用 [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) 在 Windows 虚拟机规模集上启用加密。 如果在规模集上将升级策略设置为手动，则使用 [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances) 启动加密。 先决条件是事先创建资源组、VM 和密钥保管库。 

-  **加密正在运行的虚拟机规模集**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **使用 KEK 加密正在运行的虚拟机规模集以包装密钥**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
- **获取虚拟机规模集的加密状态：** 使用 [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MySecureRG" --name "MySecureVmss"
    ```

- **在虚拟机规模集上禁用加密**：使用 [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-powershell"></a>使用 Azure Powershell 注册磁盘加密预览版

要对虚拟机规模集预览版使用 Azure 磁盘加密功能，需要使用 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) 自注册订阅。 只需在首次使用磁盘加密预览版功能时执行以下步骤：

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

传播注册请求最多可能需要 10 分钟。 可以使用 [Get AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 检查注册状态。 `RegistrationState` 报告“已注册”时，请使用 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 重新注册 Mirosoft.Compute 提供程序：

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>使用 Azure PowerShell 加密虚拟机规模集

使用 [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension) cmdlet 在 Windows 虚拟机规模集上启用加密。 先决条件是事先创建资源组、VM 和密钥保管库。

-  **加密正在运行的虚拟机规模集**：
    ```powershell
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:
    ```powershell
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

- **获取虚拟机规模集的加密状态：** 使用 [Get-AzureRmVmssVMDiskEncryption](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption) cmdlet。
    
    ```powershell
    get-AzureRmVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **在虚拟机规模集上禁用加密**：使用 [Disable-AzureRmVmssDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption) cmdlet。 

    ```powershell
    Disable-AzureRmVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-linux-virtual-machine-scale-sets"></a>适用于 Linux 虚拟机规模集的 Azure 资源管理器模板

若要加密或解密 Linux 虚拟机规模集，请使用以下 Azure 资源管理器模板和说明：

- [在 Linux 虚拟机规模集上启用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)
- [使用 jumpbox 部署 Linux VM 的 VM 规模集，并在 Linux VM 规模集上启用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)
- [在 Linux VM 规模集上禁用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

     1. 单击 **“部署到 Azure”**。
     2. 填写必填字段，然后同意条款和条件。
     3. 单击“购买”以部署模板。

## <a name="bkmk_EFA"></a>对 Linux IaaS VM 上的数据磁盘使用 EncryptFormatAll 功能
**EncryptFormatAll** 参数可以减少加密 Linux 数据磁盘所需的时间。 满足特定条件的分区将格式化（使用其当前文件系统）。 然后，将它们重新装回到执行命令之前所在的位置。 如果想要排除某个符合条件的数据磁盘，可以在运行命令之前卸载该磁盘。

 运行此命令之后，以前装载的所有驱动器将格式化。 然后，加密层将在现已为空的驱动器的顶层启动。 选择此选项后，附加到 VM 的临时资源磁盘也会加密。 如果重置临时驱动器，该驱动器将重新格式化，并且 Azure 磁盘加密解决方案下次有机会为 VM 重新加密该驱动器。

>[!WARNING]
> 如果 VM 的数据卷上存在所需的数据，则不应使用 EncryptFormatAll。 卸载磁盘可将其从加密项中排除。 首先应该在测试 VM 上试用 EncryptFormatAll，以了解功能参数及其影响，然后再尝试在生产 VM 上使用该参数。 EncryptFormatAll 选项会格式化数据磁盘，因此磁盘上的所有数据都会丢失。 在继续之前，请验证是否已正确卸载想要排除的磁盘。 </br></br>
 >如果在更新加密设置时设置此参数，可能会导致在实际加密之前重新启动。 在这种情况下，还需要从 fstab 文件中删除不想要格式化的磁盘。 同样，在启动加密操作之前，应将想要加密并格式化的分区添加到 fstab 文件。 

### <a name="bkmk_EFACriteria"></a>EncryptFormatAll 的条件
该参数会遍历并加密满足以下**所有**条件的所有分区： 
- 不是根/OS/启动分区
- 尚未加密
- 不是 BEK 卷
- 不是 RAID 卷
- 不是 LVM 卷
- 已装载

加密组成 RAID 或 LVM 卷而不是 RAID 或 LVM 卷的磁盘。

### <a name="bkmk_EFAPSH"> </a> 通过 Azure CLI 使用 EncryptFormatAll 参数
使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令在 Azure 中运行的 IaaS 虚拟机上启用加密。

-  **使用 EncryptFormatAll 加密正在运行的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"></a>结合 PowerShell cmdlet 使用 EncryptFormatAll 参数
结合 [EncryptFormatAll 参数](https://www.powershellgallery.com/packages/AzureRM/5.0.0)使用 [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) cmdlet。 

**使用 EncryptFormatAll 加密正在运行的 VM：** 例如，以下脚本会初始化变量，并结合 EncryptFormatAll 参数运行 Set-AzureRmVMDiskEncryptionExtension cmdlet。 先决条件是事先创建资源组、VM 和密钥保管库。 将 MySecureRg、MySecureVM 和 MySecureVault 替换为你的值。
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"></a>结合逻辑卷管理器 (LVM) 使用 EncryptFormatAll 参数 
我们建议采用 LVM-on-crypt 设置。 对于下面的所有示例，请将设备路径和装载点替换为适合用例的任何值。 可按如下所述完成此设置：

- 添加构成 VM 的数据磁盘。
- 格式化、装载这些磁盘并将其添加到 fstab 文件。

    1. 格式化新添加的磁盘。 此处使用了 Azure 生成的符号链接。 使用符号链接可避免设备名更改所造成的问题。 有关详细信息，请参阅[排查设备名问题](../virtual-machines/linux/troubleshoot-device-names-problems.md)一文。
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. 装载磁盘。
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. 添加到 fstab。
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. 结合 -EncryptFormatAll 运行 Set-AzureRmVMDiskEncryptionExtension PowerShell cmdlet，以加密这些磁盘。
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. 在这些新磁盘的顶层设置 LVM。 请注意，VM 在完成启动后，加密的驱动器会解锁。 因此，后续的 LVM 装载必定会延迟。


## <a name="bkmk_VHDpre"></a>通过客户加密的 VHD 和加密密钥新建的 IaaS VM
在此方案中，可以使用 PowerShell cmdlet 或 CLI 命令启用加密。 

参考附录中的说明来准备可在 Azure 中使用的预加密映像。 创建映像后，可使用下一部分中的步骤创建加密的 Azure VM。

* [准备预加密的 Windows VHD](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [准备预加密的 Linux VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >启用 Azure 磁盘加密之前，必须在其外部创建基于托管磁盘的 VM 实例的快照和/或备份。 可以从门户创建托管磁盘的快照，也可以使用 [Azure 备份](../backup/backup-azure-vms-encryption.md)。 备份确保在加密过程中发生任何意外故障时可以使用恢复选项。 备份后，可以通过指定 -skipVmBackup 参数，使用 Set-AzureRmVMDiskEncryptionExtension cmdlet 来加密托管磁盘。 在未备份基于托管磁盘的 VM 且未指定此参数的情况下，对 VM 使用 Set-AzureRmVMDiskEncryptionExtension 命令会失败。 
>
>加密或禁用加密可能导致 VM 重新启动。 



### <a name="bkmk_VHDprePSH"></a>使用 Azure PowerShell 加密包含预加密 VHD 的 IaaS VM 
可以使用 PowerShell cmdlet [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples) 在加密的 VHD 上启用磁盘加密。 以下示例显示了一些常用参数。 

```azurepowershell-interactive
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新添加的数据磁盘上启用加密

可以使用 [az vm disk attach](../virtual-machines/linux/add-disk.md) 或[通过 Azure 门户](../virtual-machines/linux/attach-disk-portal.md)添加新数据磁盘。 在加密之前，需要先装载新附加的数据磁盘。 必须请求加密数据驱动器，因为加密正在进行时，该驱动器不可用。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>使用 Azure CLI 在新添加的磁盘上启用加密

 如果 VM 先前使用“All”进行加密，则 --volume-type 参数应保留为 All。 All 包括 OS 和数据磁盘。 如果 VM 先前使用卷类型“OS”进行加密，则应将 --volume-type 参数更改为 All，以便包含 OS 和新数据磁盘。 如果 VM 仅使用卷类型“Data”进行加密，则它可以保留为“Data”，如下所示。 添加新数据磁盘并将其附加到 VM 并不足以为加密做准备。 在启用加密之前，还必须格式化新附加的磁盘并将其正确装载在 VM 中。 在 Linux 上，磁盘必须使用[永久性块设备名称](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems)装载在 /etc/fstab 中。  

与 Powershell 语法相反，CLI 在启用加密时不要求用户提供唯一的序列版本。 CLI 自动生成并使用自己唯一的序列版本值。

-  **加密正在运行的 VM 的数据卷：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **使用 KEK 加密正在运行的 VM 的数据卷：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>使用 Azure PowerShell 在新添加的磁盘上启用加密
 使用 Powershell 加密适用于 Linux 的新磁盘时，需要指定新的序列版本。 序列版本必须唯一。 以下脚本生成序列版本的 GUID。 
 

-  **加密正在运行的 VM 的数据卷：** 以下脚本初始化变量并运行 Set-AzureRmVMDiskEncryptionExtension cmdlet。 先决条件是事先创建资源组、VM 和密钥保管库。 将 MySecureRg、MySecureVM 和 MySecureVault 替换为你的值。 -VolumeType 参数可接受的值为 All、OS 和 Data。 如果 VM 先前使用卷类型“OS”或“All”进行加密，则应将 -VolumeType 参数更改为 All，以便包含 OS 和新数据磁盘。

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **使用 KEK 加密正在运行的 VM 的数据卷：**-VolumeType 参数可接受的值为 All、OS 和 Data。 如果 VM 先前使用卷类型“OS”或“All”进行加密，则应将 -VolumeType 参数更改为 All，以便包含 OS 和新数据磁盘。

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data';

     ```

    >[!NOTE]
    > disk-encryption-keyvault 参数值的语法是完整的标识符字符串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 参数值的语法是 KEK 的完整 URI，其格式为： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>为 Linux VM 禁用加密
可以使用 Azure PowerShell、Azure CLI 或资源管理器模板禁用加密。 

>[!IMPORTANT]
>在 Linux VM 上，仅支持对数据卷禁用 Azure 磁盘加密。 如果 OS 卷已加密，则不支持对数据卷或 OS 卷禁用加密。  

- **使用 Azure PowerShell 禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet。 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **使用 Azure CLI 禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **使用资源管理器模板禁用加密：** 使用[在正在运行的 Linux VM 上禁用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad)模板禁用加密。
     1. 单击 **“部署到 Azure”**。
     2. 选择订阅、资源组、位置、VM、法律条款和协议。
     3.  单击“购买”，在正在运行的 Windows VM 上禁用磁盘加密。 


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [启用适用于 Windows 的 Azure 磁盘加密](azure-security-disk-encryption-windows.md)

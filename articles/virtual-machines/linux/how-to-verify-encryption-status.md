---
title: 验证 Linux 的加密状态 -Azure 磁盘加密
description: 本文提供了从平台和 OS 级别验证加密状态的说明。
author: kailashmsft
ms.service: security
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e39a230c71cf48422220768adfa8de91cbaa6692
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072836"
---
# <a name="verify-encryption-status-for-linux"></a>验证 Linux 的加密状态 

本文介绍了如何使用不同的方法来验证虚拟机的加密状态：Azure 门户、PowerShell、Azure CLI 或虚拟机 (VM) 的操作系统。 

可以通过以下任一方法在加密期间或之后验证加密状态：

- 检查附加到特定 VM 的磁盘。 
- 查询每个磁盘上的加密设置，无论是否已附加该磁盘。

此方案适用于 Azure 磁盘加密双重传递和单次传递扩展。 此方案下的唯一环境是 Linux 发行版。

>[!NOTE] 
>我们将在本文中使用变量。 请相应地替换值。

## <a name="portal"></a>门户

在 Azure 门户的“扩展”部分中，选择列表中的 Azure 磁盘加密扩展。 “状态消息”信息指示当前加密状态：

![门户检查（突出显示状态、版本和状态消息）](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

在扩展列表中，可以看到相应的 Azure 磁盘加密扩展版本。 版本 0.x 对应于 Azure 磁盘加密双重传递，版本 1.x 对应于 Azure 磁盘加密单次传递。

选择扩展，然后选择“查看详细状态”，可获取更多详细信息。 加密过程的详细状态以 JSON 格式显示。

![门户检查（突出显示“查看详细状态”链接）](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![JSON 格式的详细状态](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

验证加密状态的另一种方法是查看“磁盘设置”部分。

![OS 磁盘和数据磁盘的加密状态](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> 此状态表示磁盘已标记加密设置，而不是在 OS 级别已对其进行加密。
>
> 根据设计，先标记磁盘，再加密磁盘。 如果加密过程失败，则磁盘可能被标记，但未被加密。 
>
> 若要确认磁盘是否已真正加密，可以在 OS 级别仔细检查每个磁盘的加密状态。

## <a name="powershell"></a>PowerShell

可以使用以下 PowerShell 命令验证已加密 VM 的“常规”加密状态：

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 中的常规加密状态](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

可以使用以下 PowerShell 命令捕获每个磁盘的加密设置。

### <a name="single-pass"></a>单次传递
在单次传递中，将在每个磁盘（OS 和数据）上标记加密设置。 可以按如下方式在单次传递中捕获 OS 磁盘的加密设置：

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![OS 磁盘的加密设置](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

如果磁盘未标记加密设置，则输出将为空：

![输出为空](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

使用以下命令捕获数据磁盘的加密设置：

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![数据磁盘的加密设置](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>双重传递
在双重传递中，将在 VM 模型中标记加密设置，而不是在每个单独的磁盘上进行标记。

若要验证是否已在双重传递中标记了加密设置，请使用以下命令：

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![双重传递中的加密设置](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>未附加的磁盘

检查未附加到 VM 的磁盘的加密设置。

### <a name="managed-disks"></a>托管磁盘
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>Azure CLI

可以使用以下 Azure CLI 命令验证已加密 VM 的“常规”加密状态：

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Azure CLI 中的常规加密状态 ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>单次传递
可以使用以下 Azure CLI 命令验证每个磁盘的加密设置：

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![数据加密设置](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> 如果磁盘未标记加密设置，将显示文本“磁盘未加密”。

可使用以下命令获取详细状态和加密设置。

OS 磁盘：

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OS 磁盘的详细状态和加密设置](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

数据磁盘：

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![数据磁盘的详细状态和加密设置](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>双重传递

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![通过 Azure CLI 进行双重传递的常规加密设置](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

还可在 OS 磁盘的 VM 模型存储配置文件上检查加密设置：

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![通过 Azure CLI 进行双重传递的 VM 配置文件](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>未附加的磁盘

检查未附加到 VM 的磁盘的加密设置。

### <a name="managed-disks"></a>托管磁盘

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>非托管磁盘

非托管磁盘是指以页 blob 形式存储在 Azure 存储帐户中的 VHD 文件。

若要获取特定磁盘的详细信息，需要提供：

- 内含磁盘的存储帐户的 ID。
- 该特定存储帐户的连接字符串。
- 存储该磁盘的容器的名称。
- 磁盘名称。

此命令可列出所有存储帐户的所有 ID：

```bash
az storage account list --query [].[id] -o tsv
```
存储帐户 ID 采用以下格式列出：

/subscriptions/\<subscription id>/resourceGroups/\<resource group name>/providers/Microsoft.Storage/storageAccounts/\<storage account name>

选择相应的 ID，并将其存储在变量上：
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

此命令可获取某个特定存储帐户的连接字符串，并将其存储在变量上：

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

以下命令可列出存储帐户下的所有容器：
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
用于磁盘的容器通常命名为“vhds”。

将容器名称存储在变量上： 
```bash
ContainerName="name of the container"
```

此命令可列出特定容器上的所有 blob：
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
选择要查询的磁盘，并将其名称存储在变量上：
```bash
DiskName="diskname.vhd"
```
查询磁盘加密设置：
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>操作系统
验证数据磁盘分区是否已加密（而 OS 磁盘未加密）。

如果分区或磁盘已加密，它将显示为 crypt 类型。 如果未加密，它将显示为 part/disk 类型。

``` bash
lsblk
```

![分区的 OS crypt 层](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

可以使用以下 lsblk 变体获取更多详细信息。 

你将看到一个由扩展装载的 crypt 类型层。 以下示例显示了类型为 crypto\_LUKS FSTYPE 的逻辑卷和普通磁盘。

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![逻辑卷和普通磁盘的 OS crypt 层](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

作为额外步骤，可以验证数据磁盘是否已加载任何密钥：

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

可以检查哪些 dm 设备作为 crypt 列出：

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密疑难解答](disk-encryption-troubleshooting.md)

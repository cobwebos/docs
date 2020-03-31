---
title: 如何验证 Linux 的加密状态
description: 本文提供有关从平台和操作系统级别验证加密状态的说明。
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123421"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>如何验证 Linux 的加密状态 

**此方案适用于 ADE 双通和单通道扩展。**  
此文档范围是使用不同的方法验证虚拟机的加密状态。

### <a name="environment"></a>环境

- Linux 发行版

### <a name="procedure"></a>过程

虚拟机已使用双通或单路加密。

加密状态可以在加密期间或加密后使用不同的方法进行验证。

>[!NOTE] 
>我们在整个文档中使用变量，相应地替换值。

### <a name="verification"></a>验证

验证可以从门户、PowerShell、AZ CLI 和 VM 操作系统端进行。 

此验证可以通过检查连接到特定 VM 的磁盘来完成。 

或者通过查询每个磁盘上的加密设置，无论磁盘是附加的还是未连接的。

在以下不同的验证方法：

## <a name="using-the-portal"></a>使用门户

通过检查 Azure 门户上的扩展部分来验证加密状态。

在 **"扩展"** 部分中，您将看到列出的 ADE 扩展。 

单击它并查看**状态消息**，它将指示当前加密状态：

![门户检查编号 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

在扩展列表中，您将看到相应的 ADE 扩展版本。 版本 0.x 对应于 ADE 双通道，版本 1.x 对应于 ADE 单通道。

您可以获取更多详细信息，单击扩展，然后在*查看详细状态*。

您将看到以 json 格式的加密过程的更详细的状态：

![门户检查编号 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![门户检查编号 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

验证加密状态的另一种方法是查看 **"磁盘"** 部分。

![门户检查编号 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> 此状态表示磁盘已加盖加密设置，但不希望它们实际在操作系统级别加密。 根据设计，磁盘首先被盖章，然后加密。 如果加密过程失败，磁盘可能最终盖章，但未加密。 要确认磁盘是否真正加密，可以在操作系统级别仔细检查每个磁盘的加密。

## <a name="using-powershell"></a>使用 PowerShell

您可以使用以下 PowerShell 命令验证加密 VM 的**一般**加密状态：

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![检查电源外壳 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

您可以使用以下 PowerShell 命令从每个磁盘捕获加密设置：

### <a name="single-pass"></a>单通道
如果单次，加密设置在每个磁盘（OS 和数据）上盖章，则可以以单次方式捕获 OS 磁盘加密设置，如下所示：

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
![验证操作系统单通道 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

如果磁盘没有加印加密设置，则输出将为空，如下所示：

![操作系统加密设置 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

捕获数据磁盘加密设置：

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
![验证数据单 ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>双通道
在双通道中，加密设置在 VM 模型中标记，而不是在每个单独的磁盘上。

要验证加密设置在双通道中盖章，可以使用以下命令：

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
![验证双通电源外壳 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>未连接磁盘

检查未连接到 VM 的磁盘的加密设置。

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
## <a name="using-az-cli"></a>使用 AZ CLI

您可以使用以下 AZ CLI 命令验证加密 VM 的**一般**加密状态：

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![使用 CLI 验证常规 ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>单次通行证
您可以使用以下 AZ CLI 命令验证每个磁盘的加密设置：

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![数据加密设置](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> 如果磁盘没有盖章的加密设置，则将显示为"磁盘未加密"

详细的状态和加密设置：

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

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

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

![数据单 CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>双通

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![使用 CLI](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)验证常规双，您还可以检查 OS 磁盘的 VM 模型存储配置文件上的加密设置：

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

![使用 CLI 验证 vm 配置文件双 ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>未连接磁盘

检查未连接到 VM 的磁盘的加密设置。

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

要获取特定磁盘的详细信息，您需要提供：

包含磁盘的存储帐户的 ID。
该特定存储帐户的连接字符串。
存储磁盘的容器的名称。
磁盘名称。

此命令列出所有存储帐户的所有 ID：

```bash
az storage account list --query [].[id] -o tsv
```
存储帐户 ID 以以下形式列出：

/订阅/\<订阅 id>/资源\<组/资源组名称>/提供商/Microsoft.存储/存储帐户\</存储帐户名称>

选择适当的 ID 并将其存储在变量上：
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
连接字符串。

此命令获取特定存储帐户的连接字符串并将其存储在变量上：

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

容器名称。

以下命令列出了存储帐户下的所有容器：
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
用于磁盘的容器通常称为"vhds"

将容器名称存储在变量上 
```bash
ContainerName="name of the container"
```

磁盘名称。

使用此命令列出特定容器上的所有 Blob
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
选择要查询的磁盘并将其名称存储在变量上。
```bash
DiskName="diskname.vhd"
```
查询磁盘加密设置
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>从操作系统
验证数据磁盘分区是否加密（并且操作系统磁盘未加密）

当分区/磁盘加密时，它显示为**密码类型**，当它未加密时，它显示为**部件/磁盘**类型

``` bash
lsblk
```

![Os 密码层 ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

您可以使用以下"lsblk"变体获取更多详细信息。 

您将看到由扩展安装的**隐场**类型图层。

下面的示例显示具有"**加密\_LUKS FSTYPE"** 的逻辑卷和普通磁盘。

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Os 密码层 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

作为额外的步骤，您还可以验证数据磁盘是否加载了任何密钥

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

哪些 dm 设备被列为密码

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密疑难解答](disk-encryption-troubleshooting.md)

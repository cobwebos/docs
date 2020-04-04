---
title: 在加密设备上配置 LVM 和 RAID - Azure 磁盘加密
description: 本文提供了在 Linux VM 的加密设备上配置 LVM 和 RAID 的说明。
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657446"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>在加密设备上配置 LVM 和 RAID

本文是有关如何在加密设备上执行逻辑卷管理 （LVM） 和 RAID 的分步过程。 该过程适用于以下环境：

- Linux 发行版
    - RHEL 7.6°
    - 乌本图 18.04°
    - SUSE 12+
- Azure 磁盘加密单通道扩展
- Azure 磁盘加密双通道扩展


## <a name="scenarios"></a>方案

本文中的过程支持以下方案：  

- 在加密设备之上配置 LVM（密码上的 LVM）
- 在加密设备（密码上的 RAID）上配置 RAID

对基础设备或设备进行加密后，您可以在该加密层之上创建 LVM 或 RAID 结构。 

物理卷 （PV） 在加密层的顶部创建。 物理卷用于创建卷组。 创建卷并在 /etc/fstab 上添加所需的条目。 

![LVM 结构层图](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

类似地，RAID 设备是在磁盘上的加密层之上创建的。 在 RAID 设备顶部创建文件系统，并作为常规设备添加到 /etc/fstab 中。

## <a name="considerations"></a>注意事项

我们建议您在密码上使用 LVM。 当由于特定的应用程序或环境限制而无法使用 LVM 时，RAID 是一个选项。

您将使用 **"加密格式全部"** 选项。 有关此选项的详细信息，请参阅[在 Linux VM 上使用加密格式所有功能进行数据磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms)。

尽管您也可以在加密操作系统时使用此方法，但我们只是在此处加密数据驱动器。

过程假定您已经审阅了 Linux VM[上的 Azure 磁盘加密方案中](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux)的先决条件，并在["快速入门"中：使用 Azure CLI 创建和加密 Linux VM。](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart)

Azure 磁盘加密双通道版本位于弃用路径上，不应再在新加密上使用。

## <a name="general-steps"></a>常规步骤

使用"在加密"配置时，请使用以下过程中概述的过程。

>[!NOTE] 
>我们在整个文章中使用变量。 相应地替换值。

### <a name="deploy-a-vm"></a>部署 VM 
以下命令是可选的，但我们建议您在新部署的虚拟机 （VM） 上应用这些命令。

PowerShell：

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI：

```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>将磁盘连接到 VM
对`$N`要附加到 VM 的新磁盘数重复以下命令。

PowerShell：

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI：

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>验证磁盘是否连接到 VM
PowerShell：
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![PowerShell 中连接的磁盘列表](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI：

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Azure CLI 中连接的磁盘列表](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

门户：

![门户中附加磁盘的列表](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

OS：

```bash
lsblk 
```
![操作系统中连接的磁盘列表](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>将磁盘配置为加密
此配置在操作系统级别完成。 通过 Azure 磁盘加密为传统加密配置了相应的磁盘：

- 文件系统在磁盘顶部创建。
- 创建临时装载点以装载文件系统。
- 文件系统配置为在 /etc/fstab 上，可在启动时装载。

检查分配给新磁盘的设备信函。 在此示例中，我们使用四个数据磁盘。

```bash
lsblk 
```
![连接到操作系统的数据磁盘](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>在每个磁盘之上创建文件系统
此命令循环在"for"周期的"in"部分定义的每个磁盘上创建 ext4 文件系统。

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![创建 ext4 文件系统](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

查找您最近创建的文件系统的通用唯一标识符 （UUID），创建临时文件夹，在 /etc/fstab 上添加相应的条目，并装载所有文件系统。

此命令还会在"for"周期的"in"部分定义的每个磁盘上循环：

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>验证磁盘是否正确安装
```bash
lsblk
```
![装载的临时文件系统列表](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

还要验证磁盘是否配置：

```bash
cat /etc/fstab
```
![通过 fstab 配置信息](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>加密数据磁盘
使用密钥加密密钥 （KEK） 的 PowerShell：

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

使用 KEK 的 Azure CLI：

```bash
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>验证加密状态

仅当所有磁盘都加密时，才继续执行下一步。

PowerShell：

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 中的加密状态](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI：

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Azure CLI 中的加密状态](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

门户：

![门户中的加密状态](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

操作系统级别：

```bash
lsblk
```
![操作系统中的加密状态](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

扩展名将文件系统添加到 /var/lib/azure_disk_encryption_config/azure_crypt_mount（旧加密）或 /etc/crypttab（新加密）。

>[!NOTE] 
>不要修改这些文件中的任何一个。

此文件将在引导过程中处理激活这些磁盘，以便 LVM 或 RAID 以后可以使用它们。 

不要担心此文件的装载点。 在我们在这些加密设备上创建物理卷或 RAID 设备后，Azure 磁盘加密将失去将磁盘装载为正常文件系统的能力。 （这将删除我们在准备过程中使用的文件系统格式。

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>删除临时文件夹和临时 fstab 条目
在将用作 LVM 一部分的磁盘上卸载文件系统。

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
并删除 /etc/fstab 条目：

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>验证磁盘是否未安装，以及 /etc/fstab 上的条目是否已被删除

```bash
lsblk
```
![验证临时文件系统未安装](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

并验证磁盘是否配置：
```bash
cat /etc/fstab
```
![验证已删除临时 fstab 条目](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>LVM 在加密点上的步骤
现在基础磁盘已加密，您可以创建 LVM 结构。

使用 /dev/映射器路径为每个磁盘创建物理卷（在磁盘顶部的地窖层上，而不是在磁盘本身上），而不是使用设备名称。

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>在加密层顶部配置 LVM
#### <a name="create-the-physical-volumes"></a>创建物理卷
您将看到一条警告，询问是否可以清除文件系统签名。 继续输入**y**，或使用**回声"y"，** 如图所示：

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![验证已创建物理卷](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>此处的 /dev/mapper/设备名称需要基于**lsblk**的输出进行实际值替换。

#### <a name="verify-the-information-for-physical-volumes"></a>验证物理卷的信息
```bash
pvs
```

![物理卷信息](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>创建卷组
使用已初始化的相同设备创建卷组：

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>检查卷组的信息

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![卷组的信息](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>创建逻辑卷

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>检查创建的逻辑卷

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![逻辑卷的信息](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>在逻辑卷的结构之上创建文件系统

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>为新文件系统创建装载点

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>将新文件系统添加到 /etc/fstab 并装载它们

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>验证是否装载了新的文件系统

```bash
lsblk -fs
df -h
```
![装载文件系统的信息](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

在**lsblk**的此变体中，我们将按相反顺序列出显示依赖项的设备。 此选项有助于标识按逻辑卷而不是原始 /dev/sd_disk） 设备名称分组的设备。

请务必确保**将 nofail**选项添加到通过 Azure 磁盘加密加密的设备之上创建的 LVM 卷的装载点选项。 它可以防止操作系统在启动过程中（或处于维护模式）期间卡住。

如果不使用 **"无故障**"选项：

- 操作系统永远不会进入启动 Azure 磁盘加密和数据磁盘解锁和装载阶段。 
- 加密磁盘将在启动过程结束时解锁。 LVM 卷和文件系统将自动装载，直到 Azure 磁盘加密解锁它们。 

您可以测试重新启动 VM，并验证文件系统在启动后是否也自动装载。 此过程可能需要几分钟时间，具体取决于文件系统的数量和大小。

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>重新启动 VM 并在重新启动后进行验证

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>在加密点上 RAID 的步骤
现在基础磁盘已加密，您可以继续创建 RAID 结构。 该过程与 LVM 的过程相同，但使用 /dev/映射器路径而不是使用设备名称。

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>在磁盘的加密层顶部配置 RAID
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![通过 mdadm 命令配置 RAID 的信息](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>此处的 /dev/mapper/设备名称需要替换为实际值，具体取决于**lsblk**的输出。

### <a name="checkmonitor-raid-creation"></a>检查/监视 RAID 创建
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![RAID 的状态](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>在新 RAID 设备顶部创建文件系统
```bash
mkfs.ext4 /dev/md10
```

为文件系统创建新的装载点，将新文件系统添加到 /etc/fstab，然后装载它：

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

验证是否装载了新的文件系统：

```bash
lsblk -fs
df -h
```
![装载文件系统的信息](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

请务必确保**将 nofail**选项添加到通过 Azure 磁盘加密加密的设备之上创建的 RAID 卷的装载点选项。 它可以防止操作系统在启动过程中（或处于维护模式）期间卡住。

如果不使用 **"无故障**"选项：

- 操作系统永远不会进入启动 Azure 磁盘加密和数据磁盘解锁和装载阶段。
- 加密磁盘将在启动过程结束时解锁。 RAID 卷和文件系统将自动装载，直到 Azure 磁盘加密解锁它们。

您可以测试重新启动 VM，并验证文件系统在启动后是否也自动装载。 此过程可能需要几分钟时间，具体取决于文件系统的数量和大小。

```bash
shutdown -r now
```

当您可以登录时：

```bash
lsblk
df -h
```
## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密疑难解答](disk-encryption-troubleshooting.md)


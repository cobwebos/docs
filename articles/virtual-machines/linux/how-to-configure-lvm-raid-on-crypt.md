---
title: 在加密设备上配置 LVM 和 RAID-Azure 磁盘加密
description: 本文提供了有关在适用于 Linux Vm 的加密设备上配置 LVM 和 RAID 的说明。
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 3be8863d86fd1525e6f64b46ddf5ca3c702ecdea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029223"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>在加密设备上配置 LVM 和 RAID

本文分步介绍了如何在加密设备上执行逻辑卷管理（LVM）和 RAID。 此过程适用于以下环境：

- Linux 发行版
    - RHEL 7.6 +
    - Ubuntu 18.04 +
    - SUSE 12 +
- Azure 磁盘加密单一传递扩展
- Azure 磁盘加密双重传递扩展


## <a name="scenarios"></a>方案

本文中的过程支持以下方案：  

- 在加密设备上配置 LVM （dm-crypt）
- 在加密设备（dm-crypt）的基础上配置 RAID

对基础设备或设备进行加密后，可以在该加密层之上创建 LVM 或 RAID 结构。 

在加密层的基础上创建物理卷（PVs）。 物理卷用于创建卷组。 创建卷并在/etc/fstab 上添加所需的项 

![LVM 结构层关系图](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

同样，在磁盘上的加密层之上创建 RAID 设备。 在 RAID 设备上创建文件系统，并将其作为常规设备添加到/etc/fstab。

## <a name="considerations"></a>注意事项

建议你使用 dm-crypt。 如果由于特定的应用程序或环境限制而无法使用 LVM，则可以选择使用 RAID。

你将使用**EncryptFormatAll**选项。 有关此选项的详细信息，请参阅[将 EncryptFormatAll 功能用于 Linux vm 上的数据磁盘](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)。

虽然你也可以在对操作系统进行加密时使用此方法，但我们只是在此处对数据驱动器进行加密。

这些过程假定你已在 Linux Vm 和快速入门中查看[Azure 磁盘加密方案](./disk-encryption-linux.md)的先决条件[：使用 Azure CLI 创建并加密 Linux VM](./disk-encryption-cli-quickstart.md)。

Azure 磁盘加密双通版本在弃用路径上，不应再在新的加密上使用。

## <a name="general-steps"></a>常规步骤

使用 "dm-crypt" 配置时，请使用以下过程中所述的过程。

>[!NOTE] 
>我们将在本文中使用变量。 请相应地替换值。

### <a name="deploy-a-vm"></a>部署 VM 
以下命令是可选的，但我们建议你将其应用于新部署的虚拟机（VM）。

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
### <a name="attach-disks-to-the-vm"></a>将磁盘附加到 VM
对于 `$N` 要附加到 VM 的新磁盘数，重复以下命令。

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

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>验证磁盘是否已附加到 VM
PowerShell：
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![PowerShell 中附加的磁盘列表](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI：

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Azure CLI 中的附加磁盘列表](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

门户：

![门户中附加的磁盘列表](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

OS：

```bash
lsblk 
```
![操作系统中附加的磁盘列表](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>配置要加密的磁盘
此配置是在操作系统级别上完成的。 将相应的磁盘配置为通过 Azure 磁盘加密进行传统加密：

- 文件系统是在磁盘上创建的。
- 创建临时装入点用于装载文件系统。
- 文件系统在/etc/fstab 上配置为在启动时装载。

检查分配给新磁盘的设备号。 在此示例中，我们使用四个数据磁盘。

```bash
lsblk 
```
![附加到操作系统的数据磁盘](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>在每个磁盘上创建文件系统
此命令在 "for" 循环的 "in" 部分中定义的每个磁盘上循环访问 ext4 文件系统。

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![创建 ext4 文件系统](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

找到最近创建的文件系统的全局唯一标识符（UUID），创建一个临时文件夹，在/etc/fstab 上添加相应的条目，并装载所有文件系统。

此命令还会循环访问 "for" 循环的 "在" 部分中定义的每个磁盘：

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>验证磁盘是否已正确装入
```bash
lsblk
```
![已装入的临时文件系统列表](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

同时验证磁盘是否已配置：

```bash
cat /etc/fstab
```
![通过 fstab 配置信息](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>加密数据磁盘
使用密钥加密密钥（KEK）的 PowerShell：

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

使用 KEK Azure CLI：

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

仅当所有磁盘都已加密时，才继续执行下一步。

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
![OS 中的加密状态](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

扩展会将文件系统添加到/var/lib/azure_disk_encryption_config/azure_crypt_mount （旧加密）或/etc/crypttab （new 加密）。

>[!NOTE] 
>不要修改这些文件中的任何一个。

此文件将在启动过程中负责激活这些磁盘，以便 LVM 或 RAID 可以在以后使用它们。 

不要担心此文件上的装入点。 创建物理卷或基于这些加密设备的 RAID 设备后，Azure 磁盘加密将失去将装载为正常文件系统的磁盘的功能。 （这将删除准备过程中使用的文件系统格式。）

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>删除临时文件夹和临时 fstab 条目
卸载磁盘上的文件系统，这些磁盘将作为 LVM 的一部分使用。

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
并删除/etc/fstab 条目：

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>验证磁盘是否未装入并且/etc/fstab 上的条目已被删除

```bash
lsblk
```
![验证临时文件系统已卸载](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

并验证磁盘是否已配置：
```bash
cat /etc/fstab
```
![验证是否已删除临时 fstab 条目](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>LVM 的步骤-dm-crypt
现在，基础磁盘已加密，可以创建 LVM 结构。

不要使用设备名称，而是使用每个磁盘的/dev/mapper 路径来创建物理卷（在磁盘顶部的 dm-crypt 层上，而不是磁盘本身上）。

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>在加密层之上配置 LVM
#### <a name="create-the-physical-volumes"></a>创建物理卷
您将收到一条警告，询问您是否可以擦除文件系统签名。 继续输入**y**，或使用**echo "y"** ，如下所示：

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![验证是否已创建物理卷](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>此处的/dev/mapper/device 名称需要替换为基于**lsblk**输出的实际值。

#### <a name="verify-the-information-for-physical-volumes"></a>验证物理卷的信息
```bash
pvs
```

![物理卷的信息](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

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

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>为新文件系统创建装入点

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>将新文件系统添加到/etc/fstab 并装入它们

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>验证是否已装载新文件系统

```bash
lsblk -fs
df -h
```
![已装载的文件系统的信息](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

在这种**lsblk**的情况下，我们将按相反的顺序列出显示依赖关系的设备。 此选项有助于识别按逻辑卷（而不是原始/dev/sd [磁盘] 设备名称）分组的设备。

请务必确保已将**nofail**选项添加到在通过 Azure 磁盘加密加密的设备上创建的 LVM 卷的装入点选项中。 它可防止操作系统在启动过程中停滞（或处于维护模式）。

如果不使用**nofail**选项：

- 操作系统将永远不会进入 Azure 磁盘加密启动的阶段，并且数据磁盘将解锁并装入。 
- 已加密的磁盘将在启动过程结束时解除锁定。 在 Azure 磁盘加密解锁之前，LVM 卷和文件系统将自动装入。 

可以测试重启 VM，并验证在启动后，是否也会自动装载文件系统。 此过程可能需要几分钟时间，具体取决于文件系统的数量和大小。

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>重新启动 VM 并在重启后验证

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Dm-crypt 的步骤
现在，基础磁盘已加密，接下来可以继续创建 RAID 结构。 此过程与 LVM 的过程相同，但使用的是每个磁盘的/dev/mapper 路径，而不是使用设备名称。

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>在磁盘加密层之上配置 RAID
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![通过 mdadm 命令配置的 RAID 的信息](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>此处的/dev/mapper/device 名称需要替换为实际值，具体取决于**lsblk**的输出。

### <a name="checkmonitor-raid-creation"></a>检查/监视 RAID 创建
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![RAID 状态](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>在新 RAID 设备上创建文件系统
```bash
mkfs.ext4 /dev/md10
```

为文件系统创建新的装入点，将新文件系统添加到/etc/fstab，并装载它：

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

验证是否已装载新文件系统：

```bash
lsblk -fs
df -h
```
![已装载的文件系统的信息](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

请务必确保已将**nofail**选项添加到在通过 Azure 磁盘加密进行加密的设备上创建的 RAID 卷的装入点选项中。 它可防止操作系统在启动过程中停滞（或处于维护模式）。

如果不使用**nofail**选项：

- 操作系统将永远不会进入 Azure 磁盘加密启动的阶段，并且数据磁盘将解锁并装入。
- 已加密的磁盘将在启动过程结束时解除锁定。 在 Azure 磁盘加密解锁它们之前，会自动装入 RAID 卷和文件系统。

可以测试重启 VM，并验证在启动后，是否也会自动装载文件系统。 此过程可能需要几分钟时间，具体取决于文件系统的数量和大小。

```bash
shutdown -r now
```

登录时，还可以执行以下操作：

```bash
lsblk
df -h
```
## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密疑难解答](disk-encryption-troubleshooting.md)

---
title: 如何在 Linux VM 上配置 LVM 和 RAID 上密码
description: 本文提供有关在 Linux VM 上的密码配置 LVM 和 RAID 的说明。
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284902"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>如何配置 LVM 和 RAID 密码

本文档是关于如何在密码和密码配置上执行 LVM 的分步过程。

### <a name="environment"></a>环境

- Linux 发行版
    - RHEL 7.6°
    - 乌本图 18.04°
    - SUSE 12+
- ADE 单通道
- ADE 双通


## <a name="scenarios"></a>方案

**此方案适用于 ADE 双通和单通道扩展。**  

- 在加密设备之上配置 LVM（加密时 LVM）
- 在加密设备之上配置 RAID（加密时 RAID）

对基础设备进行加密后，您可以在该加密层之上创建 LVM 或 RAID 结构。 物理卷 （PV） 在加密图层的顶部创建。
物理卷用于创建卷组。
创建卷并在 /etc/fstab 上添加所需的条目。 

![检查连接的磁盘 PowerShell](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

类似地，RAID 设备是在磁盘上的加密层之上创建的。 文件系统在 RAID 设备顶部创建，并作为常规设备添加到 /etc/fstab 中。

### <a name="considerations"></a>注意事项

建议使用的方法为"密码上的 LVM"。

当由于特定的应用程序/环境限制而无法使用 LVM 时，将考虑 RAID。

您将使用"加密格式所有"选项，有关此功能的信息位于此处： https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms。

虽然此方法可以在同时加密操作系统时完成，但我们只是加密数据驱动器。

此过程假定您已经审阅了此处提及的先决条件：https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux和此处。 https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart

ADE 双通道版本位于弃用路径上，不应再在新的 ADE 加密中使用。

### <a name="procedure"></a>过程

使用"在密码"配置时，您将遵循以下过程：

>[!NOTE] 
>我们在整个文档中使用变量，相应地替换值。
## <a name="general-steps"></a>常规步骤
### <a name="deploy-a-vm"></a>部署 VM 
>[!NOTE] 
>虽然这是可选的，但我们建议您在新部署的 VM 上应用此选项。

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
CLI：
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
### <a name="attach-disks-to-the-vm"></a>将磁盘附加到 vm：
对要附加到 VM PowerShell 的新磁盘$N数重复上述步骤
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
CLI：
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>验证磁盘是否连接到 VM：
PowerShell：
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![检查连接的电源壳](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)CLI 磁盘：
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![检查连接的 CLI](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)门户![磁盘：检查连接的](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)CLI 操作系统的磁盘：
```bash
lsblk 
```
![检查磁盘连接的门户](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>将磁盘配置为加密
此配置完成后，操作系统级别，相应的磁盘配置为传统的 ADE 加密：

文件系统在磁盘顶部创建。

将创建临时装载点以装载文件系统。

文件系统配置在 /etc/fstab 上，可在启动时装载。

检查分配给新磁盘的设备信函，在此示例中，我们使用四个数据磁盘

```bash
lsblk 
```
![检查连接的磁盘](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>在每个磁盘之上创建一个文件系统。
此命令在"for"周期的"in"部分定义的每个磁盘上循环 ext4 文件系统创建。
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![检查附加的磁盘](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)os 查找最近创建的文件系统的 UUID，创建一个临时文件夹来装载它，在 /etc/fstab 上添加相应的条目并装载所有文件系统。

此命令还会在"for"周期的"in"部分定义的每个磁盘上循环：
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>验证磁盘是否正确安装：
```bash
lsblk
```
![检查安装](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)并配置的临时文件系统：
```bash
cat /etc/fstab
```
![检查 fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>加密数据磁盘：
使用 KEK 的电源外壳：
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
使用 KEK 的 CLI：
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
![检查加密](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)ps CLI：
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![检查加密 CLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) ![门户：检查](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)加密操作系统级别：
```bash
lsblk
```
![检查加密 CLI](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

扩展程序将文件系统添加到"/var/lib/azure_disk_encryption_config/azure_crypt_mount"（旧加密）或添加到"/等/加密表"（新加密）。

不要修改这些文件中的任何一个。

此文件将在引导过程中处理激活这些磁盘，以便以后 LVM 或 RAID 可以使用这些磁盘。 

不要担心此文件的装载点，因为 ADE 将失去将磁盘装载为正常文件系统的能力，因为我们确实在这些加密设备之上创建了物理卷或 raid 设备（这将摆脱我们在准备过程）。
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>删除临时文件夹和临时 fstab 条目
在将用作 LVM 的一部分的磁盘上卸载文件系统
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
并删除 /etc/fstab 条目：
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>验证磁盘是否未安装，以及 /etc/fstab 上的条目是否已被删除
```bash
lsblk
```
![检查未安装](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)并配置的临时文件系统：
```bash
cat /etc/fstab
```
![检查临时 fstab 条目被删除](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>对于 LVM 在密码
现在基础磁盘已加密，您可以继续创建 LVM 结构。

使用 /dev/映射器路径为每个磁盘创建物理卷（在磁盘顶部的密码层上而不是在磁盘本身上），而不是使用设备名称。
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>在加密层顶部配置 LVM
#### <a name="create-the-physical-volumes"></a>创建物理卷
您会收到一条警告，询问是否可以清除文件系统签名。 

您可以继续输入"y"或使用回声"y"，如下所示：
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![pvcreate](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>此处的 /dev/映射器/设备名称需要基于 lsblk 的输出为实际值进行替换。
#### <a name="verify-the-physical-volumes-information"></a>验证物理卷信息
```bash
pvs
```
![检查物理卷 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>创建卷组
使用已初始化的相同设备创建 VG
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>检查卷组信息
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![检查物理卷 2](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>创建逻辑卷
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>检查创建的逻辑卷
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![检查 lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>在逻辑卷结构之上创建文件系统
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>为新文件系统创建装载点
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
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>验证是否装载了新文件系统
```bash
lsblk -fs
df -h
```
![检查逻辑卷](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)此 lsblk 变体中，我们将列出按相反顺序显示依赖项的设备，此选项有助于标识按逻辑卷而不是原始 /dev/sd_disk] 设备名称分组的设备。

重要提示：确保将"无故障"选项添加到在 ADE 加密设备顶部创建的 LVM 卷的装载点选项中。 避免操作系统在启动过程中（或处于维护模式）期间卡住非常重要。 

加密磁盘在启动过程结束时解锁，LVM 卷和文件系统将自动装载。

如果未使用 nofail 选项，操作系统将永远不会进入启动 ADE 的阶段，数据磁盘将解锁并装载。

您可以测试重新启动 VM 并验证文件系统在启动后也会自动安装。 

考虑到此过程可能需要几分钟时间，具体取决于文件系统的数量和大小
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>重新启动 VM 并在重新启动后进行验证
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>用于在加密的 RAID
现在基础磁盘已加密，您可以继续创建与 LVM 相同的 RAID 结构，而不是使用设备名称，为每个磁盘使用 /dev/映射器路径。

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
![mdadm 创建](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>此处的 /dev/映射器/设备名称需要基于 lsblk 的输出为实际值进行替换。
#### <a name="checkmonitor-the-raid-creation"></a>检查/监视 RAID 创建：
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![检查 mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>在新 Raid 设备顶部创建文件系统：
```bash
mkfs.ext4 /dev/md10
```
为文件系统创建新的装载点，将新文件系统添加到 /etc/fstab，然后装载它
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
验证是否装载了新文件系统
```bash
lsblk -fs
df -h
```
![检查 mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

重要提示：确保将"无故障"选项添加到在 ADE 加密设备顶部创建的 RAID 卷的装载点选项中。 

避免操作系统在启动过程中（或处于维护模式）期间卡住非常重要。 

加密磁盘将在启动过程结束时解锁，如果不使用 nofail 选项，RAID 卷和文件系统将自动装载，直到 ADE 解锁它们。

操作系统永远不会进入启动 ADE 和数据磁盘解锁和装载的阶段。

您可以测试重新启动 VM 并验证文件系统在启动后也会自动安装。 考虑到此过程可能需要几分钟时间，具体取决于文件系统的数量和大小
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


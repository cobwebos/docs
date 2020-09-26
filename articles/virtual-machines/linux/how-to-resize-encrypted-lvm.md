---
title: 如何利用 Azure 磁盘加密调整加密的逻辑卷管理磁盘的大小
description: 本文介绍如何使用逻辑卷管理调整 ADE 加密磁盘的大小
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341539"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>如何调整用 Azure 磁盘加密加密的逻辑卷管理设备的大小

本文分步介绍如何使用 Linux 上的逻辑卷管理 (LVM) 调整 ADE 加密数据磁盘的大小，适用于多个方案。

此过程适用于以下环境：

- Linux 发行版
    - RHEL 7+
    - Ubuntu 16 +
    - SUSE 12 +
- Azure 磁盘加密单一传递扩展
- Azure 磁盘加密双重传递扩展

## <a name="considerations"></a>注意事项

本文档假定：

1. 现有的 LVM 配置。
   
   有关在 Linux VM 上配置 LVM 的详细信息，请参阅 [在 LINUX vm 上配置 lvm](configure-lvm.md) 。

2. 已使用 Azure 磁盘加密检查对磁盘进行加密检查在 [dm-crypt 上配置 lvm](how-to-configure-lvm-raid-on-crypt.md) 。

3. 你具有所需的 Linux 和 LVM 专业知识，可遵循以下示例。

4. 你了解如何使用 Azure 上的数据磁盘（如 [排查设备名称问题](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems)）中所述的建议使用/dev/disk/scsi1/路径。

## <a name="scenarios"></a>方案

本文中的过程适用于以下方案：

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>对于传统的 LVM 和 LVM Dm-crypt 配置

- 当 VG 中有可用空间时扩展逻辑卷

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>对于传统的 LVM 加密 (会对逻辑卷进行加密，而不是整个磁盘) 

- 扩展传统的 LVM 卷添加新的 PV
- 扩展传统的 LVM 卷调整现有 PV 的大小

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>对于 LVM-dm-crypt (推荐方法，将对整个磁盘进行加密，而不仅是逻辑卷) 

- 在 dm-crypt 卷上扩展 LVM 添加新的 PV
- 扩展现有 PV 的 dm-crypt 卷上的 LVM

> [!NOTE]
> 不建议在同一 VM 上混合使用传统的 LVM 加密和 Dm-crypt。

> [!NOTE]
> 这些示例使用磁盘、物理卷、卷组、逻辑卷、文件系统、Uuid 和装载点的预先存在的名称，需要替换这些示例中提供的值，以适合您的环境。

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>当 VG 中有可用空间时扩展逻辑卷

传统方法用于调整逻辑卷的大小，可将其应用于非加密磁盘、传统的 lvm 加密卷以及 Dm-crypt 配置。

1. 验证要增加的文件系统的当前大小：

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-fs1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. 验证 VG 是否有足够的空间来增加 LV

    ``` bash
    vgs
    ```

    ![scenarioa-vg](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    你还可以使用 "vgdisplay"

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioa-vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. 确定需要调整大小的逻辑卷

    ``` bash
    lsblk
    ```

    ![scenarioa-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    对于 LVM-Dm-crypt，此输出有不同之处，这表明加密层位于涵盖整个磁盘的加密层上

    ![scenarioa-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. 检查逻辑卷大小

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. 增加 LV 大小，使用 "-r" 联机调整文件系统的大小

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenarioa-重新调整大小-lv](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. 验证 LV 和文件系统的新大小

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-检查-fs](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    新大小会反映出来，它指示 LV 和文件系统的大小调整成功

7. 你可以再次检查 LV 信息以确认 LV 级别的更改

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>扩展传统的 LVM 卷添加新的 PV

当你需要添加新磁盘以增加卷组大小时，适用。

1. 验证要增加的文件系统的当前大小：

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-检查-fs](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. 验证当前的物理卷配置

    ``` bash
    pvs
    ```

    ![scenariob-pvs](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. 检查当前的 VG 信息

    ``` bash
    vgs
    ```

    ![scenariob-vgs](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. 检查当前磁盘列表

    应通过检查/dev/disk/azure/scsi1/下的设备来标识数据磁盘

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-scs1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. 检查 lsblk 的输出 

    ``` bash
    lsbk
    ```

    ![scenariob-lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. 将新磁盘附加到 VM

    跟进以下文档的第4步

   - [将磁盘附加到 VM](attach-disk-portal.md)

7. 附加磁盘后，请检查磁盘列表，注意新磁盘

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. 在新数据磁盘顶部创建新的 PV

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![scenariob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    此方法使用整个磁盘作为不带分区的 PV，还可以使用 "fdisk" 创建分区，然后将该分区用于 "pvcreate"。

9. 验证 PV 是否已添加到 PV 列表。

    ``` bash
    pvs
    ```

    ![scenariob-pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. 通过向其添加新的 PV 来扩展 VG

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![scenariob-vg-扩展](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. 检查新的 VG 大小

    ``` bash
    vgs
    ```

    ![scenariob-vgs1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. 使用 lsblk 确定需要调整大小的 LV

    ``` bash
    lsblk
    ```

    ![scenariob-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. 使用 "-r" 扩展 LV 大小以联机增加文件系统

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. 验证新的 LV 和 filesystem 大小

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-fs1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    必须知道，当在传统的 LVM 配置中使用 ADE 时，将在 LV 级别（而非磁盘级别）创建加密的层。

    此时，已加密层扩展到新磁盘。
    在平台级别，实际数据磁盘不会有任何加密设置，因此其加密状态不会更新。

    >[!NOTE]
    >这是 Dm-crypt 的建议方法的一些原因。 

15. 查看门户中的加密信息：

    ![scenariob-portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    需要添加新的 LV 并在 VM 上启用扩展，以便更新磁盘上的加密设置。
    
16. 添加新的 LV，在其上创建文件系统，并将其添加到/etc/fstab

17. 再次设置加密扩展，以在平台级别标记新数据磁盘上的加密设置。

    示例：

    CLI

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. 查看门户中的加密信息：

    ![scenariob-portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. 更新加密设置后，你可以删除新的 LV，还需要删除为其创建的/etc/fstab 和/etc/crypttab 中的条目。

    ![scenariob-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. 卸载逻辑卷

    ``` bash
    umount /mountpoint
    ```

21. 关闭卷的加密层

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. 删除 LV

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>扩展传统的 LVM 卷调整现有 PV 的大小

某些方案或限制要求调整现有磁盘的大小。

1. 标识加密磁盘

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenarioc-scsi1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![scenarioc-lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. 检查 pv 信息

    ``` bash
    pvs
    ```

    ![scenarioc-pvs](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    当前使用的所有 PVs 上的所有空间

3. 查看 VGs 信息

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![scenarioc-vgs](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. 检查磁盘大小，可以使用 fdisk 或 lsblk 列出驱动器大小

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-检查-fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    我们已确定哪些 PVs 与使用 lsblk 的 LVs 关联，你也可以通过运行 "lvdisplay" 来识别它。

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    在此特定情况下，所有4个数据驱动器都属于相同的 VG 和一个 LV，您的配置可能与此示例有所不同。

5. 检查当前的文件系统利用率：

    ``` bash
    df -h /datalvm*
    ```

    ![scenarioc-df](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. 调整数据磁盘的大小)  (：

    你可以引用 [Linux 扩展磁盘](expand-disks.md) (仅指代磁盘大小) ，可以使用门户、CLI 或 PowerShell 执行此步骤。

    >[!NOTE]
    >请考虑在虚拟磁盘上的调整大小操作无法在运行的 VM 上执行。 需要为此步骤解除分配 VM

7. 将磁盘调整为所需的值后，启动 VM，并使用 fdisk 检查新大小

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    在此特定情况下，/dev/sdd 已从5G 调整为20G

8. 检查当前 PV 大小

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    即使磁盘已调整大小，pv 仍具有以前的大小。

9. 调整 PV 大小

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![scenarioc-pvresize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. 检查 PV 大小

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    对要调整大小的所有磁盘应用相同的过程。

11. 查看 VG 信息

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG 现在有空间分配给 LVs

12. 调整 LV 大小

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![scenarioc-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. 检查 FS 大小

    ``` bash
    df -h /datalvm2
    ```

    ![scenarioc-df3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>扩展 LVM Dm-crypt 卷添加新的 PV

此方法严格遵循在 [dm-crypt 上配置 LVM](how-to-configure-lvm-raid-on-crypt.md) 中的步骤添加新磁盘并在 dm-crypt 配置下配置它。

您可以使用此方法将空间添加到已存在的 LV，也可以创建新的 VGs 或 LVs。

1. 验证 VG 的当前大小

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioe-data-vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. 验证要增加的 fs 和 lv 的大小

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenarioe-data-lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![scenarioe-fs01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. 向 VM 添加新的数据磁盘并对其进行标识。

    在添加磁盘前检查磁盘

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenarioe-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    在添加新磁盘之前，请检查磁盘

    ``` bash
    lsblk
    ```

    ![scenarioe-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    使用 PowerShell、Azure CLI 或 Azure 门户添加新磁盘。 在将磁盘添加到 VM 时，请查看如何 [附加磁盘](attach-disk-portal.md) 以供参考。

    在设备的内核名称方案后，新驱动器通常会被分配到下一个可用字母，在此特定情况下，新添加的磁盘为 sdd。

4. 添加新磁盘后检查磁盘

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenarioe-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![scenarioe-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. 在最近添加的磁盘顶部创建文件系统

    将最近添加的磁盘与/dev/disk/azure/scsi1/上的链接设备匹配

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![scenarioe-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![scenarioe-mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. 为新添加的磁盘创建新的临时装载点

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. 将最近创建的文件系统添加到/etc/fstab

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. 使用 mount 装载新创建的 fs

    ``` bash
    mount -a
    ```

9. 验证是否已装入新添加的 FS

    ``` bash
    df -h
    ```

    ![调整大小-scenarioe-df](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![调整大小-scenarioe-lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. 重新启动先前为数据驱动器启动的加密

    对于 Dm-crypt，建议使用 EncryptFormatAll，否则在设置更多磁盘时可能会发生双重加密。

    有关使用情况的信息，请参阅 [在 dm-crypt 上配置 LVM](how-to-configure-lvm-raid-on-crypt.md)。

    示例：

    ``` bash
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

    加密完成后，你将在新添加的磁盘上看到 dm-crypt 层

    ``` bash
    lsblk
    ```

    ![scenarioe-lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. 卸载新磁盘的加密层

    ``` bash
    umount ${newmount}
    ```

12. 检查当前的 pvs 信息

    ``` bash
    pvs
    ```

    ![scenarioe-currentpvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. 在磁盘加密层的顶层创建 PV

    从上一个 lsblk 命令获取设备名称，并在设备名称前面添加/dev/mapper 以创建 pv

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![scenarioe-pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    你将看到有关擦除当前 ext4 fs 签名的警告，这是预期的，请向此问题回答 y

14. 验证是否已将新的 PV 添加到 lvm 配置

    ``` bash
    pvs
    ```

    ![scenarioe-newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. 将新的 PV 添加到需要增加的 VG

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![scenarioe-vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. 验证 VG 的新大小和可用空间

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioe-vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    请注意，总 PE 计数和可用 PE/大小的增加

17. 使用 (lvextend 上的-r 选项增加 lv 和文件系统的大小。在本示例中，我们将获取 VG 中的总可用空间，并将其添加到给定的逻辑卷) 

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![scenarioe-lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. 验证 LV 的大小

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenarioe-lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. 验证刚刚调整大小的文件系统的大小

    ``` bash
    df -h mountpoint
    ```

    ![scenarioe-df1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. 验证是否在加密层的顶层创建了 LVM 层

    ``` bash
    lsblk
    ```

    ![scenarioe-lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    使用不带选项的 lsblk 将多次显示装入点，因为它按设备和逻辑卷进行排序，因此，你可能需要使用 lsblk-s 来反转排序，以便装载点显示一次，然后将多次显示这些磁盘。

    ``` bash
    lsblk -fs
    ```

    ![scenarioe-lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>扩展现有 PV 的 dm-crypt 卷上的 LVM

1. 标识加密磁盘

    ``` bash
    lsblk
    ```

    ![scenariof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![scenariof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. 检查 pv 信息

    ``` bash
    pvs
    ```

    ![scenariof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. 查看 vg 信息

    ``` bash
    vgs
    ```

    ![scenariof-vgs](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. 检查 lv 信息

    ``` bash
    lvs
    ```

    ![scenariof-lvs](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. 检查文件系统利用率

    ``` bash
    df -h /mountpoint(s)
    ```

    ![lvm-scenariof](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. 检查磁盘大小

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. 重设数据磁盘大小

    你可以引用 [Linux 扩展磁盘](expand-disks.md) (仅指代磁盘大小) ，可以使用门户、CLI 或 PowerShell 来执行此步骤。

    >[!NOTE]
    >请考虑在虚拟磁盘上的调整大小操作无法在运行的 VM 上执行。 需要为此步骤解除分配 VM

8. 检查磁盘大小

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    请注意，在这种情况) 下 (会将这两个磁盘的大小从2GB 调整为4GB，但 FS、LV 和 PV 大小仍保持不变。

9. 检查当前 pv 大小

    请记住，在 Dm-crypt 上，pv 是/dev/mapper/设备，而不是/dev/sd * 设备

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-pvs](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. 调整 pv 大小

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![scenariof-大小-pv](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. 调整大小后检查 pv 大小

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-pv](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. 调整加密层的大小（PV）

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    对要调整大小的所有磁盘应用相同的过程。

13. 查看 vg 信息

    ``` bash
    vgdisplay vgname
    ```

    ![scenariof-vg](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG 现在有空间分配给 LVs

14. 检查 lv 信息

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lv](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. 检查 fs 利用率

    ``` bash
    df -h /mountpoint
    ```

    ![scenariof-fs](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. 调整 lv 大小

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![scenariof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    我们正在使用-r 选项来执行 FS 大小调整

17. 检查 lv 信息

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. 检查文件系统利用率

    ``` bash
    df -h /mountpoint
    ```

    ![创建文件系统](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    将相同的大小调整过程应用到任何需要它的其他 lv

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密疑难解答](disk-encryption-troubleshooting.md)

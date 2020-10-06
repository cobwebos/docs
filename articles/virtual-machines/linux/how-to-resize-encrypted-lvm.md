---
title: 如何使用 Azure 磁盘加密调整加密的逻辑卷管理磁盘的大小
description: 本文介绍如何使用逻辑卷管理调整 ADE 加密磁盘的大小。
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 3a3e9b7406e11261aff12d77d9fbeed5debbe938
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744264"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>如何调整使用 Azure 磁盘加密的逻辑卷管理设备的大小

本文介绍如何调整使用 Azure 磁盘加密的数据磁盘的大小。 若要调整磁盘大小，请在 Linux 上使用逻辑卷管理 (LVM) 。 这些步骤适用于多种方案。

在以下环境中，可以使用此调整大小过程：

- Linux 分发版：
    - Red Hat Enterprise Linux (RHEL) 7 或更高版本
    - Ubuntu 16 或更高版本
    - SUSE 12 或更高版本
- Azure 磁盘加密版本： 
    - 单步扩展
    - 双通道扩展

## <a name="prerequisites"></a>先决条件

本文假设用户具备以下条件：

- 现有的 LVM 配置。 有关详细信息，请参阅 [在 LINUX VM 上配置 LVM](configure-lvm.md)。

- 已由 Azure 磁盘加密加密的磁盘。 有关详细信息，请参阅 [在加密设备上配置 LVM 和 RAID](how-to-configure-lvm-raid-on-crypt.md)。

- 使用 Linux 和 LVM 的经验。

- 在 Azure 上使用数据磁盘的 */dev/disk/scsi1/* 路径体验。 有关详细信息，请参阅 [排查 LINUX VM 设备名称问题](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems)。 

## <a name="scenarios"></a>方案

本文中的过程适用于以下方案：

- 传统的 LVM 和 LVM-dm-crypt 配置
- 传统 LVM 加密 
- LVM-dm-crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>传统的 LVM 和 LVM-dm-crypt 配置

当卷组 (VG) 具有可用空间时，传统的 LVM 和 LVM dm-crypt 配置会将逻辑卷 (LV) 扩展。

### <a name="traditional-lvm-encryption"></a>传统 LVM 加密 

在传统的 LVM 加密中，LVs 已加密。 不加密整个磁盘。

通过使用传统的 LVM 加密，你可以：

- 添加新的物理卷 (PV) 时，请扩展 LV。
- 当调整现有 PV 的大小时，扩展 LV。

### <a name="lvm-on-crypt"></a>LVM-dm-crypt 

建议对磁盘加密使用 "LVM-加密" 方法。 此方法加密整个磁盘，而不只是 LV。

通过使用 dm-crypt，你可以： 

- 添加新的 PV 时扩展 LV。
- 当调整现有 PV 的大小时，扩展 LV。

> [!NOTE]
> 建议不要在同一 VM 上混合传统的 LVM 加密和 dm-crypt。

以下各节提供了有关如何使用 LVM 和 dm-crypt 的示例。 这些示例使用磁盘的预先存在的值、PVs、VGs、LVs、文件系统、)  (Uuid 的全局唯一标识符，以及装入点。 将这些值替换为自己的值，以适合您的环境。

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>当 VG 具有可用空间时扩展 LV

调整 LVs 大小的传统方法是在 VG 具有可用空间时扩展 LV。 此方法可用于非加密磁盘、传统的 LVM 加密卷和 dm-crypt 配置。

1. 验证要增加的文件系统的当前大小：

    ``` bash
    df -h /mountpoint
    ```

    ![显示用于检查文件系统大小的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. 验证 VG 是否有足够的空间来增加 LV：

    ``` bash
    vgs
    ```

    ![显示用于检查 VG 上空间的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    你还可以使用 `vgdisplay` ：

    ``` bash
    vgdisplay vgname
    ```

    ![显示用于检查 VG 上空间的 V G 显示代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. 确定需要调整大小的 LV：

    ``` bash
    lsblk
    ```

    ![显示 l s b l k 命令结果的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    对于 LVM-dm-crypt，这种情况的区别在于，此输出表明加密层处于磁盘级别。

    ![显示 l s b l k 命令结果的屏幕截图。 将突出显示输出。 它显示加密的层。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. 检查 LV 大小：

    ``` bash
    lvdisplay lvname
    ```

    ![显示用于检查逻辑卷大小的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. 使用调整文件系统的联机大小，增加 LV 大小 `-r` ：

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![显示增加逻辑卷大小的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. 验证 LV 和文件系统的新大小：

    ``` bash
    df -h /mountpoint
    ```

    ![显示验证 LV 和文件系统大小的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    大小输出表明已成功调整 LV 和文件系统的大小。

可以再次检查 LV 信息以确认 LV 级别的更改：

``` bash
lvdisplay lvname
```

![显示用于确认新大小的代码的屏幕截图。 将突出显示这些大小。](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>通过添加新的 PV 扩展传统的 LVM 卷

当你需要添加新磁盘以增加 VG 大小时，请通过添加新的 PV 来扩展你的传统 LVM 卷。

1. 验证要增加的文件系统的当前大小：

    ``` bash
    df -h /mountpoint
    ```

    ![显示用于检查文件系统当前大小的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. 验证当前 PV 配置：

    ``` bash
    pvs
    ```

    ![显示检查当前 PV 配置的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. 查看当前的 VG 信息：

    ``` bash
    vgs
    ```

    ![显示用于检查当前卷组信息的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. 检查当前磁盘列表。 通过检查 */dev/disk/azure/scsi1/* 中的设备来标识数据磁盘。

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![显示用于检查当前磁盘列表的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. 检查的输出 `lsblk` ： 

    ``` bash
    lsbk
    ```

    ![显示检查 l s b l k 输出的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. 按照 [将数据磁盘附加到 LINUX vm](attach-disk-portal.md)中的说明，将新磁盘附加到 VM。

7. 检查磁盘列表，并注意新磁盘。

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![显示用于检查磁盘列表的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![屏幕截图，显示使用 l s b l k 检查磁盘列表的代码。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. 在新数据磁盘顶部创建新的 PV：

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![显示用于创建新 PV 的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    此方法使用整个磁盘作为不带分区的 PV。 或者，您可以使用 `fdisk` 创建分区，然后将该分区用于 `pvcreate` 。

9. 验证 PV 是否已添加到 PV 列表中：

    ``` bash
    pvs
    ```

    ![显示显示物理卷列表的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. 通过向其添加新的 PV 来扩展 VG：

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![显示扩展卷组的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. 检查新的 VG 大小：

    ``` bash
    vgs
    ```

    ![显示用于检查卷组大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. 用于 `lsblk` 标识需要调整大小的 LV：

    ``` bash
    lsblk
    ```

    ![显示标识需要调整大小的本地卷的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. 通过使用 `-r` 将文件系统联机增加来扩展 LV 大小：

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![显示增加文件系统联机大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. 验证 LV 和文件系统的新大小：

    ``` bash
    df -h /mountpoint
    ```

    ![显示用于检查本地卷和文件系统大小的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >将 Azure 数据加密用于传统的 LVM 配置时，会在 LV 级别（而非磁盘级别）创建加密的层。
    >
    >此时，已加密层扩展到新磁盘。 实际数据磁盘在平台级别没有加密设置，因此其加密状态不会更新。
    >
    >这是 dm-crypt 的建议方法的一些原因。 

15. 查看门户中的加密信息：

    ![显示门户中加密信息的屏幕截图。 磁盘名称和加密将突出显示。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    若要更新磁盘上的加密设置，请添加一个新的 LV 并在 VM 上启用该扩展。
    
16. 添加新的 LV，在其上创建文件系统，并将其添加到中 `/etc/fstab` 。

17. 重新设置加密扩展。 这次会将加密设置标记在平台级别的新数据磁盘上。 下面是一个 CLI 示例：

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. 查看门户中的加密信息：

    ![显示门户中加密信息的屏幕截图。 磁盘名称和加密信息会突出显示。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

更新加密设置后，可以删除新的 LV。 还会从创建的中删除该项 `/etc/fstab` `/etc/crypttab` 。

![显示删除新逻辑卷的代码的屏幕截图。 将突出显示 "删除的 F S" 选项卡和 "dm-crypt" 选项卡。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

请按照以下步骤完成清理：

1. 卸载 LV：

    ``` bash
    umount /mountpoint
    ```

1. 关闭卷的加密层：

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. 删除 LV：

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>通过调整现有 PV 的大小来扩展传统的 LVM 卷

Im 在某些情况下，你的限制可能要求你调整现有磁盘的大小。 以下是操作方法：

1. 确定加密磁盘：

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![显示用于标识加密磁盘的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![显示用于识别加密磁盘的替代代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. 检查 PV 信息：

    ``` bash
    pvs
    ```

    ![显示用于检查物理卷信息的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    图像中的结果显示当前正在使用所有 PVs 上的所有空间。

3. 查看 VG 信息：

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![显示用于检查卷组相关信息的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. 检查磁盘大小。 您可以使用 `fdisk` 或 `lsblk` 来列出驱动器大小。

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![显示用于检查磁盘大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    这里，我们发现了哪些 PVs 与 LVs 相关联 `lsblk -fs` 。 可以通过运行来确定关联 `lvdisplay` 。

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![显示用于识别与本地卷的物理卷关联的替代方法的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    在这种情况下，所有四个数据驱动器都属于相同的 VG 和一个 LV。 您的配置可能有所不同。

5. 检查当前文件系统的使用情况：

    ``` bash
    df -h /datalvm*
    ```

    ![显示用于检查文件系统利用率的代码的屏幕截图。 将突出显示命令和结果。](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. 按照 [扩展 Azure 托管磁盘](expand-disks.md#expand-an-azure-managed-disk)中的说明调整数据磁盘的大小。 可以使用门户、CLI 或 PowerShell。

    >[!IMPORTANT]
    >VM 正在运行时，无法调整虚拟磁盘的大小。 为此步骤解除分配 VM。

7. 启动 VM，并使用检查新大小 `fdisk` 。

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![显示用于检查磁盘大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    在这种情况下， `/dev/sdd` 已从 5 g 调整为 20 g。

8. 检查当前 PV 大小：

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![显示用于检查 P V 大小的代码的屏幕截图。结果突出显示。](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    即使磁盘已调整大小，PV 仍具有以前的大小。

9. 调整 PV 的大小：

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![显示调整物理卷大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. 检查 PV 大小：

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![显示用于检查物理卷大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    对要调整大小的所有磁盘应用相同的过程。

11. 检查 VG 信息。

    ``` bash
    vgdisplay vgname
    ```

    ![显示用于检查卷组的信息的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG 现在有足够的空间分配给 LVs。

12. 调整 LV 的大小：

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![显示调整 L V 大小的代码的屏幕截图。结果突出显示。](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. 检查文件系统的大小：

    ``` bash
    df -h /datalvm2
    ```

    ![显示用于检查文件系统大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>通过添加新的 PV 扩展 dm-crypt 卷

还可以通过添加新的 PV 来扩展 dm-crypt 卷。 此方法严格遵循在 [加密设备上配置 LVM 和 RAID](how-to-configure-lvm-raid-on-crypt.md#general-steps)中的步骤。 请参阅介绍如何添加新磁盘并在 dm-crypt 配置中进行设置的部分。

您可以使用此方法向现有的 LV 添加空间。 也可以创建新的 VGs 或 LVs。

1. 验证 VG 的当前大小：

    ``` bash
    vgdisplay vgname
    ```

    ![显示用于检查卷组大小的代码的屏幕截图。 突出显示结果。](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. 验证要扩展的文件系统和 LV 的大小：

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![显示检查本地卷大小的代码的屏幕截图。 突出显示结果。](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![显示用于检查文件系统大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. 向 VM 添加新的数据磁盘并对其进行标识。

    在添加新磁盘之前，请检查磁盘：

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![显示用于检查磁盘大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    下面是在添加新磁盘之前检查磁盘的另一种方法：

    ``` bash
    lsblk
    ```

    ![显示用于检查磁盘大小的替代代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    若要添加新磁盘，可以使用 PowerShell、Azure CLI 或 Azure 门户。 有关详细信息，请参阅 [将数据磁盘附加到 LINUX VM](attach-disk-portal.md)。

    内核名称方案适用于新添加的设备。 通常会为新驱动器分配下一个可用的驱动器号。 在这种情况下，添加的磁盘为 `sdd` 。

4. 检查磁盘以确保已添加新磁盘：

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![显示列出磁盘的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![显示在输出中新添加的磁盘的屏幕截图。](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. 在最近添加的磁盘上创建文件系统。 将磁盘与上的链接设备匹配 `/dev/disk/azure/scsi1/` 。

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![显示用于创建文件系统的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![显示用于创建文件系统并使磁盘与链接设备匹配的其他代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. 为新添加的磁盘创建临时装入点：

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. 向添加最近创建的文件系统 `/etc/fstab` 。

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. 装载新创建的文件系统：

    ``` bash
    mount -a
    ```

9. 验证是否已装载新文件系统：

    ``` bash
    df -h
    ```

    ![显示验证文件系统是否已装载的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![显示验证是否已装载文件系统的其他代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. 重新启动先前为数据驱动器启动的加密。

    >[!TIP]
    >对于 LVM-dm-crypt，建议使用 `EncryptFormatAll` 。 否则，在设置更多磁盘时，可能会看到双精度加密。
    >
    >有关详细信息，请参阅 [在加密设备上配置 LVM 和 RAID](how-to-configure-lvm-raid-on-crypt.md)。

    下面是一个示例：

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

    加密完成后，会在新添加的磁盘上看到 dm-crypt 层：

    ``` bash
    lsblk
    ```

    ![显示用于检查 dm-crypt 层的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. 卸载新磁盘的加密层：

    ``` bash
    umount ${newmount}
    ```

12. 检查当前 PV 信息：

    ``` bash
    pvs
    ```

    ![显示用于检查物理卷信息的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. 在磁盘加密层的顶层创建 PV。 从上一个命令获取设备名称 `lsblk` 。 `/dev/`在设备名称前面添加映射器，以创建 PV：

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![显示在加密层上创建物理卷的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    将显示有关擦除当前签名的警告 `ext4 fs` 。 此警告是在意料之内。 请通过解决此问题 `y` 。

14. 验证是否已将新的 PV 添加到 LVM 配置中：

    ``` bash
    pvs
    ```

    ![显示验证物理卷是否已添加到 LVM 配置的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. 将新的 PV 添加到需要增加的 VG。

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![显示将物理卷添加到卷组的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. 验证 VG 的新大小和可用空间：

    ``` bash
    vgdisplay vgname
    ```

    ![显示验证卷组的大小和可用空间的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    请注意 `Total PE` 计数和的增加 `Free PE / Size` 。

17. 增加 LV 和文件系统的大小。 使用 `-r` 上的选项 `lvextend` 。 在此示例中，我们将 VG 中的总可用空间添加到给定的 LV。

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![显示增加本地卷和文件系统大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

按照后续步骤验证更改。

1. 验证 LV 的大小：

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![显示验证本地卷新大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. 验证文件系统的新大小：

    ``` bash
    df -h mountpoint
    ```

    ![显示验证文件系统新大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. 验证 LVM 层是否在加密层的顶层：

    ``` bash
    lsblk
    ```

    ![显示验证 LVM 层是否在加密层之上的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    如果不使用 `lsblk` 选项，则会多次看到装入点。 命令按设备和 LVs 进行排序。 

    你可能想要使用 `lsblk -fs` 。 在此命令中，将 `-fs` 反转排序顺序，以便显示一次装入点。 磁盘多次显示。

    ``` bash
    lsblk -fs
    ```

    ![显示替代代码的屏幕截图，用于验证 LVM 层是否在加密层的顶层。 结果突出显示。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>通过调整现有 PV 的大小来扩展 dm-crypt 卷上的 LVM

1. 确定加密磁盘：

    ``` bash
    lsblk
    ```

    ![显示用于标识加密磁盘的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![显示用于标识加密磁盘的替代代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. 检查 PV 信息：

    ``` bash
    pvs
    ```

    ![显示用于检查物理卷信息的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. 查看 VG 信息：

    ``` bash
    vgs
    ```

    ![显示用于检查卷组的信息的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. 检查 LV 信息：

    ``` bash
    lvs
    ```

    ![显示用于检查本地卷信息的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. 检查文件系统的使用情况：

    ``` bash
    df -h /mountpoint(s)
    ```

    ![显示用于检查文件系统的使用情况的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. 检查磁盘的大小：

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![显示用于检查磁盘大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. 调整数据磁盘的大小。 可以使用门户、CLI 或 PowerShell。 有关详细信息，请参阅在 [LINUX VM 上扩展虚拟硬盘](expand-disks.md#expand-an-azure-managed-disk)中的磁盘大小调整部分。 

    >[!IMPORTANT]
    >VM 正在运行时，无法调整虚拟磁盘的大小。 为此步骤解除分配 VM。

8. 检查磁盘大小：

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![显示用于检查磁盘大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    在这种情况下，这两个磁盘的大小已从 2 GB 调整为 4 GB。 但文件系统、LV 和 PV 的大小仍保持不变。

9. 检查当前 PV 大小。 请记住，在 dm-crypt 上，PV 是 `/dev/mapper/` 设备，而不是 `/dev/sd*` 设备。

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![显示检查当前物理卷大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. 调整 PV 的大小：

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![显示调整物理卷大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. 检查新的 PV size：

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![显示用于检查物理卷大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. 在 PV 上调整加密层的大小：

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    对要调整大小的所有磁盘应用相同的过程。

13. 查看 VG 信息：

    ``` bash
    vgdisplay vgname
    ```

    ![显示用于检查卷组的信息的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG 现在有足够的空间分配给 LVs。

14. 检查 LV 信息：

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![显示用于检查本地卷信息的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. 检查文件系统的使用情况：

    ``` bash
    df -h /mountpoint
    ```

    ![显示用于检查文件系统的使用情况的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. 调整 LV 的大小：

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![显示调整本地卷大小的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    这里，我们使用 `-r` 选项来调整文件系统的大小。

17. 检查 LV 信息：

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![显示获取有关本地卷的信息的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. 检查文件系统的使用情况：

    ``` bash
    df -h /mountpoint
    ```

    ![显示用于检查文件系统利用率的代码的屏幕截图。 结果突出显示。](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

向需要此操作的任何其他 LV 应用相同的调整大小过程。

## <a name="next-steps"></a>后续步骤

[Azure 磁盘加密故障排除](disk-encryption-troubleshooting.md)

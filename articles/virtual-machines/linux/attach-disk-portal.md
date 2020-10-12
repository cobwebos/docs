---
title: 将数据磁盘附加到 Linux VM
description: 使用门户将新的或现有数据磁盘附加到 Linux VM。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: a37ed39f3c663f9f77daa1ed8f6946403348edd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89072632"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>使用门户将数据磁盘附加到 Linux VM 
本文介绍如何通过 Azure 门户将新磁盘和现有磁盘附加到 Linux 虚拟机。 也可以[在 Azure 门户中将数据磁盘附加到 Windows VM](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

将磁盘附加到 VM 前，请查看以下提示：

* 虚拟机的大小决定了可以附加多少个磁盘。 有关详细信息，请参阅[虚拟机大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 附加到虚拟机的磁盘实际上是存储在 Azure 中的 .vhd 文件。 有关详细信息，请查看[托管磁盘简介](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 附加磁盘后，需要[连接到 Linux VM 以装载新磁盘](#connect-to-the-linux-vm-to-mount-the-new-disk)。


## <a name="find-the-virtual-machine"></a>查找虚拟机
1. 转到 [Azure 门户](https://portal.azure.com/)以找到 VM。 搜索并选择“虚拟机”。
2. 从列表中选择 VM。
3. 在“虚拟机”页面的“设置”下，选择“磁盘”。  


## <a name="attach-a-new-disk"></a>附加新磁盘

1. 在 " **磁盘** " 窗格的 " **数据磁盘**" 下，选择 " **创建并附加新磁盘**"。

1. 输入托管磁盘的名称。 查看默认设置，并根据需要更新“存储类型”、“大小(GiB)”、“加密”和“主机缓存”   。
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="检查磁盘设置。":::


1. 完成后，选择页面顶部的“保存”，以创建托管磁盘并更新 VM 配置。


## <a name="attach-an-existing-disk"></a>附加现有磁盘
1. 在 " **磁盘** " 窗格的 " **数据磁盘**" 下，选择 "  **附加现有磁盘**"。
1. 单击 " **磁盘名称** " 的下拉菜单，并从可用托管磁盘列表中选择一个磁盘。 

1. 单击“保存”以附加现有托管磁盘并更新 VM 配置：
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>连接到 Linux VM 以装入新磁盘
若要对新磁盘进行分区、格式化和装载，以便 Linux VM 可以使用它，请通过 SSH 登录到 VM。 有关详细信息，请参阅[如何在 Azure 中将 SSH 用于 Linux](mac-create-ssh-keys.md)。 以下示例使用用户名“azureuser”连接到使用公共 IP 地址 10.123.123.25 的 VM ： 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>找到磁盘

连接到 VM 后，需要找到该磁盘。 在此示例中，我们使用 `lsblk` 来列出磁盘。 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

输出类似于以下示例：

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
```

在此示例中，添加的磁盘是 `sdc`。 它的 LUN 为 0，大小为 4 GB。

对于更复杂的示例，多个数据磁盘在门户中显示如下：

:::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="检查磁盘设置。":::

在图片中，可以看到有 3 个数据磁盘：LUN 0 为 4 GB，LUN 1 为 16 GB，LUN 2 为 32G。

下面是使用 `lsblk` 时可能显示的内容：

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

从 `lsblk` 的输出可以看到，LUN 0 处的 4GB 磁盘为 `sdc`，LUN 1 处的 16 GB 磁盘为 `sdd`，LUN 2 处的 32G 磁盘为 `sde`。

### <a name="partition-a-new-disk"></a>对新磁盘进行分区

如果使用包含数据的现有磁盘，请跳到装载磁盘。 如果附加新磁盘，需要对磁盘进行分区。

`parted` 实用程序可用于对数据磁盘进行分区和格式设置。

> [!NOTE]
> 建议使用适用于你的发行版的最新版 `parted`。
> 如果磁盘大于或等于 2 TiB，必须使用 GPT 分区。 如果磁盘小于 2 TiB，则可以使用 MBR 或 GPT 分区。  


以下示例在 `/dev/sdc` 上使用 `parted`，那里是大多数 VM 上第一块数据磁盘通常所在的位置。 将 `sdc` 替换为磁盘的正确选项。 我们还使用 [XFS](https://xfs.wiki.kernel.org/) 文件系统对其进行格式设置。

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

请使用 [`partprobe`](https://linux.die.net/man/8/partprobe) 实用程序以确保内核知晓新分区和文件系统。 如果无法使用 `partprobe`，则可能导致 blkid 或 lslbk 命令不立即返回新文件系统的 UUID。

### <a name="mount-the-disk"></a>装载磁盘

使用 `mkdir` 创建一个目录来装载文件系统。 以下示例在 `/datadrive` 处创建一个目录：

```bash
sudo mkdir /datadrive
```

然后，使用 `mount` 来装载文件系统。 以下示例将 */dev/sdc1* 分区装载到 `/datadrive` 装入点：

```bash
sudo mount /dev/sdc1 /datadrive
```

若要确保在重新引导后自动重新装载驱动器，必须将其添加到 */etc/fstab* 文件。 强烈建议在 /etc/fstab 中使用 UUID（全局唯一标识符）来引用驱动器而不是只使用设备名称（例如 //dev/sdc1） 。 如果 OS 在启动过程中检测到磁盘错误，使用 UUID 可以避免将错误的磁盘装载到给定位置。 然后为剩余的数据磁盘分配这些设备 ID。 若要查找新驱动器的 UUID，请使用 `blkid` 实用工具：

```bash
sudo blkid
```

输出与以下示例类似：

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> 错误地编辑 **/etc/fstab** 文件可能会导致系统无法引导。 如果没有把握，请参考分发的文档来获取有关如何正确编辑该文件的信息。 另外，建议在编辑前备份 /etc/fstab 文件。

接下来，在文本编辑器中打开 */etc/fstab* 文件，如下所示：

```bash
sudo nano /etc/fstab
```

在此示例中，使用在之前的步骤中创建的 `/dev/sdc1` 设备的 UUID 值并使用 `/datadrive` 装入点。 在 `/etc/fstab` 文件的末尾添加以下行：

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

因为使用的是 nano 编辑器，所以在编辑完文件后，使用 `Ctrl+O` 写入该文件，然后使用 `Ctrl+X` 退出该编辑器。

> [!NOTE]
> 之后，在不编辑 fstab 的情况下删除数据磁盘可能会导致 VM 无法启动。 大多数分发版都提供 *nofail* 和/或 *nobootwait* fstab 选项。 这些选项使系统在磁盘无法装载的情况下也能启动。 有关这些参数的详细信息，请查阅分发文档。
> 
> 即使文件系统已损坏或磁盘在引导时不存在， *nofail* 选项也能确保 VM 启动。 如果不使用此选项，可能会遇到 [Cannot SSH to Linux VM due to FSTAB errors](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)


## <a name="verify-the-disk"></a>验证磁盘

现在可以再次使用 `lsblk` 来查看磁盘和装入点。

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

输出将如下所示：

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

可以看到 `sdc` 现在已装载到 `/datadrive`。

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure 中对 Linux 的 TRIM/UNMAP 支持

某些 Linux 内核支持 TRIM/UNMAP 操作以放弃磁盘上未使用的块。 此功能主要用于标准存储中，如果你创建大型文件后又将其删除，则该功能将通知 Azure 已删除的页不再有效并且可以丢弃，可以节省成本。

在 Linux VM 中有两种方法可以启用 TRIM 支持。 与往常一样，有关建议的方法，请参阅分发：

* 在 */etc/fstab* 中使用 `discard` 装载选项，例如：

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
    ```
* 在某些情况下，`discard` 选项可能会影响性能。 此处，还可以从命令行手动运行 `fstrim` 命令，或将其添加到 crontab 以定期运行：
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="next-steps"></a>后续步骤
还可使用 Azure CLI [附加数据磁盘](add-disk.md)。
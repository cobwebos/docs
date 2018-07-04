---
title: 使用 Azure CLI 将数据磁盘添加到 Linux VM | Microsoft Docs
description: 了解如何使用 Azure 将持久性数据磁盘添加到 Linux VM
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c41090943e4053ddf0ea46e9da1b3b5c7dbbf132
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331217"
---
# <a name="add-a-disk-to-a-linux-vm"></a>将磁盘添加到 Linux VM
本文介绍了如何将持久性磁盘附加到 VM 以便持久保存数据 - 即使 VM 由于维护或调整大小而重新预配。 


## <a name="attach-a-new-disk-to-a-vm"></a>将新磁盘附加到 VM

如果只需要在 VM 上添加新的空数据磁盘，请使用 [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) 命令以及 `--new` 参数。 如果 VM 位于某个可用性区域中，则会自动在与 VM 相同的区域中创建磁盘。 有关详细信息，请参阅[可用性区域概述](../../availability-zones/az-overview.md)。 以下示例创建一个名为“myDataDisk”且大小为 50 GB 的磁盘：

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --disk myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>附加现有磁盘 

若要附加现有磁盘，请查找磁盘 ID 并将该 ID 传递到 [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) 命令。 以下示例查询 *myResourceGroup* 中名为 *myDataDisk* 的磁盘，然后将其附加到名为 *myVM* 的 VM：

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```


## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>连接到 Linux VM 以装入新磁盘
若要对新磁盘进行分区、格式化和装载，以便 Linux VM 可以使用它，请通过 SSH 登录到 VM。 有关详细信息，请参阅[如何在 Azure 中将 SSH 用于 Linux](mac-create-ssh-keys.md)。 以下示例使用公共 DNS 条目 *mypublicdns.westus.cloudapp.azure.com* 和用户名 *azureuser* 连接到一个 VM： 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

连接到 VM 后就可以附加磁盘了。 首先，使用 `dmesg` 来查找磁盘（用于发现新磁盘的方法可能各不相同）。 以下示例使用 dmesg 来筛选 *SCSI* 磁盘：

```bash
dmesg | grep SCSI
```

输出类似于以下示例：

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

此处，*sdc* 是我们需要的磁盘。 使用 `fdisk` 对磁盘进行分区，将其设置为分区 1 中的主磁盘，并接受其他默认值。 以下示例在 */dev/sdc* 上启动 `fdisk` 进程：

```bash
sudo fdisk /dev/sdc
```

使用 `n` 命令添加新分区。 在此示例中，我们还选择主分区的 `p` 并接受其余默认值。 输出将类似于以下示例：

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

通过键入 `p` 打印分区表并使用 `w` 将该表写入磁盘，然后退出。 输出应类似于以下示例：

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

现在，使用 `mkfs` 命令将文件系统写入到该分区。 指定文件系统类型和设备名称。 以下示例在通过前面的步骤创建的 */dev/sdc1* 分区中创建 *ext4* 文件系统：

```bash
sudo mkfs -t ext4 /dev/sdc1
```

输出类似于以下示例：

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

现在，使用 `mkdir` 创建一个目录来装载文件系统。 以下示例在 */datadrive* 处创建一个目录：

```bash
sudo mkdir /datadrive
```

然后，使用 `mount` 来装载文件系统。 以下示例将 */dev/sdc1* 分区装载到 */datadrive* 装入点：

```bash
sudo mount /dev/sdc1 /datadrive
```

若要确保在重新引导后自动重新装载驱动器，必须将其添加到 */etc/fstab* 文件。 此外，强烈建议在 */etc/fstab* 中使用 UUID（全局唯一标识符）来引用驱动器而不是只使用设备名称（例如 */dev/sdc1*）。 如果 OS 在启动过程中检测到磁盘错误，使用 UUID 可以避免将错误的磁盘装载到给定位置。 然后，为剩余的数据磁盘分配这些设备 ID。 若要查找新驱动器的 UUID，请使用 `blkid` 实用工具：

```bash
sudo -i blkid
```

输出与以下示例类似：

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> 错误地编辑 **/etc/fstab** 文件可能会导致系统无法引导。 如果没有把握，请参考分发的文档来获取有关如何正确编辑该文件的信息。 另外，建议在编辑之前创建 /etc/fstab 文件的备份。

接下来，在文本编辑器中打开 */etc/fstab* 文件，如下所示：

```bash
sudo vi /etc/fstab
```

在此示例中，使用在之前的步骤中创建的 /dev/sdc1 设备的 UUID 值并使用装入点 /datadrive。 将以下行添加到 */etc/fstab* 文件的末尾：

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> 之后，在不编辑 fstab 的情况下删除数据磁盘可能会导致 VM 无法启动。 大多数分发版都提供 *nofail* 和/或 *nobootwait* fstab 选项。 这些选项使系统在磁盘无法装载的情况下也能启动。 有关这些参数的详细信息，请查阅分发文档。
> 
> 即使文件系统已损坏或磁盘在引导时不存在，*nofail* 选项也能确保 VM 启动。 如果不使用此选项，可能会遇到 [Cannot SSH to Linux VM due to FSTAB errors](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)（由于 FSTAB 错误而无法通过 SSH 连接到 Linux VM）中所述的行为

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure 中对 Linux 的 TRIM/UNMAP 支持
某些 Linux 内核支持 TRIM/UNMAP 操作以放弃磁盘上未使用的块。 此功能主要用于标准存储中，如果你创建大型文件后又将其删除，则该功能将通知 Azure 已删除的页不再有效并且可以丢弃，可以节省成本。

在 Linux VM 中有两种方法可以启用 TRIM 支持。 与往常一样，有关建议的方法，请参阅分发：

* 在 */etc/fstab* 中使用 `discard` 装载选项，例如：

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
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

## <a name="troubleshooting"></a>故障排除
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>后续步骤
* 为确保正确配置 Linux VM，请查看有关[优化 Linux 计算机性能](optimization.md)的建议。
* 可以添加更多的磁盘来扩展存储容量，[配置 RAID](configure-raid.md) 来提高性能。


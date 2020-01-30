---
title: 排查由于文件系统错误而导致的 Linux VM 启动问题 |Microsoft Docs
description: 说明为何无法启动 Linux VM 以及如何解决此问题。
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842395"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>排查由于文件系统错误导致的 Linux VM 启动问题

无法使用安全外壳（SSH）连接到 Azure Linux 虚拟机（VM）。 在[Azure 门户](https://portal.azure.com/)上运行启动诊断功能时，将看到类似于以下示例的日志条目。

## <a name="examples"></a>示例

下面是可能的错误的示例。

### <a name="example-1"></a>示例 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>示例 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>示例 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>示例 4 

此示例由干净的 fsck 引起。 在这种情况下，还附加了附加到 VM 的其他数据磁盘（/dev/sdc1 和/dev/sde1）。

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

如果文件系统未完全关闭或与存储相关的问题，则可能出现此问题。 这些问题包括硬件或软件错误、驱动程序或程序的问题、写错误，等等。对关键数据进行备份始终很重要。 本文中描述的工具可以帮助恢复文件系统，但仍可能会丢失数据。

Linux 提供了多个文件系统检查程序。 Azure 中最常见的分发是： [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific)、 [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)和[Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair)。

## <a name="resolution"></a>分辨率

若要解决此问题，请使用[串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)将 VM 启动到紧急模式，并使用该工具修复文件系统。 如果未在 VM 上启用串行控制台或不工作，请参阅本文的[修复 VM 脱机](#repair-the-vm-offline)部分。

## <a name="use-the-serial-console"></a>使用串行控制台

1. 连接到串行控制台。

   > [!Note]
   > 有关使用适用于 Linux 的串行控制台的详细信息，请参阅：
   > * [使用串行控制台访问 GRUB 和单用户模式](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [使用串行控制台进行 SysRq 和 NMI 调用](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. 选择电源图标按钮，然后选择 "重新启动 VM"。 （如果串行控制台未启用或未成功连接，你将看不到此按钮。）

   ![IMAGE](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. 在紧急模式下启动 VM。

4. 输入用于登录到紧急模式的根帐户的密码。

5. 使用带有-n 选项的 xfs_repair 检测文件系统中的错误。 在下面的示例中，我们假定系统分区为/dev/sda1。 将其替换为 VM 的适当值：

   ```
   xfs_repair -n /dev/sda1
   ```

6. 运行以下命令来修复文件系统：

   ```
   xfs_repair /dev/sda1
   ```

7. 如果收到错误消息 "错误：文件系统在需要重播的日志中有重要的元数据更改"，请创建一个临时目录并装载 filesystem：

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. 如果磁盘未能装入，请运行包含-L 选项的 xfs_repair 命令（强制日志为零）：

   ```
   xfs_repair /dev/sda1 -L
   ```

9. 接下来，尝试装载文件系统。 如果磁盘安装成功，您将收到以下输出：
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. 重新启动 VM，然后检查问题是否已解决。

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>修复 VM 脱机

1. 将 VM 的系统磁盘作为数据磁盘附加到恢复 VM （任何工作的 Linux VM）。 为此，可以使用[CLI 命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)，也可以使用[VM repair 命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)自动设置恢复 VM。

2. 找到所附加的系统磁盘的驱动器标签。 在这种情况下，我们假设附加的系统磁盘的标签是/dev/sdc1 将其替换为 VM 的适当值。

3. 使用带有-n 选项的 xfs_repair 检测文件系统中的错误。

   ```
   xfs_repair -n /dev/sdc1
   ```

4. 运行以下命令来修复文件系统：

   ```
   xfs_repair /dev/sdc1
   ```

5. 如果收到错误消息 "错误：文件系统在需要重播的日志中有重要的元数据更改"，请创建一个临时目录并装载 filesystem：

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   如果磁盘未能装入，请运行包含-L 选项的 xfs_repair 命令（强制日志为零）：

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. 接下来，尝试装载文件系统。 如果磁盘安装成功，您将收到以下输出：

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. 卸载并分离原始虚拟硬盘，然后从原始系统磁盘创建 VM。 为此，可以使用[CLI 命令](troubleshoot-recovery-disks-linux.md)或[VM repair 命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)（如果你使用它们来创建恢复 VM）。

8. 检查问题是否已解决。

## <a name="next-steps"></a>后续步骤

* [通过将 OS 磁盘附加到带有 Azure CLI 2.0 的恢复 VM 来对 Linux VM 进行故障排除](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [使用门户将数据磁盘附加到 Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)


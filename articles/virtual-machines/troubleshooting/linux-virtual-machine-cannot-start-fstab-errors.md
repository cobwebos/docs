---
title: 排查由于 fstab 错误而导致的 Linux VM 启动问题 |Microsoft Docs
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
ms.openlocfilehash: 868a0238092786d0999a6a41de71d30011bbef7a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245339"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>排查由于 fstab 错误而导致的 Linux VM 启动问题

无法使用安全外壳（SSH）连接连接到 Azure Linux 虚拟机（VM）。 在[Azure 门户](https://portal.azure.com/)上运行[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)功能时，将看到类似于以下示例的日志条目：

## <a name="examples"></a>示例

下面是可能的错误的示例。

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>示例 1：磁盘由 SCSI ID 而不是全局唯一标识符（UUID）装载

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type “journalctl -xb” to viewsystem logs, “systemctl reboot” to reboot, “systemctl default” to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>示例 2：CentOS 上缺少未连接的设备

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device (“nofail” fstab option may be used to skip this device)
/dev/sdd1: nonexistent device (“nofail” fstab option may be used to skip this device)
/dev/sde1: nonexistent device (“nofail” fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>示例 3：VM 无法启动，因为 fstab 配置错误或磁盘不再附加

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>示例 4：串行日志项显示错误的 UUID

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run ‘setenforce 1’ to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

如果文件系统表（fstab）语法不正确，或者映射到 "/etc/fstab" 文件中的条目的所需数据磁盘未附加到 VM，则可能出现此问题。

## <a name="resolution"></a>分辨率

若要解决此问题，请使用 Azure 虚拟机的串行控制台在紧急模式下启动 VM。 然后使用该工具修复文件系统。 如果虚拟机上未启用串行控制台，请参阅[修复 vm 脱机](#repair-the-vm-offline)部分。

## <a name="use-the-serial-console"></a>使用串行控制台

1. 连接到[串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)。
2. 使用本地用户和密码登录到系统。

   > [!Note]
   > 不能使用 SSH 密钥登录到串行控制台中的系统。

3. 查找指示磁盘未装入的错误。 在以下示例中，系统尝试附加不再存在的磁盘：

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. 使用根密码（基于 Red Hat 的 Vm）连接到 VM。

5. 使用您最喜欢的文本编辑器打开 fstab 文件。 装载磁盘后，为 Nano 运行以下命令：

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 查看列出的文件系统。 Fstab 文件中的每行都表示在 VM 启动时装载的文件系统。 有关 fstab 文件语法的详细信息，请运行 man fstab 命令。 若要解决开始失败的问题，请查看每一行，确保它在结构和内容中都是正确的。

   > [!Note]
   > * 每行上的字段由制表符或空格分隔。 空行被忽略。 以数字符号（#）作为第一个字符的行是注释。 注释行可以保留在 fstab 文件中，但不会进行处理。 建议您注释 fstab 的行，而不是删除行。
   > * 要恢复和启动 VM，文件系统分区应是唯一需要的分区。 VM 可能会遇到有关其他注释分区的应用程序错误。 但是，VM 应在没有其他分区的情况下启动。 稍后可以取消注释任何注释行。
   > * 建议使用文件系统分区的 UUID 将数据磁盘装载到 Azure Vm 上。 例如，运行以下命令： ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 若要确定文件系统的 UUID，请运行 blkid 命令。 有关语法的详细信息，请运行 man blkid 命令。
   > * Nofail 选项有助于确保 VM 启动，即使文件系统已损坏或启动时文件系统不存在也是如此。 建议你使用 fstab 文件中的 nofail 选项，使启动在发生错误发生在 VM 启动后不需要的分区中时继续。

7. 更改或注释掉 fstab 文件中的任何不正确或不必要的行，使 VM 能够正常启动。

8. 保存对 fstab 文件所做的更改。

9. 重启虚拟机。

10. 如果条目已成功完成注释或修复，则系统会在门户中看到 bash 提示。 检查是否可以连接到 VM。

11. 通过运行 mount – a 命令来测试任何 fstab 更改时检查装入点。 如果没有错误，则装入点应良好。

## <a name="repair-the-vm-offline"></a>修复 VM 脱机

1. 将 VM 的系统磁盘作为数据磁盘附加到恢复 VM （任何工作的 Linux VM）。 为此，可以使用[CLI 命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)，也可以使用[VM repair 命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)自动设置恢复 VM。

2. 在恢复 VM 上装载系统磁盘作为数据磁盘后，请在进行更改之前备份 fstab 文件，然后按照后续步骤更正 fstab 文件。

3.  查找指示磁盘未装入的错误。 在以下示例中，系统尝试附加不再存在的磁盘：

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. 使用根密码（基于 Red Hat 的 Vm）连接到 VM。

5. 使用您最喜欢的文本编辑器打开 fstab 文件。 装入磁盘后，为 Nano 运行以下命令。 请确保使用的是位于已装入磁盘上的 fstab 文件，而不是修复 VM 上的 fstab 文件。

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 查看列出的文件系统。 Fstab 文件中的每行都表示在 VM 启动时装载的文件系统。 有关 fstab 文件语法的详细信息，请运行 man fstab 命令。 若要解决开始失败的问题，请查看每一行，确保它在结构和内容中都是正确的。

   > [!Note]
   > * 每行上的字段由制表符或空格分隔。 空行被忽略。 以数字符号（#）作为第一个字符的行是注释。 注释行可以保留在 fstab 文件中，但不会进行处理。 建议您注释 fstab 的行，而不是删除行。
   > * 要恢复和启动 VM，文件系统分区应是唯一需要的分区。 VM 可能会遇到有关其他注释分区的应用程序错误。 但是，VM 应在没有其他分区的情况下启动。 稍后可以取消注释任何注释行。
   > * 建议使用文件系统分区的 UUID 将数据磁盘装载到 Azure Vm 上。 例如，运行以下命令： ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 若要确定文件系统的 UUID，请运行 blkid 命令。 有关语法的详细信息，请运行 man blkid 命令。 请注意，要恢复的磁盘现在已装载到新的 VM 上。 虽然 Uuid 应一致，但设备分区 Id （例如 "/dev/sda1"）在此 VM 上是不同的。 位于非系统 VHD 上的原始失败 VM 的文件系统分区不适用于[使用 CLI 命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)的恢复 VM。
   > * Nofail 选项有助于确保 VM 启动，即使文件系统已损坏或启动时文件系统不存在也是如此。 建议你使用 fstab 文件中的 nofail 选项，使启动在发生错误发生在 VM 启动后不需要的分区中时继续。

7. 更改或注释掉 fstab 文件中的任何不正确或不必要的行，使 VM 能够正常启动。

8. 保存对 fstab 文件所做的更改。

9. 重新启动虚拟机，或重新生成原始 VM。

10. 如果条目已成功完成注释或修复，则系统会在门户中看到 bash 提示。 检查是否可以连接到 VM。

11. 通过运行 mount – a 命令来测试任何 fstab 更改时检查装入点。 如果没有错误，则装入点应良好。

12. 卸载并分离原始虚拟硬盘，然后从原始系统磁盘创建 VM。 为此，可以使用[CLI 命令](troubleshoot-recovery-disks-linux.md)或[VM repair 命令](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)（如果你使用它们来创建恢复 VM）。

13. 再次创建 VM 并且可以通过 SSH 连接到该 VM 后，请执行以下操作：
    * 查看在恢复期间更改或注释掉的任何 fstab 行。
    * 请确保正确使用 UUID 和 nofail 选项。
    * 重新启动 VM 之前，请测试任何 fstab 更改。 为此，请使用以下命令： ``$ sudo mount -a``
    * 创建更正后的 fstab 文件的附加副本，以便在将来的恢复方案中使用。

## <a name="next-steps"></a>后续步骤

* [通过将 OS 磁盘附加到带有 Azure CLI 2.0 的恢复 VM 来对 Linux VM 进行故障排除](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [通过使用 Azure 门户将 OS 磁盘附加到恢复 VM 来对 Linux VM 进行故障排除](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)


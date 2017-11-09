---
title: "如何在 Azure VM 上重置本地 Linux 密码 | Microsoft Docs"
description: "在 Azure VM 上重置本地 Linux 密码的步骤简介"
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 7/3/2017
ms.author: delhan
ms.openlocfilehash: d24d1f194b74b9da6a463d3efd741b402102fde3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>如何在 Azure VM 上重置本地 Linux 密码

本文介绍了几种重置本地 Linux 虚拟机 (VM) 密码的方法。 如果用户帐户已过期或者只是想要创建新帐户，则可以使用以下方法来创建新的本地管理员帐户并重新获得对 VM 的访问权限。

## <a name="symptoms"></a>症状

无法登录到 VM 时会收到一条消息，指示所使用的密码不正确。 此外，无法在 Azure 门户上使用 VMAgent 重置密码。 

## <a name="manual-password-reset-procedure"></a>手动密码重置过程

1.  删除 VM 并保留附加的磁盘。

2.  将 OS 驱动器作为数据磁盘附加到同一位置中的另一个临时 VM 中。

3.  在临时 VM 上运行以下 SSH 命令，成为超级用户。


    ~~~~
    sudo su
    ~~~~

4.  运行 fdisk -l，或查看系统日志以查找新附加的磁盘。 找到要装载的驱动器名称。 然后在临时 VM 上，查找相关的日志文件。

    ~~~~
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ~~~~

    下面是 grep 命令的输出示例：

    ~~~~
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ~~~~

5.  创建名为 tempmount 的装入点。

    ~~~~
    mkdir /tempmount
    ~~~~

6.  在该装入点上装载 OS 磁盘。 通常需要装载 sdc1 或 sdc2。 这将取决于断开的计算机磁盘 /etc 目录中的托管分区。

    ~~~~
    mount /dev/sdc1 /tempmount
    ~~~~

7.  请在进行任何更改之前执行备份：

    ~~~~
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ~~~~

8.  重置所需的用户密码：

    ~~~~
    passwd <<USER>> 
    ~~~~

9.  将已修改的文件移动到断开的计算机磁盘上的正确位置。

    ~~~~
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    
10. Go back to the root and unmount the disk.

    ~~~~
    cd / umount /tempmount
    ~~~~

11. Detach the disk from the management portal.

12. Recreate the VM.

## Next steps

* [Troubleshoot Azure VM by attaching OS disk to another Azure VM](http://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: How to delete and re-deploy a VM from VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
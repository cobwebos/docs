---
title: 在 Azure 中创建和上传 SUSE Linux VHD
description: 了解如何创建和上传包含 SUSE Linux 操作系统的 Azure 虚拟硬盘 (VHD)。
author: gbowerman
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2018
ms.author: guybo
ms.openlocfilehash: 5bf26fa096058f5a73d5527c0c6adb1649c9884f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857317"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>为 Azure 准备 SLES 或 openSUSE 虚拟机


本文假定已在虚拟硬盘中安装了 SUSE 或 openSUSE Linux 操作系统。 存在多个用于创建 .vhd 文件的工具，例如 Hyper-V 等虚拟化解决方案。 有关说明，请参阅[安装 Hyper-V 角色和配置虚拟机](https://technet.microsoft.com/library/hh846766.aspx)。

## <a name="sles--opensuse-installation-notes"></a>SLES/openSUSE 安装说明
* 另请参阅[常规 Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)，获取更多有关如何为 Azure 准备 Linux 的提示。
* Azure 不支持 VHDX 格式，仅支持**固定大小的 VHD**。  可使用 Hyper-V 管理器或 convert-vhd cmdlet 将磁盘转换为 VHD 格式。
* 在安装 Linux 系统时，建议使用标准分区而不是 LVM（通常是许多安装的默认值）。 这会避免 LVM 与克隆 VM 发生名称冲突，特别是在 OS 磁盘需要连接到另一台 VM 以进行故障排除的情况下。 如果需要，可以在数据磁盘上使用 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 不要在操作系统磁盘上配置交换分区。 可以配置 Linux 代理，以在临时资源磁盘上创建交换文件。  可以在下面的步骤中找到有关此内容的详细信息。
* Azure 上的所有 VHD 必须已将虚拟大小调整为 1MB。 从原始磁盘转换为 VHD 时，必须确保在转换前原始磁盘大小是 1MB 的倍数。 有关详细信息，请参阅 [Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)。

## <a name="use-suse-studio"></a>使用 SUSE Studio
[SUSE Studio](http://www.susestudio.com) 可以轻松地创建和管理 Azure 和 Hyper-V 的 SLES 和 openSUSE 映像。 这是自定义用户自己的 SUSE 和 openSUSE 映像的推荐方法。

作为构建用户自己的 VHD 的替代方法，SUSE 也会为 [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf) 中的 SLES 发布 BYOS（自带订阅）映像。

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>准备 SUSE Linux Enterprise Server 11 SP4
1. 在 Hyper-V 管理器的中间窗格中，选择虚拟机。
2. 单击“连接”打开虚拟机窗口。 
3. 注册 SUSE Linux Enterprise 系统以允许其下载更新并安装程序包。
4. 使用最新修补程序更新系统：
   
        # sudo zypper update
5. 从 SLES 存储库安装 Azure Linux 代理（SLE11）：
   
        # sudo zypper install python-azure-agent
6. 在 chkconfig 中检查 waagent 是否设置为“on”，如果不是，请启用它以便自动启动：
   
        # sudo chkconfig waagent on
7. 检查 waagent 服务是否正在运行，如果没有，请启动它： 
   
        # sudo service waagent start
8. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 为此，请在文本编辑器中打开“/boot/grub/menu.lst”，并确保默认内核包含以下参数：
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    这会确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。
9. 确认 /boot/grub/menu.lst 和 /etc/fstab 是否都使用其 UUID (by-uuid) 而不是磁盘 ID (by-id) 引用磁盘。 
   
    获取磁盘 UUID
   
        # ls /dev/disk/by-uuid/
   
    如果使用了 /dev/disk/by-id/，请使用正确的 uuid 值更新 /boot/grub/menu.lst 和 /etc/fstab
   
    更改之前
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    更改之后
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. 修改 udev 规则，以避免产生以太网接口的静态规则。 在 Microsoft Azure 或 Hyper-V 中克隆虚拟机时，这些规则可能会引发问题：
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. 建议编辑文件“/etc/sysconfig/network/dhcp”，并将 `DHCLIENT_SET_HOSTNAME` 参数更改为以下值：
    
     DHCLIENT_SET_HOSTNAME="no"
12. 在“/etc/sudoers”中，注释掉或删除以下行（如果存在）：
    
    ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```
13. 请确保已安装 SSH 服务器且已将其配置为在引导时启动。  这通常是默认设置。
14. 不要在 OS 磁盘上创建交换空间。
    
    Azure Linux 代理可使用在 Azure 上设置后附加到虚拟机的本地资源磁盘自动配置交换空间。 请注意，本地资源磁盘是*临时*磁盘，并可能在取消预配 VM 时被清空。 在安装 Azure Linux 代理（请参见前一步骤）后，相应地在 /etc/waagent.conf 中修改以下参数：
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## 注意：将此项设置为所需的内容。
15. 运行以下命令可取消对虚拟机的预配并且对其进行准备以便在 Azure 上进行预配：
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. 单击 "**操作-> 关闭**" "Hyper-v 管理器"。 Linux VHD 现已准备好上传到 Azure。

---
## <a name="prepare-opensuse-131"></a>准备 openSUSE 13.1+
1. 在 Hyper-V 管理器的中间窗格中，选择虚拟机。
2. 单击 **“连接”** 以打开虚拟机窗口。
3. 在 shell 上，运行命令“`zypper lr`”。 如果此命令返回了类似于下面的输出，则表示已按预期配置了存储库 - 不需要进行任何调整（请注意版本号可能有所不同）：
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    如果该命令返回“未定义存储库...”，则使用以下命令来添加这些存储库：
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    然后，可以通过再次运行命令“`zypper lr`”验证已添加存储库。 如果未启用某个相关的更新存储库，请使用以下命令启用该存储库：
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. 将内核更新为可用的最新版本：
   
        # sudo zypper up kernel-default
   
    或使用所有最新修补程序更新系统：
   
        # sudo zypper update
5. 安装 Azure Linux 代理。
   
        # sudo zypper install WALinuxAgent
6. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 为此，请在文本编辑器中打开“/boot/grub/menu.lst”，并确保默认内核包含以下参数：
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   这会确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。 此外，从内核引导行删除以下参数（如果它们存在）：
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. 建议编辑文件“/etc/sysconfig/network/dhcp”，并将 `DHCLIENT_SET_HOSTNAME` 参数更改为以下值：
   
     DHCLIENT_SET_HOSTNAME="no"
8. **重要提示：** 在“/etc/sudoers”中，注释掉或删除以下行（如果存在）：
     
     ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```

9. 请确保已安装 SSH 服务器且已将其配置为在引导时启动。  这通常是默认设置。
10. 不要在 OS 磁盘上创建交换空间。
    
    Azure Linux 代理可使用在 Azure 上设置后附加到虚拟机的本地资源磁盘自动配置交换空间。 请注意，本地资源磁盘是*临时*磁盘，并可能在取消预配 VM 时被清空。 在安装 Azure Linux 代理（请参见前一步骤）后，相应地在 /etc/waagent.conf 中修改以下参数：
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## 注意：将此项设置为所需的内容。
11. 运行以下命令可取消对虚拟机的预配并且对其进行准备以便在 Azure 上进行预配：
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. 确保在启动时运行 Azure Linux 代理：
    
        # sudo systemctl enable waagent.service
13. 单击 "**操作-> 关闭**" "Hyper-v 管理器"。 Linux VHD 现已准备好上传到 Azure。

## <a name="next-steps"></a>后续步骤
现在，可以使用 SUSE Linux 虚拟硬盘在 Azure 中创建新的 Azure 虚拟机了。 如果是首次将 .vhd 文件上传到 Azure，请参阅[从自定义磁盘创建 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。

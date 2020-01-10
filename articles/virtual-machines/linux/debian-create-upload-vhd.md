---
title: 在 Azure 中准备 Debian Linux VHD
description: 了解如何创建 Debian VHD 映像，以便在 Azure 中进行部署。
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/13/2018
ms.author: mimckitt
ms.openlocfilehash: 3cf6a4a98451a36826cadf84b9be8e3ea63efea7
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750143"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>为 Azure 准备 Debian VHD
## <a name="prerequisites"></a>必备组件
本部分假设已经将从 [Debian 网站](https://www.debian.org/distrib/)下载的 .iso 文件中的 Debian Linux 操作系统安装到虚拟硬盘。 可以使用多种现有的工具来创建 .vhd 文件；Hyper-V 只是一个示例。 有关 Hyper-V 的使用说明，请参阅[安装 Hyper-V 角色和配置虚拟机](https://technet.microsoft.com/library/hh846766.aspx)。

## <a name="installation-notes"></a>安装说明
* 另请参阅[常规 Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)，获取更多有关如何为 Azure 准备 Linux 的提示。
* Azure 不支持更新的 VHDX 格式。 可使用 Hyper-V 管理器或 **convert-vhd** cmdlet 将磁盘转换为 VHD 格式。
* 在安装 Linux 系统时，建议使用标准分区而不是 LVM（通常是许多安装的默认值）。 这会避免 LVM 与克隆 VM 发生名称冲突，特别是在 OS 磁盘需要连接到另一台 VM 以进行故障排除的情况下。 如果需要，可以在数据磁盘上使用 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 不要在操作系统磁盘上配置交换分区。 可以配置 Azure Linux 代理，以在临时资源磁盘上创建交换文件。 可以在下面的步骤中找到更多信息。
* Azure 上的所有 VHD 必须已将虚拟大小调整为 1MB。 从原始磁盘转换为 VHD 时，必须确保在转换前原始磁盘大小是 1MB 的倍数。 有关详细信息，请参阅 [Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)。

## <a name="use-azure-manage-to-create-debian-vhds"></a>使用 Azure-Manage 来创建 Debian VHD
有工具可用于生成适用于 Azure 的 Debian VHD，如来自 [credativ](https://www.credativ.com/) 的 [azure-manage](https://github.com/credativ/azure-manage) 脚本。 这是建议的方法，而不是从头开始创建映像。 例如，要创建 Debian 8 VHD，运行以下命令来下载 `azure-manage` 实用程序（以及依赖项），并运行 `azure_build_image` 脚本：

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>手动准备 Debian VHD
1. 在 Hyper-V 管理器中，选择虚拟机。
2. 单击“连接”打开该虚拟机的控制台窗口。
3. 如果用 ISO 安装了 OS，请注释掉与 `/etc/apt/source.list` 中的“`deb cdrom`”相关的任何行。

4. 编辑 `/etc/default/grub` 文件并按如下方式修改 **GRUB_CMDLINE_LINUX** 参数，包含用于 Azure 的其他内核参数。
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. 重新生成 grub 并运行：

        # sudo update-grub

6. 将 Debian 的 Azure 存储库添加到 Debian 8 或 9 的 /etc/apt/sources.list 中：

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Stretch"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. 安装 Azure Linux 代理：
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. 对于 Debian 9+，建议将新的 Debian Cloud 内核与 Azure 中的 VM 配合使用。 若要安装此新内核，首先使用以下内容创建名为 /etc/apt/preferences.d/linux.pref 的文件：
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    然后运行“sudo apt-get install linux-image-amd64”，以安装该新内核。

9. 取消对虚拟机的预配并对其进行准备，以便在 Azure 上进行预配并运行：
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. 在 Hyper-V 管理器中单击“操作”->“关闭”。 Linux VHD 现已准备好上传到 Azure。

## <a name="next-steps"></a>后续步骤
现在，可以使用 Debian 虚拟硬盘在 Azure 中创建新的 Azure 虚拟机了。 如果是首次将 .vhd 文件上传到 Azure，请参阅[从自定义磁盘创建 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。


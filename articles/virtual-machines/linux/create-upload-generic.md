---
title: "在 Azure 中创建和上传 Linux VHD"
description: "了解如何创建和上传包含 Linux 操作系统的 Azure 虚拟硬盘 (VHD)。"
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: ae266b0fc78083250c5d14ff2978cd59d394b202
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="information-for-non-endorsed-distributions"></a>有关未认可分发版的信息
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

仅当使用某个[认可的分发版](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)时，Azure 平台 SLA 才适用于运行 Linux OS 的虚拟机。 对于这些认可的分发版，将在 Azure Marketplace 中提供具有所需配置的 Linux 映像。

* [Azure 上的 Linux - 认可的分发版](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Microsoft Azure 中对 Linux 映像的支持](https://support.microsoft.com/kb/2941892)

所有正在 Azure 上运行的分发都需要满足多个先决条件才能在平台上正常运行。  本文并未涵盖所有信息，因为每个分发都是不同的；即使你满足以下所有条件，也可能仍需显著调整 Linux 系统以确保其在平台上正常运行。

正是出于这个原因，建议尽可能地从某个 [Azure 上的 Linux - 认可的分发版](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)开始操作。 以下文章指导完成如何准备 Azure 上支持的各种认可的 Linux 分发：

* **[基于 CentOS 的分发](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES 和 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

本文的其余部分将重点介绍有关在 Azure 上运行 Linux 分发的一般准则。

## <a name="general-linux-installation-notes"></a>常规 Linux 安装说明
* Azure 不支持 VHDX 格式，仅支持**固定大小的 VHD**。  可使用 Hyper-V 管理器或 convert-vhd cmdlet 将磁盘转换为 VHD 格式。 如果使用 VirtualBox，则意味着选择的是“固定大小”，而不是在创建磁盘时动态分配默认大小。
* Azure 仅支持第 1 代虚拟机。 可以将第 1 代虚拟机从 VHDX 转换为 VHD 文件格式，从动态扩展磁盘转换为固定大小磁盘。 但无法更改虚拟机的代次。 有关详细信息，请参阅[是否应在 Hyper-V 中创建第 1 代或第 2 代虚拟机？](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* VHD 允许的最大大小为 1,023 GB。
* 在安装 Linux 系统时，*建议*使用标准分区而不是 LVM（通常是许多安装的默认值）。 这会避免 LVM 与克隆 VM 发生名称冲突，特别是在 OS 磁盘需要连接到另一台相同的 VM 进行故障排除的情况下。 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 可以在数据磁盘上使用。
* 需要装载 UDF 文件系统的内核支持。 在 Azure 上首次启动时，预配配置将通过附加到来宾的 UDF 格式媒体传递到 Linux VM。 Azure Linux 代理必须能够装载 UDF 文件系统才能读取其配置和预配 VM。
* 低于 2.6.37 的 Linux 内核版本不支持具有更大 VM 大小的 Hyper-V 上的 NUMA。 此问题主要影响使用上游 Red Hat 2.6.32 内核的旧分发版，在 RHEL 6.6 (kernel-2.6.32-504) 中已解决。 运行版本低于 2.6.37 的自定义内核的系统，或者版本低于 2.6.32-504 的基于 RHEL 的内核必须在 grub.conf 中的内核命令行上设置启动参数 `numa=off`。 有关详细信息，请参阅 Red Hat [KB 436883](https://access.redhat.com/solutions/436883)。
* 不要在操作系统磁盘上配置交换分区。 可以配置 Linux 代理，以在临时资源磁盘上创建交换文件。  可以在下面的步骤中找到有关此内容的详细信息。
* 所有 VHD 的大小必须是 1 MB 的倍数。

### <a name="installing-kernel-modules-without-hyper-v"></a>安装无 Hyper-V 的内核模块
Azure 在 Hyper-V 虚拟机监控程序上运行，因此 Linux 需要安装某些内核模块才能在 Azure 中运行。 如果具有在 Hyper-V 外部创建的虚拟机，Linux 安装程序可能无法在初始 ramdisk（initrd 或 initramfs）中包含 Hyper-V 驱动程序，除非它检测到它正在运行 Hyper-V 环境。 使用不同虚拟化系统（即 Virtualbox、KVM 等）来准备 Linux 映像时，可能需要重新生成 initrd 以确保至少 `hv_vmbus` 和 `hv_storvsc` 内核模块可在初始 ramdisk 上使用。  至少在基于上游 Red Hat 分发的系统上这是一个已知问题。

重新生成 initrd 或 initramfs 映像的机制可能会因分发而有所不同。 请查阅分发的文档或相应过程的支持。  下面是有关如何使用 `mkinitrd` 实用工具重新生成 initrd 的示例：

首先，备份现有 initrd 映像：

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

接下来，使用 `hv_vmbus` 和 `hv_storvsc` 内核模块重新生成 initrd：

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>调整 VHD 大小
Azure 上的 VHD 映像必须已将虚拟大小调整为 1MB。  通常情况下，使用 Hyper-V 创建的 VHD 应已正确调整。  如果未正确调整 VHD，则尝试基于 VHD 创建*映像*时，可能会收到如下错误消息：

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

若要修正此问题，可使用 Hyper-V 管理器控制台或 [Resize-VHD](http://technet.microsoft.com/library/hh848535.aspx) Powershell cmdlet 调整 VM 大小。  如果未在 Windows 环境中运行，则建议使用 qemu-img 转换（如果需要）并调整 VHD 大小。

> [!NOTE]
> qemu-img 版本（>=2.2.1）中有一个已知 bug，会导致 VHD 格式不正确。 QEMU 2.6 中已修复此问题。 建议使用 qemu-img 2.2.0 或更低版本，或者更新到 2.6 或更高版本。 参考：https://bugs.launchpad.net/qemu/+bug/1490611。
> 
> 

1. 直接使用工具（如 `qemu-img` 或 `vbox-manage`）调整 VHD 大小可能会生成无法启动的 VHD。  因此，建议先将 VHD 转换为 RAW 磁盘映像。  如果已将 VM 映像创建为 RAW 磁盘映像（对于 KVM 等某些虚拟机监控程序，这是默认设置），则可以跳过此步骤：
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. 计算磁盘映像所需的大小，以确保虚拟大小已调整为 1MB。  以下 bash shell 脚本可以对此有帮助。  此脚本使用“`qemu-img info`”来确定磁盘映像的虚拟大小，并将大小计算到下个 1MB：
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. 使用 $rounded_size 调整原始磁盘大小，如上述脚本所设置：
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. 现在，将 RAW 磁盘重新转换为固定大小 VHD：
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   或者，对于 qemu 版本 **2.6+**，包括 `force_size` 选项：

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Linux 内核要求
Hyper-V 和 Azure 的 Linux 集成服务 (LIS) 驱动程序会直接影响上游 Linux 内核。 包括最新 Linux 内核版本（即 3.x）在内的许多分发已提供这些驱动程序，或以其他方式为其内核提供了这些驱动程序的向后移植版本。  这些驱动程序会不断地在上游内核中使用新的修补程序和功能进行更新，因此，如果可能，请运行[认可的分发版](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)以包含这些修补程序和更新。

如果正在运行 Red Hat Enterprise Linux 版本 **6.0-6.3** 的一个变体，需要为 Hyper-V 安装最新的 LIS 驱动程序。 可[在此处](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)找到这些驱动程序。 从 RHEL **6.4+**（和派生产品）开始，LIS 驱动程序已包含在内核中，因此，无需其他安装包即在 Azure 上运行这些系统。

如果需要自定义内核，建议使用较新的内核版本（即 **3.8+**）。 对于这些分发或维护自己的内核的供应商，将需要执行一些操作，以便定期将 LIS 驱动程序从上游内核向后移植到自定义内核。  即使你已运行相对较新的内核版本，也强烈建议跟踪 LIS 驱动程序中的任何上游修复，并根据需要向后移植这些修复。 LIS 驱动程序源文件的位置可在 Linux 内核源树中的 [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) 文件中找到：

    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/

至少，缺少以下修补程序已知会在 Azure 上导致问题，因此必须在内核中包含这些修补程序。 对于所有分发而言，此列表绝并非详尽或完整：

* [ata_piix：默认情况下，将磁盘交由 Hyper-V 驱动程序处理](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc：解释 RESET 路径中传输中的数据包](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc：避免使用 WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc：对 RAID 和虚拟主机适配器驱动程序禁用 WRITE SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc：NULL 指针取消引用修补程序](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc：环形缓冲区故障可能会导致 I/O 冻结](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs：防止执行两次 __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux 代理
[Azure Linux 代理](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (waagent) 是在 Azure 中正确设置 Linux 虚拟机所必需的。 可以在 [Linux 代理 GitHub 存储库](https://github.com/Azure/WALinuxAgent)中获取最新版本、文件问题或提交拉取请求。

* 根据 Apache 2.0 许可证发布 Linux 代理。 许多分发已为该代理提供 RPM 或 deb 包，因此，在某些情况下不费吹灰之力即可安装和更新该代理。
* Azure Linux 代理需要 Python v2.6 以上版本。
* 该代理还需要 python-pyasn1 模块。 大多数分发提供此模块作为可以安装的单独包。
* 在某些情况下，Azure Linux 代理可能与 NetworkManager 不兼容。 分发提供的许多 RPM/Deb 包会将 NetworkManager 配置为与 waagent 包冲突，因此安装 Linux 代理包时会卸载 NetworkManager。
* Azure Linux 代理必须是上面的最低支持版本，请参阅此文章中的[详细信息](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。

## <a name="general-linux-system-requirements"></a>一般 Linux 系统要求

* 修改 GRUB 或 GRUB2 中的内核引导行，以便包含以下参数。 这还将确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题：
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    这还将确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。
  
    除此之外，建议*删除*以下参数（如果存在）：
  
        rhgb quiet crashkernel=auto
  
    图形引导和无人参与引导不适用于云环境，在该环境中我们想要将所有日志都发送到串行端口。 根据需要可以配置 `crashkernel` 选项，但请注意此参数会使虚拟机中的可用内存量减少 128MB 或更多，这在较小的虚拟机上可能会出现问题。

* 安装 Azure Linux 代理
  
    Azure Linux 代理是在 Azure 上设置 Linux 映像所必需的。  许多分发将该代理提供为 RPM 或 Deb 包（该包通常称为“WALinuxAgent”或“walinuxagent”）。  还可以按照 [Linux 代理指南](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的步骤手动安装该代理。

* 请确保已安装 SSH 服务器且已将其配置为在引导时启动。  这通常是默认设置。

* 不要在操作系统磁盘上创建交换空间
  
    Azure Linux 代理可使用在 Azure 上设置后附加到虚拟机的本地资源磁盘自动配置交换空间。 请注意，本地资源磁盘是*临时*磁盘，并可能在取消预配 VM 时被清空。 在安装 Azure Linux 代理（请参见前一步骤）后，相应地在 /etc/waagent.conf 中修改以下参数：
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* 最后一步，请运行以下命令以取消设置虚拟机：
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > 运行“waagent -force -deprovision”之后，在 Virtualbox 上可能看到以下错误：`[Errno 5] Input/output error`。 此错误消息并不关键，可以忽略。
  > 
  > 

* 然后，需要关闭虚拟机并将 VHD 上传到 Azure。


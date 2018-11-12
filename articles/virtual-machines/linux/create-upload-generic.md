---
title: 在 Azure 中创建和上传 Linux VHD
description: 了解如何创建和上传包含 Linux 操作系统的 Azure 虚拟硬盘 (VHD)。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: szark
ms.openlocfilehash: 3aa2803550c445e0b30ff998cf3adb779515e487
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235966"
---
# <a name="information-for-non-endorsed-distributions"></a>有关未认可分发版的信息
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

仅当使用某个[认可的分发版](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)时，Azure 平台 SLA 才适用于运行 Linux OS 的虚拟机。 对于这些认可的分发版，Azure 市场中提供了预配置的 Linux 映像。

* [Azure 上的 Linux - 认可的分发版](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Microsoft Azure 中对 Linux 映像的支持](https://support.microsoft.com/kb/2941892)

Azure 上运行的所有分发版都要满足一些先决条件。 本文的内容并不全面，因为每个分发版不同。 即使满足以下所有条件，但仍可能需要大幅调整 Linux 系统才能让其正常运行。

我们建议从某个 [Azure 上的 Linux 认可的分发版](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)着手。 以下文章介绍了如何准备 Azure 上支持的各种认可的 Linux 分发版：

* **[基于 CentOS 的分发](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES 和 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

本文重点介绍有关在 Azure 上运行 Linux 分发版的一般准则。

## <a name="general-linux-installation-notes"></a>常规 Linux 安装说明
* Azure 不支持 Hyper-V 虚拟硬盘 (VHDX) 格式，仅支持固定大小的 VHD。  可使用 Hyper-V 管理器或 [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) cmdlet 将磁盘转换为 VHD 格式。 如果使用 VirtualBox，请在创建磁盘时选择“固定大小”，而不要选择默认（动态分配的）大小。
* Azure 仅支持第 1 代虚拟机。 可将第 1 代虚拟机从 VHDX 转换为 VHD 文件格式，从动态扩展磁盘转换为固定大小磁盘。 但无法更改虚拟机的代次。 有关详细信息，请参阅[是否应在 Hyper-V 中创建第 1 代或第 2 代虚拟机？](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* VHD 允许的最大大小为 1,023 GB。
* 在安装 Linux 系统时，建议使用标准分区而不是逻辑卷管理器 (LVM)，这是许多安装的默认设置。 使用标准分区可避免 LVM 名称与克隆的 VM 发生冲突，特别是在 OS 磁盘曾经连接到另一台相同的 VM 进行故障排除的情况下。 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 可以在数据磁盘上使用。
* 需要装载 UDF 文件系统的内核支持。 在 Azure 上首次启动时，预配配置将使用附加到来宾的 UDF 格式媒体传递到 Linux VM。 Azure Linux 代理必须装载 UDF 文件系统才能读取其配置和预配 VM。
* 低于 2.6.37 的 Linux 内核版本不支持具有更大 VM 大小的 Hyper-V 上的 NUMA。 此问题主要影响使用上游 Red Hat 2.6.32 内核的旧分发版，在 Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504) 中已解决。 运行版本低于 2.6.37 的自定义内核的系统，或者版本低于 2.6.32-504 的基于 RHEL 的内核必须在 grub.conf 中的内核命令行上设置启动参数 `numa=off`。 有关详细信息，请参阅 [Red Hat KB 436883](https://access.redhat.com/solutions/436883)。
* 不要在 OS 磁盘上配置交换分区。 可根据以下步骤中所述配置 Linux 代理，并在临时资源磁盘上创建交换文件。
* Azure 上的所有 VHD 必须已将虚拟大小调整为 1 MB。 从原始磁盘转换为 VHD 时，必须根据以下步骤中所述，确保在转换前原始磁盘大小是 1 MB 的倍数。

### <a name="installing-kernel-modules-without-hyper-v"></a>安装无 Hyper-V 的内核模块
Azure 在 Hyper-V 虚拟机监控程序上运行，因此 Linux 需要某些内核模块才能在 Azure 中运行。 如果具有在 Hyper-V 外部创建的虚拟机，Linux 安装程序可能无法在初始 ramdisk（initrd 或 initramfs）中包含 Hyper-V 驱动程序，除非 VM 检测到它正在 Hyper-V 环境中运行。 使用不同的虚拟化系统（例如 Virtualbox、KVM 等）来准备 Linux 映像时，可能需要重新生成 initrd，以便至少 hv_vmbus 和 hv_storvsc 内核模块可在初始 ramdisk 上使用。  在基于上游 Red Hat 分发版的系统上（可能还包括其他系统），这是一个已知问题。

重新生成 initrd 或 initramfs 映像的机制可能会因分发而有所不同。 查阅分发的文档或相应过程的支持。  以下示例演示如何使用 `mkinitrd` 实用工具重新生成 initrd：

1. 备份现有 initrd 映像：

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. 使用 hv_vmbus 和 hv_storvsc 内核模块重新生成 initrd：

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>调整 VHD 大小
Azure 上的 VHD 映像必须已将虚拟大小调整为 1MB。  通常情况下，使用 Hyper-V 创建的 VHD 已正确调整。  如果未正确调整 VHD，在尝试基于 VHD 创建映像时，可能会收到如下错误消息：

* VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd 的虚拟大小为 21475270656 字节，这是不受支持的。 大小必须是整数（以 MB 为单位）。

在这种情况下，可使用 Hyper-V 管理器控制台或 [Resize-VHD](https://technet.microsoft.com/library/hh848535.aspx) PowerShell cmdlet 调整 VM 大小。  如果不是在 Windows 环境中运行，我们建议使用 `qemu-img` 转换（如果需要）并调整 VHD 大小。

> [!NOTE]
> 2.2.1 或更高版本的 qemu-img 存在一个[已知的 bug](https://bugs.launchpad.net/qemu/+bug/1490611)，会导致 VHD 格式不正确。 QEMU 2.6 中已修复此问题。 我们建议使用 `qemu-img` 2.2.0 或更低版本，或者 2.6 或更高版本。
> 

1. 直接使用工具（如 `qemu-img` 或 `vbox-manage`）调整 VHD 大小可能会生成无法启动的 VHD。  我们建议先将 VHD 转换为 RAW 磁盘映像。  如果已将 VM 映像创建为 RAW 磁盘映像（对于 KVM 等某些虚拟机监控程序，这是默认设置），则可以跳过此步骤。
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. 计算磁盘映像所需的大小，使虚拟大小调整为 1MB。  以下 bash shell 脚本使用 `qemu-img info` 来确定磁盘映像的虚拟大小，然后将大小计算到下个 1 MB。

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$((($size/$MB + 1)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. 使用上面设置的 `$rounded_size` 调整原始磁盘大小。

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. 现在，将 RAW 磁盘重新转换为固定大小 VHD。

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   或者，对 qemu 版本 2.6+ 包含 `force_size` 选项。

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Linux 内核要求

Hyper-V 和 Azure 的 Linux 集成服务 (LIS) 驱动程序会直接影响上游 Linux 内核。 包括最新 Linux 内核版本（例如 3.x）在内的许多分发版已提供这些驱动程序，或以其他方式为其内核提供了这些驱动程序的向后移植版本。  这些驱动程序会不断地在上游内核中使用新的修补程序和功能进行更新，因此，如果可能，建议运行包含这些修补程序和更新的[认可的分发版](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

如果你正在运行 Red Hat Enterprise Linux 版本 6.0 到 6.3 的一个变体，需要安装[适用于 Hyper-V 的最新 LIS 驱动程序](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)。 从 RHEL 6.4+（和派生产品）开始，LIS 驱动程序已包含在内核中，因此，无需其他安装包。

如果需要自定义内核，我们建议使用最新的内核版本（例如 3.8+）。 对于维护自己内核的分发版或供应商，需要定期将 LIS 驱动程序从上游内核向后移植到自定义内核。  即使已运行相对较新的内核版本，我们也强烈建议跟踪 LIS 驱动程序中的任何上游修复，并根据需要向后移植这些修复。 LIS 驱动程序源文件的位置在 Linux 内核源树中的 [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) 文件中指定：
```
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
```
必须在内核中包含以下修补程序。 此列表并不完整，并未包括所有分发版。

* [ata_piix：默认情况下，将磁盘交由 Hyper-V 驱动程序处理](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc：解释 RESET 路径中传输中的数据包](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc：避免使用 WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc：对 RAID 和虚拟主机适配器驱动程序禁用 WRITE SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc：NULL 指针取消引用修补程序](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc：环形缓冲区故障可能会导致 I/O 冻结](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs：防止执行两次 __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux 代理
[Azure Linux 代理](../extensions/agent-linux.md) `waagent` 在 Azure 中预配 Linux 虚拟机。 可以在 [Linux 代理 GitHub 存储库](https://github.com/Azure/WALinuxAgent)中获取最新版本、文件问题或提交拉取请求。

* 根据 Apache 2.0 许可证发布 Linux 代理。 许多分发版已经为该代理提供 RPM 或 deb 包，可以轻松安装和更新这些包。
* Azure Linux 代理需要 Python v2.6 以上版本。
* 该代理还需要 python-pyasn1 模块。 大多数分发版提供此模块作为可安装的单独包。
* 在某些情况下，Azure Linux 代理可能与 NetworkManager 不兼容。 分发版提供的许多 RPM/Deb 包所配置的 NetworkManager 与 waagent 包相冲突。 在这种情况下，它会在你安装 Linux 代理包时卸载 NetworkManager。
* Azure Linux 代理必须至少是[支持的最低版本](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。

## <a name="general-linux-system-requirements"></a>一般 Linux 系统要求

1. 修改 GRUB 或 GRUB2 中的内核引导行并包含以下参数，以便将所有控制台消息发送到第一个串行端口。 这些消息可以协助 Azure 支持人员调试任何问题。
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    我们还建议删除以下参数（如果存在）。
    ```  
    rhgb quiet crashkernel=auto
    ```
    图形引导和静默引导不适用于云环境，在该环境中我们希望将所有日志发送到串行端口。 可根据需要配置 `crashkernel` 选项，但请注意此参数会使 VM 中的可用内存量至少减少 128 MB，这对于较小的 VM 而言可能是个问题。

2. 安装 Azure Linux 代理。
  
    Azure Linux 代理是在 Azure 上设置 Linux 映像所必需的。  许多分发版将该代理提供为 RPM 或 Deb 包（该包通常称为 WALinuxAgent 或 walinuxagent）。  还可以按照 [Linux 代理指南](../extensions/agent-linux.md)中的步骤手动安装该代理。

3. 确保已安装 SSH 服务器且已将其配置为在引导时启动。  此配置通常是默认值。

4. 不要在 OS 磁盘上创建交换空间。
  
    Azure Linux 代理可使用在 Azure 上设置后附加到虚拟机的本地资源磁盘自动配置交换空间。 本地资源磁盘是临时磁盘，并可能在取消预配 VM 时被清空。 安装 Azure Linux 代理（上述步骤 2）后，根据需要在 /etc/waagent.conf 中修改以下参数。
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
* 运行以下命令以取消预配虚拟机。
  
    ```
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```  
  > [!NOTE]
  > 运行 `waagent -force -deprovision` 之后，Virtualbox 上可能会出现以下错误，指出 `[Errno 5] Input/output error`。 此错误消息并不关键，可以忽略。

* 关闭虚拟机并将 VHD 上传到 Azure。


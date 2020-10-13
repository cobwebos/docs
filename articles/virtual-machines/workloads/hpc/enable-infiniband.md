---
title: 在 HPC Vm 上启用 InifinBand-Azure 虚拟机 |Microsoft Docs
description: 了解如何在 Azure HPC Vm 上启用不允许。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0cbfed307cea1bd98bf864046a8c08edb849226a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87797979"
---
# <a name="enable-infiniband"></a>启用 InfiniBand

[支持 RDMA 的](../../sizes-hpc.md#rdma-capable-instances) [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM 通过低延迟的高带宽 InfiniBand 网络进行通信。 此类互连上的 RDMA 功能对于提高分布式节点 HPC 和 AI 工作负载的可伸缩性和性能至关重要。 支持 InfiniBand 的 H 系列和 N 系列 VM 采用非阻塞性胖树进行连接，使用的小直径旨在实现一致的优化 RDMA 性能。

可以通过多种方式在支持的 VM 大小上启用允许。

## <a name="vm-images-with-infiniband-drivers"></a>具有无限驱动程序的 VM 映像
请参阅 [VM 映像](configure.md#vm-images) ，获取 Marketplace 上受支持的 vm 映像的列表，该列表已预先加载用于 sr-iov 或非 sr-iov vm 的不受支持的驱动程序 () 或可使用适当的驱动程序进行配置。
对于启用了 SR-IOV 的支持 [RDMA 的 vm](../../sizes-hpc.md#rdma-capable-instances)， [CentOS-HPC 7.6 版或更高](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) 版本的 vm 映像是入门的最简单方法。
可以使用 [此处的说明](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)，使用适用于 sr-iov 和非 Sr-iov 启用 vm 的适当驱动程序配置 Ubuntu VM 映像。

## <a name="infiniband-driver-vm-extensions"></a>不通过的驱动程序 VM 扩展
在 Linux 上，可以使用 [INFINIBANDDRIVERLINUX VM 扩展](../../extensions/hpc-compute-infiniband-linux.md) 安装 Mellanox OFED 驱动程序，并在启用 Sr-iov 的 H 和 N 系列 vm 上启用不受支持。

在 Windows 上， [INFINIBANDDRIVERWINDOWS VM 扩展](../../extensions/hpc-compute-infiniband-windows.md) 会将 Windows 网络直接 (驱动程序安装在非 sr-iov vm) 或 OFED 驱动程序 (上的驱动程序上，以实现 RDMA 连接) 。 在某些 A8 和 A9 实例的部署中，会自动添加 HpcVmDrivers 扩展。 请注意，不推荐使用 HpcVmDrivers VM 扩展;它不会更新。

若要将 VM 扩展添加到 VM，可以使用 [Azure PowerShell](/powershell/azure/) cmdlet。 有关详细信息，请参阅[虚拟机扩展和功能](../../extensions/overview.md)。 还可使用[经典部署模型](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)中部署的 VM 扩展。

## <a name="manual-installation"></a>手动安装
可以在[启用了 sr-iov](../../sizes-hpc.md#rdma-capable-instances)的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm 上手动安装[ (OFED) 的 Mellanox OpenFabrics 驱动程序](https://www.mellanox.com/products/InfiniBand-VPI-Software)。

### <a name="linux"></a>Linux
可在以下示例中安装 [适用于 Linux 的 OFED 驱动程序](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) 。 尽管此处的示例适用于 RHEL/CentOS，但步骤是一般的，可用于任何兼容的 Linux 操作系统，如 Ubuntu (16.04、18.04 19.04、20.04) 和 SLES (12 SP4 和 15) 。 [Azhpc](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh)存储库中提供了其他发行版的更多示例。 收件箱驱动程序也工作正常，但 Mellanox OFED 驱动程序提供更多功能。

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
对于 Windows，请下载并安装 [适用于 windows 的 MELLANOX OFED 驱动程序](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)。

## <a name="enable-ip-over-infiniband-ib"></a>通过 (IB) 启用 IP
如果打算运行 MPI 作业，则通常不需要 IPoIB。 除非显式使用 MPI library) 的 TCP/IP 通道，否则 MPI 库将使用用于 IB 通信的谓词接口 (。 但是，如果您的应用程序使用 TCP/IP 进行通信，而您希望通过 IB 运行，则可以通过 IB 接口使用 IPoIB。 使用适用于 RHEL/CentOS) 的以下 (命令在上启用 IP。

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>后续步骤

- 详细了解如何在 Vm 上安装各种 [受支持的 MPI 库](setup-mpi.md) 及其最佳配置。
- 查看 [HB 系列概述](hb-series-overview.md)和 [HC 系列概述](hc-series-overview.md)，以了解如何对工作负载进行优化配置以提高性能和可伸缩性。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。

---
title: Azure Linux VM 大小 - HPC | Microsoft Docs
description: 列出 Azure 中适用于 Linux 高性能计算虚拟机的各种大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: jonbeck
ms.openlocfilehash: 441e99b86e9560d47af8ea18a2633e3f37a05e94
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2018
ms.locfileid: "34653835"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>高性能计算虚拟机大小

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

仅支持 Intel MPI 5.x 版本。 更高版本（2017 版和 2018 版）的 Intel MPI 运行时库与 Azure Linux RDMA 驱动程序不兼容。


### <a name="distributions"></a>分发
 
从支持 RDMA 连接的 Azure 市场中的一个映像部署计算密集型 VM：
  
* Ubuntu - Ubuntu Server 16.04 LTS。 在 VM 上配置 RDMA 驱动程序，并注册 Intel 下载 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* SUSE Linux Enterprise Server - SLES 12 SP3 for HPC、SLES 12 SP3 for HPC（高级版）、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC（高级版）。 安装 RDMA 驱动程序，并在 VM 上分发 Intel MPI 包。 运行以下命令安装 MPI：

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **基于 CentOS 的 HPC** - 基于 CentOS 的 6.5 HPC 或更高版本（对于 H 系列，建议使用版本 7.1 或更高版本）。 在 VM 上安装 RDMA 驱动程序和 Intel MPI 5.1。  
 
  > [!NOTE]
  > 在基于 CentOS 的 HPC 映像中，内核更新已在 **yum** 配置文件中禁用。 这是因为 Linux RDMA 驱动程序以 RPM 包的形式分发，如果更新了内核，驱动程序更新可能无法工作。
  > 
 
### <a name="cluster-configuration"></a>群集配置 
    
需要进行额外的系统配置才能在群集 VM 上运行 MPI 作业。 例如，在 VM 群集上，需要在计算节点之间建立信任。 有关典型设置，请参阅 [Set up a Linux RDMA cluster to run MPI applications](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（设置 Linux RDMA 群集以运行 MPI 应用程序）。

### <a name="network-topology-considerations"></a>网络拓扑注意事项
* 在 Azure 中支持 RDMA 的 Linux VM 上，Eth1 保留用于传输 RDMA 网络流量。 不要更改引用此网络的配置文件中的任何 Eth1 设置或任何信息。 Eth0 保留用于传输常规 Azure 网络流量。

* Azure 中的 RDMA 网络保留地址空间 172.16.0.0/16。 


## <a name="using-hpc-pack"></a>使用 HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) 是 Microsoft 的免费 HPC 群集和作业管理解决方案，可以通过它在 Linux 上使用计算密集型实例。 最新版本的 HPC Pack 支持多个 Linux 分发在由 Windows Server 头节点管理的 Azure VM 中部署的计算节点上运行。 使用支持 RDMA 且运行 Intel MPI 的 Linux 计算节点，HPC Pack 可以计划和运行访问 RDMA 网络的 Linux MPI 应用程序。 请参阅 [Get started with Linux compute nodes in an HPC Pack cluster in Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（Azure 的 HPC Pack 群集中的 Linux 计算节点入门）。

## <a name="other-sizes"></a>其他大小
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 若要开始在 Linux 上部署和使用支持 RDMA 的计算密集型大小 VM，请参阅 [Set up a Linux RDMA cluster to run MPI applications](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（设置 Linux RDMA 群集以运行 MPI 应用程序）。

- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。





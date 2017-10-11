---
title: "Azure Linux VM 大小 - HPC | Microsoft Docs"
description: "列出 Azure 中适用于 Linux 高性能计算虚拟机的各种大小。"
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.openlocfilehash: b7a3844ce86b4efac8a4fc3c2540e7b6460873a2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="high-performance-compute-linux-vm-sizes"></a>高性能计算 Linux VM 大小

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>支持 RDMA 的实例
计算密集型实例（H16r、H16mr、A8 和 A9）的子集具有远程直接内存访问 (RDMA) 连接的网络接口。 此接口是对可供其他 VM 大小使用的标准 Azure 网络接口的补充。 
  
此接口允许支持 RDMA 的实例在 InfiniBand 网络上通信，对 H16r 和 H16mr 虚拟机以 FDR 速率运行，对 A8 和 A9 虚拟机以 QDR 速率运行。 这些 RDMA 功能可以提高消息传递接口 (MPI) 应用程序的可伸缩性和性能。

支持 RDMA 的 Linux VM 可以访问 Azure RDMA 网络的要求如下：
 
* 分发版 - 必须从 Azure Marketplace 中支持 RDMA 的 SUSE Linux Enterprise Server (SLES) 或基于 Rogue Wave Software（之前为 OpenLogic） CentOS 的 HPC 映像部署 VM。 以下 Marketplace 映像支持 RDMA 连接：
  
    * SLES 12 SP1 for HPC 或 SLES 12 SP1 for HPC（高级）
    
    * 基于 CentOS 的 7.3 HPC、基于 CentOS 的 7.1 HPC、基于 CentOS 的 6.8 HPC 或基于 CentOS 的 6.5 HPC  
 
        > [!NOTE]
        > 对于 H 系列 VM，建议使用 SLES 12 SP1 for HPC 映像或基于 CentOS 的 7.1 或更高版本的 HPC 映像。
        >
        > 在基于 CentOS 的 HPC 映像中，内核更新已在 **yum** 配置文件中禁用。 这是因为 Linux RDMA 驱动程序以 RPM 包的形式分发，如果更新了内核，驱动程序更新可能无法工作。
        > 
        > 
* **MPI** - Intel MPI Library 5.x
  
    根据选择的 Marketplace 映像，可能需要单独进行 Intel MPI 授权、安装或配置，如下所述： 
  
  * **LES 12 SP1 for HPC 映像** - 已在 VM 上分发 Intel MPI 包。 运行以下命令进行安装：

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **基于 CentOS 的 HPC 映像** - 已安装 Intel MPI 5.1。  
    
    需要进行额外的系统配置才能在群集 VM 上运行 MPI 作业。 例如，在 VM 群集上，需要在计算节点之间建立信任。 有关典型设置，请参阅 [Set up a Linux RDMA cluster to run MPI applications](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（设置 Linux RDMA 群集以运行 MPI 应用程序）

### <a name="network-topology-considerations"></a>网络拓扑注意事项
* 在 Azure 中支持 RDMA 的 Linux VM 上，Eth1 保留用于传输 RDMA 网络流量。 不要更改引用此网络的配置文件中的任何 Eth1 设置或任何信息。 Eth0 保留用于传输常规 Azure 网络流量。

* 在 Azure 中，不支持基于 InfiniBand (IB) 的 IP。 仅支持 RDMA over IB。

## <a name="using-hpc-pack"></a>使用 HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) 是 Microsoft 的免费 HPC 群集和作业管理解决方案，可以通过它在 Linux 上使用计算密集型实例。 最新版本的 HPC Pack 支持多个 Linux 分发在由 Windows Server 头节点管理的 Azure VM 中部署的计算节点上运行。 使用支持 RDMA 且运行 Intel MPI 的 Linux 计算节点，HPC Pack 可以计划和运行访问 RDMA 网络的 Linux MPI 应用程序。 请参阅 [Get started with Linux compute nodes in an HPC Pack cluster in Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（Azure 的 HPC Pack 群集中的 Linux 计算节点入门）。

## <a name="other-sizes"></a>其他大小
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>后续步骤

- 若要开始在 Linux 上部署和使用支持 RDMA 的计算密集型大小 VM，请参阅 [Set up a Linux RDMA cluster to run MPI applications](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（设置 Linux RDMA 群集以运行 MPI 应用程序）。

- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。





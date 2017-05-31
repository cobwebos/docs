---
title: "关于 Linux 上的计算密集型 VM| Microsoft Docs"
description: "了解有关使用适用于 Linux VM 的 H 系列和 A8、A9、A10 与 A11 计算密集型实例的背景信息和注意事项。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 16cf6e2d-f401-4b22-af8c-9a337179f5f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a2307f7055966ec7146b5da0b4daf1ad469abe2b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017

---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-linux"></a>关于适用于 Linux 的 H 系列和计算密集型 A 系列 VM
本文提供有关使用较新 Azure H 系列和早期 A8、A9、A10 与 A11 大小（也称*计算密集型*实例）的背景信息及某些注意事项。 本文重点介绍如何对 Linux VM 使用这些大小。 还可以将其用于 [Windows VM](../windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

有关基本规范、存储容量和磁盘详细信息，请参阅[虚拟机的大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>RDMA 网络访问权限
可以创建支持 RDMA 的 Linux VM 群集来运行以下受支持的 Linux HPC 分发版和受支持的 MPI 实现之一，以利用 Azure RDMA 网络。 有关部署选项和示例配置步骤，请参阅 [Set up a Linux RDMA cluster to run MPI applications](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（设置 Linux RDMA 群集以运行 MPI 应用程序）。

* 分发版 - 必须从 Azure 应用商店中支持 RDMA 的 SUSE Linux Enterprise Server (SLES) 或基于 Rogue Wave Software（之前为 OpenLogic） CentOS 的 HPC 映像部署 VM。 以下应用商店映像支持 RDMA 连接：
  
    * SLES 12 SP1 for HPC、SLES 12 SP1 for HPC（高级）
    
    * 基于 CentOS 的 7.1 HPC、基于 CentOS 的 6.5 HPC  
 
        > [!NOTE]
        > 对于 H 系列 VM，建议使用 SLES 12 SP1 for HPC 映像或基于 CentOS 的 7.1 HPC。
        >
        > 在基于 CentOS 的 HPC 映像中，内核更新已在 **yum** 配置文件中禁用。 这是因为 Linux RDMA 驱动程序以 RPM 包的形式分发，如果更新了内核，驱动程序更新可能无法工作。
        > 
        > 
* **MPI** - Intel MPI Library 5.x
  
    根据选择的应用商店映像，可能需要单独进行 Intel MPI 授权、安装或配置，如下所述： 
  
  * **LES 12 SP1 for HPC 映像** - 已在 VM 上分发 Intel MPI 包。 运行以下命令进行安装：

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **基于 CentOS 的 HPC 映像** - 已安装 Intel MPI 5.1。  
    
    需要进行额外的系统配置才能在群集 VM 上运行 MPI 作业。 例如，在 VM 群集上，需要在计算节点之间建立信任。 有关典型设置，请参阅 [Set up a Linux RDMA cluster to run MPI applications](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（设置 Linux RDMA 群集以运行 MPI 应用程序）。

## <a name="considerations-for-hpc-pack-and-linux"></a>HPC Pack 和 Linux 的注意事项
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) 是 Microsoft 的免费 HPC 群集和作业管理解决方案，提供一个选项让你在 Linux 上使用计算密集型实例。 最新版本的 HPC Pack 支持多个 Linux 分发在由 Windows Server 头节点管理的 Azure VM 中部署的计算节点上运行。 使用支持 RDMA 且运行 Intel MPI 的 Linux 计算节点，HPC Pack 可以计划和运行访问 RDMA 网络的 Linux MPI 应用程序。 若要开始使用，请参阅 [Get started with Linux compute nodes in an HPC Pack cluster in Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（Azure 的 HPC Pack 群集中的 Linux 计算节点入门）。

## <a name="network-topology-considerations"></a>网络拓扑注意事项
* 在 Azure 中支持 RDMA 的 Linux VM 上，Eth1 保留用于传输 RDMA 网络流量。 不要更改引用此网络的配置文件中的任何 Eth1 设置或任何信息。 Eth0 保留用于传输常规 Azure 网络流量。
* 在 Azure 中，不支持基于 InfiniBand (IB) 的 IP。 仅支持 RDMA over IB。



## <a name="next-steps"></a>后续步骤
* 有关计算密集型大小的可用性和定价详细信息，请参阅 [Virtual Machines pricing](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)（虚拟机定价）。
* 有关 VM 存储容量和磁盘详细信息，请参阅 [Sizes for virtual machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（虚拟机大小）。
* 若要开始在 Linux 上部署和使用支持 RDMA 的计算密集型大小 VM，请参阅 [Set up a Linux RDMA cluster to run MPI applications](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（设置 Linux RDMA 群集以运行 MPI 应用程序）。



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
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 70dca655d5300fcd34b4198093e136f6a971963b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344483"
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
 
### <a name="cluster-configuration-options"></a>群集配置选项

Azure 提供了多个选项，用于创建可使用 RDMA 网络通信的 Linux HPC VM 的群集，包括： 

* **虚拟机**：在同一可用性集中部署支持 RDMA 的 HPC VM（在使用 Azure 资源管理器部署模型时）。 如果使用经典部署模型，请在同一云服务中部署 VM。 

* **虚拟机规模集**：在 VM 规模集中，请确保将部署限制为单个放置组。 例如，在资源管理器模板中，将 `singlePlacementGroup` 属性设置为 `true`。 

* **Azure CycleCloud**：在 [Azure CycleCloud](/azure/cyclecloud/) 中创建 HPC 群集，以在 Linux 节点上运行 MPI 作业。

* **Azure Batch**：创建 [Azure Batch](/azure/batch/) 池，以在 Linux 计算节点上运行 MPI 工作负荷。 有关详细信息，请参阅[在 Batch 池中使用支持 RDMA 或启用了 GPU 的实例](../../batch/batch-pool-compute-intensive-sizes.md)。 要在 Batch 上运行基于容器的工作负荷，另请参阅 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 项目。

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) 支持在计算节点上运行多个 Linux 分发项，而这些节点是在由 Windows Server 头节点管理的支持 RDMA 的 Azure VM 中部署的。 相关部署示例，请参阅[在 Azure 中创建 HPC Pack Linux RDMA 群集](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。

可能需要其他系统配置才能运行 MPI 作业，具体取决于所选的群集管理工具。 例如，在 VM 的群集上，可能需要生成 SSH 密钥或建立无密码的 SSH 信任，从而在群集节点之间建立信任。

### <a name="network-topology-considerations"></a>网络拓扑注意事项
* 在 Azure 中支持 RDMA 的 Linux VM 上，Eth1 保留用于传输 RDMA 网络流量。 不要更改引用此网络的配置文件中的任何 Eth1 设置或任何信息。 Eth0 保留用于传输常规 Azure 网络流量。

* Azure 中的 RDMA 网络保留地址空间 172.16.0.0/16。 




## <a name="other-sizes"></a>其他大小
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。





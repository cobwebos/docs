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
ms.openlocfilehash: 003a14174ff65bab253f27a458d4f3e2c0a1a6db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070004"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>高性能计算虚拟机大小

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

在 Azure 上的已启用的 VM 大小允许 MPI 使用几乎任何风格 SR IOV。
非 SR-IOV 已启用在 Vm 上，支持仅 Intel MPI 5.x 版本。 更高版本 （2017 版和 2018年） 的 Intel MPI 运行时库可能会或可能不与 Azure Linux RDMA 驱动程序兼容。


### <a name="supported-os-images"></a>支持的 OS 映像
 
Azure Marketplace 有支持 RDMA 连接的许多 Linux 分发：
  
* **基于 centOS 的 HPC** -对于非 SR-IOV 启用了 Vm，基于 CentOS 的 6.5 HPC 或更高版本，最多 7.5 都适用。 对于 H 系列 Vm，建议版本 7.1 到 7.5。 在 VM 上安装 RDMA 驱动程序和 Intel MPI 5.1。
  对于 SR-IOV 的 Vm，CentOS HPC 7.6 附带优化和预先加载与 RDMA 驱动程序和安装的各种 MPI 包。
  对于其他 RHEL/CentOS VM 映像，添加用于启用 InfiniBand InfiniBandLinux 扩展。 此 Linux VM 扩展安装 RDMA 连接 Mellanox OFED SR-IOV Vm 上的驱动程序。 以下 PowerShell cmdlet 支持 RDMA 的现有 VM 上安装 InfiniBandDriverLinux 扩展的最新版本 （版本 1.0）。 名为支持 RDMA 的 VM *myVM*和名为的资源组中部署*myResourceGroup*中*美国西部*区域，如下所示：

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  或者，可以通过下面的 JSON 元素的易于部署的 Azure 资源管理器模板中包括 VM 扩展：
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
 
  > [!NOTE]
  > 在基于 CentOS 的 HPC 映像中，内核更新已在 **yum** 配置文件中禁用。 这是因为 Linux RDMA 驱动程序以 RPM 包的形式分发，如果更新了内核，驱动程序更新可能无法运行。
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 for HPC、 SLES 12 SP3 for HPC （高级），SLES 12 SP1 for HPC、 SLES 12 SP1 for HPC （高级）、 SLES 12 SP4 和 SLES 15。 安装 RDMA 驱动程序，并在 VM 上分发 Intel MPI 包。 运行以下命令安装 MPI：

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -Ubuntu Server 16.04 LTS、 18.04 LTS。 在 VM 上配置 RDMA 驱动程序，并注册 Intel 下载 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  有关详细详细了解如何启用 InfiniBand，设置 MPI，请参阅[启用 InfiniBand](../workloads/hpc/enable-infiniband.md)。


### <a name="cluster-configuration-options"></a>群集配置选项

Azure 提供了多个选项，用于创建可使用 RDMA 网络通信的 Linux HPC VM 的群集，包括： 

* **虚拟机**：在同一可用性集中部署支持 RDMA 的 HPC VM（在使用 Azure 资源管理器部署模型时）。 如果使用经典部署模型，请在同一云服务中部署 VM。 

* **虚拟机规模集**-在虚拟机规模集，请确保限制为单个放置组部署。 例如，在资源管理器模板中，将 `singlePlacementGroup` 属性设置为 `true`。 

* **Azure CycleCloud**：在 [Azure CycleCloud](/azure/cyclecloud/) 中创建 HPC 群集，以在 Linux 节点上运行 MPI 作业。

* **Azure Batch**：创建 [Azure Batch](/azure/batch/) 池，以在 Linux 计算节点上运行 MPI 工作负荷。 有关详细信息，请参阅[在 Batch 池中使用支持 RDMA 或启用了 GPU 的实例](../../batch/batch-pool-compute-intensive-sizes.md)。 要在 Batch 上运行基于容器的工作负荷，另请参阅 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 项目。

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) 支持在计算节点上运行多个 Linux 分发项，而这些节点是在由 Windows Server 头节点管理的支持 RDMA 的 Azure VM 中部署的。 相关部署示例，请参阅[在 Azure 中创建 HPC Pack Linux RDMA 群集](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。


### <a name="network-considerations"></a>网络注意事项
* 非-sr-iov，启用了 RDMA 的 Linux 虚拟机在 Azure 中，eth1 保留为 RDMA 网络流量。 不要更改任何 eth1 设置或引用此网络的配置文件中的任何信息。
* 在上启用 SR-IOV 的 Vm （HB 和 HC 系列）、 ib0 保留用于传输 RDMA 网络流量。
* Azure 中的 RDMA 网络保留地址空间 172.16.0.0/16。 若要在 Azure 虚拟网络中部署的实例上运行 MPI 应用程序，请确保虚拟网络地址空间不与 RDMA 网络重叠。
* 可能需要其他系统配置才能运行 MPI 作业，具体取决于所选的群集管理工具。 例如，在群集上的 Vm，可能需要通过生成 SSH 密钥或通过建立无密码 SSH 登录名建立在群集节点间的信任关系。


## <a name="other-sizes"></a>其他大小
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 要详细了解如何设置、 优化和缩放[HPC 工作负荷](../workloads/hpc/configure.md)在 Azure 上。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。

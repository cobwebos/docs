---
title: Azure Windows VM 大小 - HPC | Microsoft Docs
description: 列出 Azure 中适用于 Windows 高性能计算虚拟机的各种大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: 0c5398caf85566f78c7ebe9e8ba114a53d2f6271
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088527"
---
# <a name="high-performance-compute-vm-sizes"></a>高性能计算 VM 大小

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **操作系统**-Windows Server 2016 以上所有 HPC 系列 vm。 Windows Server 2012 R2 和 Windows Server 2012 在非 SR-IOV 启用的 Vm (因此不包括 HB-ACCT-WC 和 HC) 中也受支持。

* **MPI** -Azure 上启用 SR-IOV 的 VM 大小 (HB-ACCT-WC, HC) 允许将任何适用于 Mellanox OFED 的 MPI 的任何风格。
在非 SR-IOV 启用 Vm 上, 支持的 MPI 实现使用 Microsoft Network Direct (ND) 接口在实例之间进行通信。 因此, 只支持 Microsoft MPI (MS-CHAP) 2012 R2 或更高版本和 Intel MPI 1.x 版本。 Intel MPI 运行时库的更高版本 (2017, 2018) 不一定与 Azure RDMA 驱动程序兼容。

* **INFINIBANDDRIVERWINDOWS vm 扩展**-在支持 RDMA 的 vm 上, 添加 InfiniBandDriverWindows 扩展以启用无限功能。 此 Windows VM 扩展为 RDMA 连接性安装 Windows 网络直接驱动程序 (在非 SR-IOV Vm 上) 或 Mellanox OFED 驱动程序 (在 SR-IOV Vm 上)。
在某些 A8 和 A9 实例的部署中, 会自动添加 HpcVmDrivers 扩展。 请注意, 不推荐使用 HpcVmDrivers VM 扩展;它不会更新。 若要将 VM 扩展添加到 VM，可以使用 [Azure PowerShell](/powershell/azure/overview) cmdlet。 

  以下命令在 "*美国西部*" 区域中名为 " *myResourceGroup* " 的资源组中的名为 " *MYVM* " 的现有支持 RDMA 的 VM 上安装最新版本 1.0 InfiniBandDriverWindows 扩展:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  此外, 还可以在 Azure 资源管理器模板中包含 VM 扩展, 以便进行部署, 其中包含以下 JSON 元素:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  以下命令在名为*myResourceGroup*的资源组中部署的名为*MYVMSS*的现有 vm 规模集中的所有支持 RDMA 的 vm 上安装最新版本 1.0 InfiniBandDriverWindows 扩展。

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  有关详细信息，请参阅[虚拟机扩展和功能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 还可使用[经典部署模型](classic/manage-extensions.md)中部署的 VM 扩展。

* **RDMA 网络地址空间** - Azure 中的 RDMA 网络保留地址空间 172.16.0.0/16。 若要在 Azure 虚拟网络中部署的实例上运行 MPI 应用程序，请确保虚拟网络地址空间不与 RDMA 网络重叠。


### <a name="cluster-configuration-options"></a>群集配置选项

Azure 提供了多个选项，用于创建可使用 RDMA 网络通信的 Windows HPC VM 的群集，包括： 

* **虚拟机**：在同一可用性集中部署支持 RDMA 的 HPC VM（在使用 Azure 资源管理器部署模型时）。 如果使用经典部署模型，请在同一云服务中部署 VM。 

* **虚拟机规模**集-在虚拟机规模集中, 确保将部署限制为单个放置组。 例如，在资源管理器模板中，将 `singlePlacementGroup` 属性设置为 `true`。 

* **虚拟机**之间的 mpi-如果虚拟机 (vm) 之间需要 mpi 通信, 请确保 vm 处于同一可用性集或虚拟机相同规模集。

* **Azure CycleCloud**：在 [Azure CycleCloud](/azure/cyclecloud/) 中创建 HPC 群集，以在 Windows 节点上运行 MPI 作业。

* **Azure Batch**：创建 [Azure Batch](/azure/batch/) 池，以在 Windows Server 计算节点上运行 MPI 工作负荷。 有关详细信息，请参阅[在 Batch 池中使用支持 RDMA 或启用了 GPU 的实例](../../batch/batch-pool-compute-intensive-sizes.md)。 要在 Batch 上运行基于容器的工作负荷，另请参阅 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 项目。

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) 包括用于 MS-MPI 的、在支持 RDMA 的 Windows VM 上部署时使用 Azure RDMA 网络的运行时环境。 有关示例部署，请参阅[使用 HPC Pack 设置一个运行 MPI 应用程序的 Windows RDMA 群集](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="other-sizes"></a>其他大小
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](../virtual-machines-windows-sizes-memory.md)
- [存储优化](../virtual-machines-windows-sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 有关在 Windows Server 上配合使用计算密集型实例和 HPC Pack 的清单，请参阅[使用 HPC Pack 设置一个用于运行 MPI 应用程序的 Windows RDMA 群集](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

- 若要在 Azure Batch 中运行 MPI 应用程序时使用计算密集型实例，请参阅[在 Azure Batch 中使用多实例任务来运行消息传递接口 (MPI) 应用程序](../../batch/batch-mpi.md)。

- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。

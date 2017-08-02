---
title: "Azure Windows VM 大小 - HPC | Microsoft Docs"
description: "列出 Azure 中适用于 Windows 高性能计算虚拟机的各种大小。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 162638197d096294c27df5eb7817b65e90dd9729
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---

# <a name="high-performance-compute-vm-sizes"></a>高性能计算 VM 大小

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>支持 RDMA 的实例
计算密集型实例（H16r、H16mr、A8 和 A9）的子集具有远程直接内存访问 (RDMA) 连接的网络接口。 此接口是对可供其他 VM 大小使用的标准 Azure 网络接口的补充。 
  
此接口允许支持 RDMA 的实例在 InfiniBand 网络上通信，对 H16r 和 H16mr 虚拟机以 FDR 速率运行，对 A8 和 A9 虚拟机以 QDR 速率运行。 这些 RDMA 功能可以提高消息传递接口 (MPI) 应用程序的可伸缩性和性能。

支持 RDMA 的 Windows VM 可以访问 Azure RDMA 网络的要求如下： 

* **操作系统**
  
  Windows Server 2012 R2、Windows Server 2012
  
  > [!NOTE]
  > 目前，Windows Server 2016 在 Azure 中不支持 RDMA 连接。
  >

* **可用性集或云服务** – 在同一个可用性集中（使用 Azure Resource Manager 部署模型时）或同一个云服务中（使用经典部署模型时）部署支持 RDMA 的 VM。 如果使用 Azure Batch，则支持 RDMA 的VM 必须位于同一池中。

* **MPI**：Microsoft MPI (MS-MPI) 2012 R2 或更高版本、Intel MPI Library 5.x

  支持的 MPI 实现使用 Microsoft Network Direct 接口在实例之间通信。 

* **RDMA 网络地址空间** - Azure 中的 RDMA 网络保留地址空间 172.16.0.0/16。 若要在 Azure 虚拟网络中部署的实例上运行 MPI 应用程序，请确保虚拟网络地址空间不与 RDMA 网络重叠。

* **HpcVmDrivers VM 扩展** - 在支持 RDMA 的 VM 上，必须添加 HpcVmDrivers 扩展才能安装 RDMA 连接所需的 Windows 网络设备驱动程序。 （在某些 A8 和 A9 实例的部署中，会自动添加 HpcVmDrivers 扩展。）若要将 VM 扩展添加到 VM，可以使用 [Azure PowerShell](/powershell/azure/overview) cmdlet。 

  
  以下命令在 *West US* 区域的 *myResourceGroup* 资源组中部署的、支持 RDMA 的现有 VM *myVM* 上安装最新的 HpcVMDrivers 扩展版本 1.1：

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  有关详细信息，请参阅[虚拟机扩展和功能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 还可使用[经典部署模型](classic/manage-extensions.md)中部署的 VM 扩展。


## <a name="using-hpc-pack"></a>使用 HPC Pack

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)：Microsoft 的免费 HPC 群集和作业管理解决方案，它是一个方便在 Azure 中创建计算群集来运行基于 Windows 的 MPI 应用程序和其他 HPC 工作负荷的选项。 HPC Pack 2012 R2 和更高版本包括用于 MS-MPI 的、在支持 RDMA 的 VM 上部署时使用 Azure RDMA 网络的运行时环境。




## <a name="other-sizes"></a>其他大小
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](../virtual-machines-windows-sizes-memory.md)
- [存储优化](../virtual-machines-windows-sizes-storage.md)
- [GPU 优化](sizes-gpu.md)

## <a name="next-steps"></a>后续步骤

- 有关在 Windows Server 上配合使用计算密集型实例和 HPC Pack 的清单，请参阅[使用 HPC Pack 设置一个用于运行 MPI 应用程序的 Windows RDMA 群集](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

- 若要在 Azure Batch 中运行 MPI 应用程序时使用计算密集型实例，请参阅[在 Azure Batch 中使用多实例任务来运行消息传递接口 (MPI) 应用程序](../../batch/batch-mpi.md)。

- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。






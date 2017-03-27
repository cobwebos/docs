---
title: "关于 Windows 上的计算密集型 VM | Microsoft Docs"
description: "了解有关针对 Windows VM 和云服务使用 Azure H 系列和 A8、A9、A10 及 A11 计算密集型实例的背景信息和注意事项"
services: virtual-machines-windows, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 58e8474a9cafdad06c2968a7317e0c30474b5069
ms.openlocfilehash: 5021a0aa554978fbb5543024400986715227de0b
ms.lasthandoff: 03/01/2017


---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-windows"></a>关于适用于 Windows 的 H 系列和计算密集型 A 系列 VM
本文提供有关使用较新的 Azure H 系列和较早的 A8、A9、A10 和 A11 实例（也称为*计算密集型*实例）的背景信息和一些注意事项。 本文重点介绍如何对 Windows VM 使用这些实例。 本文同样适用于 [Linux VM](virtual-machines-linux-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

有关基本规范、存储容量和磁盘详细信息，请参阅[虚拟机的大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>RDMA 网络访问权限
若要访问用于 Windows MPI 通信的 Azure RDMA 网络，支持 RDMA 的实例必须满足以下要求： 

* **操作系统**
  
  * **虚拟机**：Windows Server 2012 R2、Windows Server 2012
  * **云服务**：Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 来宾 OS 系列
* **MPI**：Microsoft MPI (MS-MPI) 2012 R2 或更高版本、Intel MPI Library 5.x

  支持的 MPI 实现使用 Microsoft Network Direct 接口在实例之间通信。 
* **HpcVmDrivers VM 扩展** - 在具有 RDMA 功能的 VM 上，必须添加 HpcVmDrivers 扩展才能安装 RDMA 连接所需的 Windows 网络设备驱动程序。 （在某些 A8 和 A9 实例的部署中，会自动添加 HpcVmDrivers 扩展。）可使用用于 Azure Resource Manager 的 [Azure PowerShell](/powershell/azureps-cmdlets-docs) cmdlet 将 VM 扩展添加到 VM。

  获取有关最新 HpcVmDrivers 扩展的信息：

  ```PowerShell
  Get-AzureVMAvailableExtension -ExtensionName  "HpcVmDrivers"
  ```

  若要在现有的支持 RDMA 的 VM（名为 myVM）上安装最新版本 1.1 HpcVMDrivers 扩展：
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  有关详细信息，请参阅[管理 VM 扩展](virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 还可在[经典部署模型](virtual-machines-windows-classic-manage-extensions.md)下使用 VM 的扩展。


## <a name="considerations-for-hpc-pack-and-windows"></a>HPC Pack 和 Windows 的注意事项
[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)：Microsoft 的免费 HPC 群集和作业管理解决方案，将计算密集型实例与 Windows Server. 配合使用时不需要。 但是，它提供了一个选项，方便在 Azure 中创建计算群集来运行基于 Windows 的 MPI 应用程序和其他 HPC 工作负荷。 HPC Pack 2012 R2 和更高版本包括用于 MS-MPI 的运行时环境，在具有 RDMA 功能的 VM 上部署时可以使用 Azure RDMA 网络。

有关在 Windows Server 上配合使用计算密集型实例和 HPC Pack 的详细信息和清单，请参阅[使用 HPC Pack 设置一个用于运行 MPI 应用程序的 Windows RDMA 群集](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="next-steps"></a>后续步骤
* 有关计算密集型大小的可用性和定价的详细信息，请参阅[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)和[云服务定价](https://azure.microsoft.com/pricing/details/cloud-services/)。
* 有关 VM 存储容量和磁盘详细信息，请参阅 [Sizes for virtual machines](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（虚拟机大小）。
* 若要开始在 Windows 上通过 HPC Pack 部署和使用计算密集型实例，请参阅[使用 HPC Pack 设置一个用于运行 MPI 应用程序的 Windows RDMA 群集](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
* 有关使用计算密集型实例在 Azure Batch 中运行 MPI 应用程序的信息，请参阅[在 Azure Batch 中使用多实例任务来运行消息传递接口 (MPI) 应用程序](../batch/batch-mpi.md)。



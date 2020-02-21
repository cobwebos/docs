---
title: Azure VM 大小-HPC |Microsoft Docs
description: 列出 Azure 中适用于高性能计算虚拟机的不同大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: dc2086223dea9bff311aac9e7d4771b5273f0e91
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493548"
---
# <a name="high-performance-compute-vm-sizes"></a>高性能计算 VM 大小

Azure H 系列虚拟机（Vm）旨在为各种真实的 HPC 工作负荷提供领先的性能、MPI 可伸缩性和成本效益。

[HBv2 系列](hbv2-series.md)Vm 功能 200 Gb/秒，Mellanox HDR 不会有任何功能，同时，HB-ACCT-WC 和 HC 系列 Vm 的功能 100 Gb/秒。 其中每个 VM 类型都连接在一个非阻塞 fat 树中，以实现优化和一致的 RDMA 性能。 HBv2 Vm 支持自适应路由和动态连接传输（DCT，附加到标准 RC 和 UD 传输）。 这些功能增强了应用程序的性能、可伸缩性和一致性，并且强烈建议使用这些功能。

[Hb-acct-wc 系列](hb-series.md)Vm 针对内存带宽驱动的应用程序进行了优化，例如流体 dynamics、显式有限元素分析和天气建模。 HB-ACCT-WC Vm 功能 60 AMD EPYC 7551 处理器核心，每个 CPU 核心 4 GB RAM，无超线程。 AMD EPYC 平台提供超过 260 GB/秒的内存带宽。

[HC 系列](hc-series.md)Vm 针对密集计算驱动的应用程序进行了优化，如隐式有限元素分析、分子 dynamics 和计算化学。 HC Vm 功能 44 Intel 至强白金8168处理器核心，每 CPU 内核 8 GB RAM，无超线程。 Intel 强白金平台支持 Intel 丰富的软件工具生态系统，如 Intel 数学内核库。

[H 系列](h-series.md)Vm 针对按 CPU 频率高的应用程序或每个核心要求提供较大内存的应用程序进行了优化。 H 系列 Vm 的功能8或 16 Intel 至强 E5 2667 v3 处理器核心、7或 14 GB 的 RAM/CPU 核心，无超线程。 H 系列功能 56 Gb/秒，FDR 不会阻止 fat 树配置，以实现一致的 RDMA 性能。 H 系列 Vm 支持 Intel MPI 1.x 和 MS-CHAP。

## <a name="deployment-considerations"></a>部署注意事项

- **Azure 订阅** - 若要部署不止一些计算密集型实例，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

- **定价和可用性** - 只在标准定价层提供 VM 大小。 有关各 Azure 区域推出的产品，请查看 [Products available by region](https://azure.microsoft.com/global-infrastructure/services/)（按区域提供的产品）。
- **核心配额** - 可能需要在 Azure 订阅中在默认值的基础上增加核心配额。 订阅可能也会限制可在特定 VM 大小系列（包括 H 系列）中部署的核心数目。 若要请求提高配额，可免费[提出在线客户支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 （默认限制可能会因订阅类别而异。）

  > [!NOTE]
  > 如果有大规模容量需求，请联系 Azure 支持。 Azure 配额为信用额度，而不是容量保障。 不管配额是什么，都只根据所用的核心数计费。
  
- **虚拟网络** – Azure [虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)不需要使用计算密集型实例。 但是，对于许多部署来说，如果需要访问本地资源，则可能至少需要一个基于云的 Azure 虚拟网络或站点到站点连接。 需要时，请创建一个新的虚拟网络来部署实例。 不支持将计算密集型 VM 添加到地缘组中的虚拟网络。
- **调整大小** - 考虑到专用硬件，可以只对同一大小系列（H 系列或计算密集型 A 系列）内的计算密集型实例进行大小调整。 例如，可仅将 H 系列 VM 的大小从一个 H 系列大小调整为另一个。 此外，不支持从非计算密集型大小调整为计算密集型大小。  
## <a name="rdma-capable-instances"></a>支持 RDMA 的实例

计算密集型实例（A8、A9、H16r、H16mr、HB-ACCT-WC 和 HC）的一个子集为远程直接内存访问（RDMA）连接提供网络接口。 所选的 N 系列大小为 "r" （如 NC24rs 配置（NC24rs_v2 和 NC24rs_v3））也支持 RDMA 功能。 此接口是对可供其他 VM 大小使用的标准 Azure 网络接口的补充。

此接口允许支持 RDMA 的实例通过未使用的（IB）网络进行通信，以 HB-ACCT-WC、HC、FDR 速率（适用于 H16r、H16mr 和支持 RDMA 的 N 系列虚拟机）运行，并为 A8 和 A9 虚拟机提供 QDR 速率。 这些 RDMA 功能可以提高某些消息传递接口 (MPI) 应用程序的可伸缩性和性能。 有关速度的详细信息，请参阅本页上的表中的详细信息。

> [!NOTE]
> 在 Azure 中，仅支持基于 SR-IOV 的 Vm （SR-IOV，当前 HB-ACCT-WC 和 HC）支持 IP over IB。 支持 RDMA 的所有支持 RDMA 的实例。
- **操作系统**-Windows Server 2016 以上所有 HPC 系列 vm。 Windows Server 2012 R2 和 Windows Server 2012 在非 SR-IOV 启用的 Vm （因此不包括 HB-ACCT-WC 和 HC）中也受支持。

- **MPI** -Azure 上启用 SR-IOV 的 VM 大小（HB-ACCT-WC，HC）允许将任何适用于 Mellanox OFED 的 MPI 的任何风格。
在非 SR-IOV 启用 Vm 上，支持的 MPI 实现使用 Microsoft Network Direct （ND）接口在实例之间进行通信。 因此，只支持 Microsoft MPI （MS-CHAP） 2012 R2 或更高版本和 Intel MPI 1.x 版本。 Intel MPI 运行时库的更高版本（2017，2018）不一定与 Azure RDMA 驱动程序兼容。

- **INFINIBANDDRIVERWINDOWS vm 扩展**-在支持 RDMA 的 vm 上，添加 InfiniBandDriverWindows 扩展以启用无限功能。 此 Windows VM 扩展为 RDMA 连接性安装 Windows 网络直接驱动程序（在非 SR-IOV Vm 上）或 Mellanox OFED 驱动程序（在 SR-IOV Vm 上）。
在某些 A8 和 A9 实例的部署中，会自动添加 HpcVmDrivers 扩展。 请注意，不推荐使用 HpcVmDrivers VM 扩展;它不会更新。 若要将 VM 扩展添加到 VM，可以使用 [Azure PowerShell](/powershell/azure/overview) cmdlet。 

  以下命令在 "*美国西部*" 区域中名为 " *myResourceGroup* " 的资源组中的名为 " *MYVM* " 的现有支持 RDMA 的 VM 上安装最新版本 1.0 InfiniBandDriverWindows 扩展：

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  此外，还可以在 Azure 资源管理器模板中包含 VM 扩展，以便进行部署，其中包含以下 JSON 元素：

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

  有关详细信息，请参阅[虚拟机扩展和功能](/extensions/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 还可使用[经典部署模型](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)中部署的 VM 扩展。

- **RDMA 网络地址空间** - Azure 中的 RDMA 网络保留地址空间 172.16.0.0/16。 若要在 Azure 虚拟网络中部署的实例上运行 MPI 应用程序，请确保虚拟网络地址空间不与 RDMA 网络重叠。

## <a name="cluster-configuration-options"></a>群集配置选项

Azure 提供了多个选项，用于创建可使用 RDMA 网络通信的 Windows HPC VM 的群集，包括： 

- **虚拟机**：在同一可用性集中部署支持 RDMA 的 HPC VM（在使用 Azure 资源管理器部署模型时）。 如果使用经典部署模型，请在同一云服务中部署 VM。 

- **虚拟机规模**集-在虚拟机规模集中，确保将部署限制为单个放置组。 例如，在资源管理器模板中，将 `singlePlacementGroup` 属性设置为 `true`。 

- **虚拟机**之间的 mpi-如果虚拟机（vm）之间需要 mpi 通信，请确保 vm 处于同一可用性集或虚拟机相同规模集。

- **Azure CycleCloud**：在 [Azure CycleCloud](/azure/cyclecloud/) 中创建 HPC 群集，以在 Windows 节点上运行 MPI 作业。

- **Azure Batch**：创建 [Azure Batch](/azure/batch/) 池，以在 Windows Server 计算节点上运行 MPI 工作负荷。 有关详细信息，请参阅[在 Batch 池中使用支持 RDMA 或启用了 GPU 的实例](../batch/batch-pool-compute-intensive-sizes.md)。 要在 Batch 上运行基于容器的工作负荷，另请参阅 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 项目。

- **MICROSOFT Hpc pack** - [hpc pack](https://docs.microsoft.com/powershell/high-performance-computing/overview)包含一个用于 MS MPI 的运行时环境，该环境在支持 rdma 的 Linux vm 上部署时使用 Azure RDMA 网络。 有关示例部署，请参阅[使用 HPC Pack 设置 LINUX RDMA 群集以运行 MPI 应用程序](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 若要在 Windows Server 上使用具有 HPC Pack 的计算密集型实例的清单，请参阅使用[Hpc Pack 设置 LINUX RDMA 群集以运行 MPI 应用程序](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。

- 若要在 Azure Batch 中运行 MPI 应用程序时使用计算密集型实例，请参阅[在 Azure Batch 中使用多实例任务来运行消息传递接口 (MPI) 应用程序](../batch/batch-mpi.md)。

- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
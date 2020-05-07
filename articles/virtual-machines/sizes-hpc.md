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
ms.openlocfilehash: 409fe69d111e2c5aebe0ad0bd38ced10604b5f1b
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82839056"
---
# <a name="high-performance-computing-vm-sizes"></a>高性能计算 VM 大小

Azure H 系列虚拟机（Vm）旨在为各种真实的 HPC 工作负荷提供领先的性能、MPI 可伸缩性和成本效益。

[HBv2 系列](hbv2-series.md)Vm 功能 200 Gb/秒，Mellanox HDR 不会有任何功能，同时，HB-ACCT-WC 和 HC 系列 Vm 的功能 100 Gb/秒。 其中每个 VM 类型都连接在一个非阻塞 fat 树中，以实现优化和一致的 RDMA 性能。 HBv2 Vm 支持自适应路由和动态连接传输（DCT，附加到标准 RC 和 UD 传输）。 这些功能增强了应用程序的性能、可伸缩性和一致性，并且强烈建议使用这些功能。

[Hb-acct-wc 系列](hb-series.md)Vm 针对内存带宽驱动的应用程序进行了优化，例如流体 dynamics、显式有限元素分析和天气建模。 HB-ACCT-WC Vm 功能 60 AMD EPYC 7551 处理器核心，每个 CPU 核心 4 GB RAM，无超线程。 AMD EPYC 平台提供超过 260 GB/秒的内存带宽。

[HC 系列](hc-series.md)Vm 针对密集计算驱动的应用程序进行了优化，如隐式有限元素分析、分子 dynamics 和计算化学。 HC Vm 功能 44 Intel 至强白金8168处理器核心，每 CPU 内核 8 GB RAM，无超线程。 Intel 强白金平台支持 Intel 丰富的软件工具生态系统，如 Intel 数学内核库。

[H 系列](h-series.md)Vm 针对按 CPU 频率高的应用程序或每个核心要求提供较大内存的应用程序进行了优化。 H 系列 Vm 的功能8或 16 Intel 至强 E5 2667 v3 处理器核心、7或 14 GB 的 RAM/CPU 核心，无超线程。 H 系列功能 56 Gb/秒，FDR 不会阻止 fat 树配置，以实现一致的 RDMA 性能。 H 系列 Vm 支持 Intel MPI 1.x 和 MS-CHAP。

> [!NOTE]
> A8 – A11 Vm 计划于3/2021 停用。 有关详细信息，请参阅[HPC 迁移指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

## <a name="rdma-capable-instances"></a>支持 RDMA 的实例

大多数 HPC VM 大小（HBv2、HB-ACCT-WC、HC、H16r、H16mr、A8 和 A9）都具有用于远程直接内存访问（RDMA）连接的网络接口。 选择了用 "r" （如 NC24rs 配置（NC24rs_v3、NC24rs_v2 和 NC24r）指定的[N 系列](https://docs.microsoft.com/azure/virtual-machines/nc-series)大小也是支持 RDMA 功能的。 此接口是对其他 VM 大小中可用的标准 Azure 网络接口的补充。

此接口允许支持 RDMA 的实例通过未使用的（IB）网络进行通信，以 HBv2、HB-ACCT-WC、HC、FDR 速率为 H16r、H16mr 和支持 RDMA 的 N 系列虚拟机进行通信，并为 A8 和 A9 Vm 提供 QDR 速率。 这些 RDMA 功能可以提高某些消息传递接口 (MPI) 应用程序的可伸缩性和性能。 有关速度的详细信息，请参阅本页上的表中的详细信息。

> [!NOTE]
> 在 Azure HPC 中，有两类 Vm，具体取决于虚拟机是否已启用 SR-IOV。 目前，启用了允许的虚拟机的 SR-IOV 为： HBv2、HB-ACCT-WC、HC 和 NCv3。 其余启用了未支持的 Vm 不会启用 SR-IOV。
> 支持 RDMA 的所有 Vm 都支持 RDMA over IB。
> 仅支持 SR-IOV 的 Vm 上的 IP over IB。

- **操作系统**-Linux 非常适合 HPC vm，发行版，例如 CENTOS、RHEL、UBUNTU 和 SUSE。 对于 Windows 支持，所有 HPC 系列 Vm 都支持 Windows Server 2016。 Windows Server 2012 R2 和 Windows Server 2012 在非 SR-IOV 启用的 Vm 上也受支持。

- **MPI** -Azure （HBV2，HB-ACCT-WC，HC，NCv3）上启用了 SR-IOV 的 VM 大小，几乎可以使用任何适用于 Mellanox OFED 的 MPI 的任何风格。
在非 SR-IOV 启用 Vm 上，支持的 MPI 实现使用 Microsoft Network Direct （ND）接口在 Vm 之间进行通信。 因此，只支持 Microsoft MPI （MS-CHAP） 2012 R2 或更高版本和 Intel MPI 1.x 版本。 Intel MPI 运行时库的更高版本（2017，2018）不一定与 Azure RDMA 驱动程序兼容。

- **InfiniBandDriver<Linux |Windows> VM 扩展**-在支持 RDMA 的 vm 上，添加 InfiniBandDriver<Linux |用于启用无限的 Windows> 扩展。 在 Linux 上，InfiniBandDriverLinux VM 扩展将为 RDMA 连接性安装 Mellanox OFED 驱动程序（位于 SR-IOV Vm 上）。 在 Windows 上，InfiniBandDriverWindows VM 扩展为 RDMA 连接性安装 Windows 网络直接驱动程序（在非 SR-IOV Vm 上）或 Mellanox OFED 驱动程序（在 SR-IOV Vm 上）。
在某些 A8 和 A9 实例的部署中，会自动添加 HpcVmDrivers 扩展。 请注意，不推荐使用 HpcVmDrivers VM 扩展;它不会更新。
若要将 VM 扩展添加到 VM，可以使用 [Azure PowerShell](/powershell/azure/overview) cmdlet。 

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

  以下命令在名为*myResourceGroup*的资源组中部署的名为*myVMSS*的现有虚拟机规模集中的所有支持 RDMA 的 vm 上安装最新版本 1.0 InfiniBandDriverWindows 扩展。

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  有关详细信息，请参阅[虚拟机扩展和功能](./extensions/overview.md)。 还可使用[经典部署模型](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)中部署的 VM 扩展。

- **RDMA 网络地址空间** - Azure 中的 RDMA 网络保留地址空间 172.16.0.0/16。 若要在 Azure 虚拟网络中部署的实例上运行 MPI 应用程序，请确保虚拟网络地址空间不与 RDMA 网络重叠。

## <a name="cluster-configuration-options"></a>群集配置选项

Azure 提供了多个选项，用于创建可使用 RDMA 网络通信的 Windows HPC VM 的群集，包括： 

- **虚拟机**-在同一规模集或可用性集中（使用 Azure 资源管理器部署模型时）部署支持 RDMA 的 HPC vm。 如果使用经典部署模型，请在同一云服务中部署 VM。

- **虚拟机规模集**-在虚拟机规模集（VMSS）中，确保将部署限制为单个放置组，以便在 VMSS 内进行不受限制的通信。 例如，在资源管理器模板中，将 `singlePlacementGroup` 属性设置为 `true`。 请注意，默认情况下，可以使用`singlePlacementGroup`属性设置的最大`true` VMSS 大小限制为 100 vm。 如果你的 HPC 作业规模需求高于单个 VMSS 租户中的 100 Vm，则你可以请求增加，免费[打开联机客户支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 单个 VMSS 中的 Vm 数目限制可增加到300。 请注意，使用可用性集部署 Vm 时，最大限制为每个可用性集200个 Vm。

- **虚拟机**之间的 MPI-如果虚拟机（vm）之间需要 RDMA （如使用 MPI 通信），请确保 vm 处于相同的虚拟机规模集或可用性集中。

- **Azure CycleCloud** -在[azure CYCLECLOUD](/azure/cyclecloud/)中创建 HPC 群集来运行 MPI 作业。

- **Azure Batch** -创建[Azure Batch](/azure/batch/)池来运行 MPI 工作负荷。 若要在 Azure Batch 中运行 MPI 应用程序时使用计算密集型实例，请参阅[在 Azure Batch 中使用多实例任务来运行消息传递接口 (MPI) 应用程序](../batch/batch-mpi.md)。

- **Microsoft HPC pack** - [HPC pack](https://docs.microsoft.com/powershell/high-performance-computing/overview)包括用于 MS MPI 的运行时环境，该环境在支持 rdma 的 Linux vm 上部署时使用 Azure RDMA 网络。 有关示例部署，请参阅[使用 HPC Pack 设置 LINUX RDMA 群集以运行 MPI 应用程序](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)。

## <a name="deployment-considerations"></a>部署注意事项

- **Azure 订阅** - 若要部署不止一些计算密集型实例，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

- **定价和可用性** - 只在标准定价层提供 VM 大小。 有关各 Azure 区域推出的产品，请查看 [Products available by region](https://azure.microsoft.com/global-infrastructure/services/)（按区域提供的产品）。

- **核心配额** - 可能需要在 Azure 订阅中在默认值的基础上增加核心配额。 订阅可能也会限制可在特定 VM 大小系列（包括 H 系列）中部署的核心数目。 若要请求增加配额，请免费[打开联机客户支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 （默认限制可能会因订阅类别而异。）

  > [!NOTE]
  > 如果有大规模容量需求，请联系 Azure 支持。 Azure 配额为信用额度，而不是容量保障。 不管配额是什么，都只根据所用的核心数计费。
  
- **虚拟网络** – Azure [虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)不需要使用计算密集型实例。 但是，对于许多部署来说，如果需要访问本地资源，则可能至少需要一个基于云的 Azure 虚拟网络或站点到站点连接。 需要时，请创建一个新的虚拟网络来部署实例。 不支持将计算密集型 VM 添加到地缘组中的虚拟网络。

- **调整大小** - 考虑到专用硬件，可以只对同一大小系列（H 系列或计算密集型 A 系列）内的计算密集型实例进行大小调整。 例如，可仅将 H 系列 VM 的大小从一个 H 系列大小调整为另一个。 此外，不支持从非计算密集型大小调整为计算密集型大小。  


## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 详细了解如何优化 Azure 的 HPC 应用程序以及[Hpc 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview)中的一些示例 

- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。

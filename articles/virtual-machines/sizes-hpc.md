---
title: Azure VM 大小-HPC |Microsoft Docs
description: 列出 Azure 中适用于高性能计算虚拟机的不同大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 29033cbabfcfa00c9f8458cbc161af67df5806cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325957"
---
# <a name="high-performance-computing-vm-sizes"></a>高性能计算 VM 大小

Azure H 系列虚拟机 (Vm) 旨在为各种真实的 HPC 工作负荷提供领导级的性能、可伸缩性和成本效益。

[HBv2 系列](hbv2-series.md) Vm 针对内存带宽驱动的应用程序进行了优化，例如流体 dynamics、有限元素分析和容器模拟。 HBv2 Vm 功能 120 AMD EPYC 7742 处理器核心，每个 CPU 核心 4 GB RAM，无并发多线程处理。 每个 HBv2 VM 提供高达 340 GB/秒的内存带宽和最多4个兆次的 FP64 计算。

HBv2 Vm 功能 200 Gb/秒，而 HB-ACCT-WC 和 HC 系列 Vm 功能 100 Gb/秒，则不会产生任何功能。 其中每个 VM 类型都连接在一个非阻塞 fat 树中，以实现优化和一致的 RDMA 性能。 HBv2 Vm 支持自适应路由，而动态连接传输 (DCT，) 中的附加到标准 RC 和 UD 传输。 这些功能增强了应用程序的性能、可伸缩性和一致性，并且强烈建议使用这些功能。

[Hb-acct-wc 系列](hb-series.md) Vm 针对内存带宽驱动的应用程序进行了优化，例如流体 dynamics、显式有限元素分析和天气建模。 HB-ACCT-WC Vm 功能 60 AMD EPYC 7551 处理器核心，每个 CPU 核心 4 GB RAM，无超线程。 AMD EPYC 平台提供超过 260 GB/秒的内存带宽。

[HC 系列](hc-series.md) Vm 针对密集计算驱动的应用程序进行了优化，如隐式有限元素分析、分子 dynamics 和计算化学。 HC Vm 功能 44 Intel 至强白金8168处理器核心，每 CPU 内核 8 GB RAM，无超线程。 Intel 强白金平台支持 Intel 丰富的软件工具生态系统，如 Intel 数学内核库。

[H 系列](h-series.md) Vm 针对按 CPU 频率高的应用程序或每个核心要求提供较大内存的应用程序进行了优化。 H 系列 Vm 的功能8或 16 Intel 至强 E5 2667 v3 处理器核心、7或 14 GB 的 RAM/CPU 核心，无超线程。 H 系列功能 56 Gb/秒，FDR 不会阻止 fat 树配置，以实现一致的 RDMA 性能。 H 系列 Vm 支持 Intel MPI 1.x 和 MS-CHAP。

> [!NOTE]
> [A8 – A11 vm](./sizes-previous-gen.md#a-series---compute-intensive-instances)计划于3/2021 停用。 有关详细信息，请参阅 [HPC 迁移指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

## <a name="rdma-capable-instances"></a>支持 RDMA 的实例

大多数 HPC VM 大小 (HBv2、HB-ACCT-WC、HC、H16r、H16mr、A8 和 A9) 为远程直接内存访问提供网络接口， (RDMA) 连接性。 选定的 [N 系列](./nc-series.md) 大小用 "r" 指定 (ND40rs_v2、ND24rs、NC24rs_v3、NC24rs_v2 和 NC24r) 也支持 RDMA 功能。 此接口是对其他 VM 大小中可用的标准 Azure 以太网网络接口的补充。

此接口允许支持 RDMA 的实例通过一种不合格的 (IB) 网络，以 HBv2、HB-ACCT-WC、HC、NDv2、FDR 速率为 H16r、H16mr 和其他支持 RDMA 的 N 系列虚拟机进行通信，并为 A8 和 A9 Vm 提供 QDR 速率。 这些 RDMA 功能可以提高某些消息传递接口 (MPI) 应用程序的可伸缩性和性能。

> [!NOTE]
> 在 Azure HPC 中，有两类 Vm，具体取决于虚拟机是否已启用 SR-IOV。 目前，启用了允许的虚拟机的 SR-IOV 为： HBv2、HB-ACCT-WC、HC、NCv3 和 NDv2。 目前未启用启用了虚拟机的虚拟机。
> RDMA 仅在 (IB) 网络上启用并且支持所有支持 RDMA 的 Vm。
> 仅支持 SR-IOV 的 Vm 上的 IP over IB。
> 不通过以太网网络启用 RDMA。

- **操作系统** -适用于 HPC vm 的 Linux 非常受支持;通常使用发行版，例如 CentOS、RHEL、Ubuntu 和 SUSE。 对于 Windows 支持，所有 HPC 系列 Vm 都支持 Windows Server 2016 和更高版本。 Windows Server 2012 R2 和 Windows Server 2012 在非 SR-IOV 启用的 Vm (H16r、H16mr、A8 和 A9) 上都受支持。 请注意， [在 HBv2 和具有超过 64 (虚拟或物理) 内核的其他 vm 上不支持 Windows Server 2012 R2](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)。 请参阅 [VM 映像](./workloads/hpc/configure.md) ，获取 Marketplace 上支持的 vm 映像列表，以及如何对其进行相应的配置。

- **无限和驱动程序** -在启用了允许的虚拟机上，需要适当的驱动程序才能启用 RDMA。 在 Linux 上，对于启用 SR-IOV 和非 SR-IOV 虚拟机的 Vm，Marketplace 中的 CentOS-HPC VM 映像已预先配置了适当的驱动程序。 可以使用 [此处的说明](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)，使用正确的驱动程序配置 Ubuntu VM 映像。 请参阅 [配置和优化 LINUX OS 的 vm](./workloads/hpc/configure.md) ，详细了解可用的 VM linux os 映像。

   在 Linux 上，可以使用 [INFINIBANDDRIVERLINUX VM 扩展](./extensions/hpc-compute-infiniband-linux.md) 安装 Mellanox OFED 驱动程序，并在启用 Sr-iov 的 H 和 N 系列 vm 上启用不受支持。 详细了解如何在 [HPC 工作负荷](./workloads/hpc/enable-infiniband.md)的支持 RDMA 的 vm 上启用允许。

   在 Windows 上， [INFINIBANDDRIVERWINDOWS VM 扩展](./extensions/hpc-compute-infiniband-windows.md) 会将 Windows 网络直接 (驱动程序安装在非 sr-iov vm) 或 OFED 驱动程序 (上的驱动程序上，以实现 RDMA 连接) 。 在某些 A8 和 A9 实例的部署中，会自动添加 HpcVmDrivers 扩展。 请注意，不推荐使用 HpcVmDrivers VM 扩展;它不会更新。

   若要将 VM 扩展添加到 VM，可以使用 [Azure PowerShell](/powershell/azure/) cmdlet。 有关详细信息，请参阅[虚拟机扩展和功能](./extensions/overview.md)。 还可使用[经典部署模型](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)中部署的 VM 扩展。

- **MPI** -Azure (HBV2、HB-ACCT-WC、HC、NCv3、NDv2) 上启用了 SR-IOV 的 VM 大小，几乎可以使用任何一种与 Mellanox OFED 结合使用的 MPI。 在非 SR-IOV 启用 Vm 上，支持的 MPI 实现使用 Microsoft Network Direct (ND) 接口在 Vm 之间进行通信。 因此，只支持 Microsoft MPI (MS-MPI) 2012 R2 或更高版本和 Intel MPI 1.x 版本。  (2017，2018) 的 Intel MPI 运行时库的更高版本可能与 Azure RDMA 驱动程序兼容或不兼容。 有关在 Azure 上的 HPC Vm 上设置 MPI 的详细信息，请参阅 [设置 mpi FOR HPC](./workloads/hpc/setup-mpi.md) 。

- **RDMA 网络地址空间** - Azure 中的 RDMA 网络保留地址空间 172.16.0.0/16。 若要在 Azure 虚拟网络中部署的实例上运行 MPI 应用程序，请确保虚拟网络地址空间不与 RDMA 网络重叠。

## <a name="cluster-configuration-options"></a>群集配置选项

Azure 提供了多个选项，用于创建可使用 RDMA 网络通信的 Windows HPC VM 的群集，包括： 

- **虚拟机**  -使用 Azure 资源管理器部署) 模型时，在同一规模集或可用性集中部署支持 RDMA 的 HPC vm (。 如果使用经典部署模型，请在同一云服务中部署 VM。

- **虚拟机规模** 集-在虚拟机规模集中，确保将部署限制为单个放置组，以便在规模集内进行不受限制的通信。 例如，在资源管理器模板中，将 `singlePlacementGroup` 属性设置为 `true`。 请注意，默认情况下，可以使用属性设置的最大规模集大小限制 `singlePlacementGroup` `true` 在 100 vm。 如果你的 HPC 作业规模需求高于单个租户中的 100 Vm，则你可以请求增加，免费 [打开联机客户支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md) 。 单个规模集中 Vm 的数目限制可增加到300。 请注意，使用可用性集部署 Vm 时，最大限制为每个可用性集200个 Vm。

- **虚拟机之间的 MPI** -如果 RDMA (例如，在虚拟机 (vm) 之间需要使用 MPI 通信) ，请确保 vm 处于相同的虚拟机规模集或可用性集中。

- **Azure CycleCloud** -在 [azure CYCLECLOUD](/azure/cyclecloud/) 中创建 HPC 群集来运行 MPI 作业。

- **Azure Batch** -创建 [Azure Batch](../batch/index.yml) 池来运行 MPI 工作负荷。 若要在 Azure Batch 中运行 MPI 应用程序时使用计算密集型实例，请参阅[在 Azure Batch 中使用多实例任务来运行消息传递接口 (MPI) 应用程序](../batch/batch-mpi.md)。

- **MICROSOFT HPC Pack**  - [HPC Pack](/powershell/high-performance-computing/overview)包括用于 MS MPI 的运行时环境，该环境在支持 Rdma 的 Linux vm 上部署时使用 Azure RDMA 网络。 有关示例部署，请参阅 [使用 HPC Pack 设置 LINUX RDMA 群集以运行 MPI 应用程序](/powershell/high-performance-computing/hpcpack-linux-openfoam)。

## <a name="deployment-considerations"></a>部署注意事项

- **Azure 订阅** - 若要部署不止一些计算密集型实例，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

- **定价和可用性** - 只在标准定价层提供 VM 大小。 有关各 Azure 区域推出的产品，请查看 [Products available by region](https://azure.microsoft.com/global-infrastructure/services/)（按区域提供的产品）。

- **核心配额** - 可能需要在 Azure 订阅中在默认值的基础上增加核心配额。 订阅可能也会限制可在特定 VM 大小系列（包括 H 系列）中部署的核心数目。 若要请求增加配额，可免费 [建立联机客户支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md) 。 （默认限制可能会因订阅类别而异。）

  > [!NOTE]
  > 如果有大规模容量需求，请联系 Azure 支持。 Azure 配额为信用额度，而不是容量保障。 不管配额是什么，都只根据所用的核心数计费。
  
- **虚拟网络** – Azure [虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)不需要使用计算密集型实例。 但是，对于许多部署来说，如果需要访问本地资源，则可能至少需要一个基于云的 Azure 虚拟网络或站点到站点连接。 需要时，请创建一个新的虚拟网络来部署实例。 不支持将计算密集型 VM 添加到地缘组中的虚拟网络。

- **调整大小** –由于其专用硬件，只能在同一大小系列 (H 系列或 N 系列) 内调整计算密集型实例的大小。 例如，可仅将 H 系列 VM 的大小从一个 H 系列大小调整为另一个。 对于某些 Vm，可能需要考虑有关不受支持的驱动程序支持和 NVMe 磁盘的其他注意事项。


## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 详细了解如何在[HPC 工作负荷](./workloads/hpc/overview.md)中[配置 Vm](./workloads/hpc/configure.md)、[启用无限](./workloads/hpc/enable-infiniband.md)，为 Azure[设置 MPI](./workloads/hpc/setup-mpi.md)和优化 hpc 应用程序。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。

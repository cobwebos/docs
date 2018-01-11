---
title: "在 Batch 中使用计算密集型 Azure VM |Microsoft Docs"
description: "如何在 Azure Batch 池中利用支持 RDMA 或启用 GPU 的 VM 大小"
services: batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: danlep
ms.openlocfilehash: 26cab5ba892d892e035bd94c52cacabd23eebd0c
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>在 Batch 池中使用支持 RDMA 或启用 GPU 的实例

若要运行某些 Batch 作业，可能需要利用专为大规模计算设计的 Azure VM 大小。 例如，运行多实例 [MPI 工作负荷](batch-mpi.md)时，可为远程直接内存访问 (RDMA) 选择具备网络接口的 A8、A9 或 H 系列大小。 这些大小均连接到用于节点间通信的 InfiniBand 网络，可加快 MPI 应用程序的速度。 或者，对于 CUDA 应用程序，可以选择包含 NVIDIA Tesla 图形处理单元 (GPU) 卡的 N 系列大小。

本文介绍了在 Batch 池中使用 Azure 的某些专用大小的指南和示例。 有关规格和背景的信息，请参阅：

* 高性能计算 VM 大小（[Linux](../virtual-machines/linux/sizes-hpc.md)、[Windows](../virtual-machines/windows/sizes-hpc.md)） 

* 启用 GPU 的 VM 大小（[Linux](../virtual-machines/linux/sizes-gpu.md)、[Windows](../virtual-machines/windows/sizes-gpu.md)） 


## <a name="subscription-and-account-limits"></a>订阅和帐户限制

* **配额** - [每个 Batch 帐户的专用内核配额](batch-quota-limit.md#resource-quotas)可能会限制添加到 Batch 池的节点数量或类型。 当选择支持 RDMA、启用 GPU 或其他多核 VM 大小时，更有可能会达到配额。 默认情况下，该配额为 20 个内核。 如果使用它们，单独的配额也将适用于[低优先级 VM](batch-low-pri-vms.md)。 

如果需要请求增加配额，可免费建立[联机客户支持请求](../azure-supportability/how-to-create-azure-support-request.md)。

* 区域可用性 - 计算密集型 VM 在创建 Batch 帐户的区域可能无法使用。 若要检查大小是否可用，请参阅[可用产品（按区域）](https://azure.microsoft.com/regions/services/)。


## <a name="dependencies"></a>依赖项

计算密集型大小的 RDMA 和 GPU 功能仅在某些操作系统中才受到支持。 根据你的操作系统，可能需要安装或配置其他驱动程序或软件。 下表总结了这些依存关系。 有关详细信息，请参阅链接的文章。 有关配置 Batch 池的选项，请参阅本文后面部分。


### <a name="linux-pools---virtual-machine-configuration"></a>Linux 池 - 虚拟机配置

| 大小 | 功能 | 操作系统 | 所需软件 | 池设置 |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS、<br/>SUSE Linux Enterprise Server 12 HPC 或<br/>基于 CentO 的 HPC<br/>(Microsoft Azure Marketplace) | Intel MPI 5 | 启用节点间通信，禁用并发任务执行 |
| [NC 系列*](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-ncv2-and-nd-vms) | NVIDIA Tesla K80 GPU | Ubuntu 16.04 LTS、<br/>Red Hat Enterprise Linux 7.3 或<br/>基于 CentOS 的 7.3<br/>(Microsoft Azure Marketplace) | NVIDIA CUDA Toolkit 9.0 驱动程序 | 不适用 | 
| [NV 系列](../virtual-machines/linux/n-series-driver-setup.md#install-grid-drivers-for-nv-vms) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS、<br/>Red Hat Enterprise Linux 7.3 或<br/>基于 CentOS 的 7.3<br/>(Microsoft Azure Marketplace) | NVIDIA GRID 4.3 驱动程序 | 不适用 |

*基于 Ubuntu 16.04 LTS 或 CentOS 且具备 Intel MPI 的 7.3 HPC （来自 Azure Marketplace）支持在 NC24r VM 上连接 RDMA。



### <a name="windows-pools---virtual-machine-configuration"></a>Windows 池 - 虚拟机配置

| 大小 | 功能 | 操作系统 | 所需软件 | 池设置 |
| -------- | ------ | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2 或<br/>Windows Server 2012 (Microsoft Azure Marketplace) | Microsoft MPI 2012 R2 或更高版本，或<br/> Intel MPI 5<br/><br/>HpcVMDrivers Azure VM 扩展 | 启用节点间通信，禁用并发任务执行 |
| [NC 系列*](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla K80 GPU | Windows Server 2016 或 <br/>Windows Server 2012 R2 (Microsoft Azure Marketplace) | NVIDIA Tesla 驱动程序或 CUDA Toolkit 9.0 驱动程序| 不适用 | 
| [NV 系列](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 或<br/>Windows Server 2012 R2 (Microsoft Azure Marketplace) | NVIDIA GRID 4.3 驱动程序 | 不适用 |

*具备 HpcVMDrivers 扩展和 Microsoft MPI/Intel MPI 的 Windows Server 2012 R2（来自 Azure Marketplace）支持在 NC24r VM 上连接 RDMA。

### <a name="windows-pools---cloud-services-configuration"></a>Windows 池 - 云服务配置

> [!NOTE]
> 具备云服务配置的 Batch 池不支持 N 系列大小。
>

| 大小 | 功能 | 操作系统 | 所需软件 | 池设置 |
| -------- | ------- | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2、<br/>Windows Server 2012 或<br/>Windows Server 2008 R2（来宾 OS 系列） | Microsoft MPI 2012 R2 或更高版本，或<br/>Intel MPI 5<br/><br/>HpcVMDrivers Azure VM 扩展 | 启用节点间通信，<br/> 禁用并发任务执行 |





## <a name="pool-configuration-options"></a>池配置选项

在为 Batch 池配置专用 VM 大小时，Batch API 和工具提供若干选项来安装所需软件或驱动程序，包括：

* [启动任务](batch-api-basics.md#start-task) - 将安装程序包以资源文件形式上传到所在区域与 Batch 帐户相同的 Azure 存储帐户。 创建一个启动任务命令行，以在池启动时静默安装该资源文件。 有关详细信息，请参阅 [REST API 文档](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask)。

  > [!NOTE] 
  > 启动任务必须使用提升的（管理员）权限运行，且必须待其运行成功。
  >

* [应用程序包](batch-application-packages.md) - 将压缩的安装程序包添加到 Batch 帐户，然后在池中配置程序包引用。 此设置可将程序包上传到池的所有节点并解压。 如果程序包是安装程序，请创建一个启动任务命令行，以在所有池节点上静默安装该应用。 也可以在节点运行计划的任务时安装程序包。

* [自定义池映像](batch-custom-images.md) - 创建包含 VM 大小所需的驱动程序、软件或其他设置的自定义 Windows 或 Linux VM 映像。 

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) 将自动配置 GPU 和 RDMA，以便透明地用于 Azure Batch 上的容器化工作负荷。 Batch Shipyard 完全由配置文件驱动。 提供众多的示例配方配置来启用 GPU 和 RDMA 工作负荷，例如 [CNTK GPU 配方](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI)，它可在 N 系列 VM 上预先配置 GPU 驱动程序，并以 Docker 映像形式加载 Microsoft Cognitive Toolkit 软件。


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>示例：A8 VM 池上的 Microsoft MPI

若要在 Azure A8 节点的池上运行 Windows MPI 应用程序，需要安装支持的 MPI 实现。 以下是使用 Batch 应用程序包在 Windows 池上安装 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) 的示例步骤。

1. 下载最新版 Microsoft MPI 的[安装程序包](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe)。
2. 为程序包创建 zip 文件。
3. 将程序包上载到 Batch 帐户。 有关详细步骤，请参阅[应用程序包](batch-application-packages.md)指南。 指定应用程序 ID（如 MSMPI）和版本（如 8.1）。 
4. 通过 Batch API 或 Azure 门户，在云服务配置中创建具有所需节点数和规模的池。 下表列出了在无人参与模式下使用启动任务设置 MPI 的示例设置：

| 设置 | 值 |
| ---- | ----- | 
| **映像类型** | 云服务 |
| **OS 系列** | Windows Server 2012 R2（OS 系列 4） |
| **节点大小** | A8 标准 |
| **启用了节点间通信** | True |
| **每个节点的任务上限** | 1 |
| **应用程序包引用** | MSMPI |
| **启用了启动任务** | True<br>**命令行** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**用户标识** - 池自动用户、管理员<br/>**等待成功** - True

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>示例：NC VM 池上的 NVIDIA Tesla 驱动程序

若要在 Linux NC 节点的池上运行 CUDA 应用程序，需要在节点上安装 CUDA Toolkit 9.0。 该工具包安装了所需的 NVIDIA Tesla GPU 驱动程序。 以下是使用 GPU 驱动程序部署自定义 Ubuntu 16.04 LTS 映像的示例步骤：

1. 部署运行 Ubuntu 16.04 LTS 的 Azure NC6 VM。 例如：在美国中南部区域创建 VM。 请确保创建具有托管磁盘的 VM。
2. 请照以下步骤连接 VM 并[安装 CUDA 驱动程序](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-ncv2-and-nd-vms)。
3. 取消设置 Linux 代理，然后[捕获 Linux VM 映像](../virtual-machines/linux/capture-image.md)。
4. 在支持 NC VM 的区域中创建 Batch 帐户。
5. 通过 Batch API 或 Azure 门户，[使用自定义映像](batch-custom-images.md)创建具有所需节点数和规模的池。 下表列出了映像的示例池设置：

| 设置 | 值 |
| ---- | ---- |
| **映像类型** | 自定义映像 |
| **自定义映像** | 映像名称 |
| **节点代理 SKU** | batch.node.ubuntu 16.04 |
| **节点大小** | NC6 标准 |



## <a name="next-steps"></a>后续步骤

* 若要在 Azure Batch 池上运行 MPI 作业，请参阅 [Windows](batch-mpi.md) 或 [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) 示例。

* 有关 Batch 上的 GPU 工作负荷示例，请参阅 [Batch Shipyard](https://github.com/Azure/batch-shipyard/) 配方。
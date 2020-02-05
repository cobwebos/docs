---
title: 将计算密集型 Azure Vm 与 Batch 配合使用
description: 如何利用 Azure Batch 池中的 HPC 和 GPU 虚拟机大小。 了解 OS 依赖关系，并查看几个方案示例。
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/17/2018
ms.author: labrenne
ms.openlocfilehash: 17cd9db1e86a66630d3b93c8295915933abb1ec2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023593"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>在 Batch 池中使用 RDMA 或 GPU 实例

若要运行某些 Batch 作业，可以利用专为大规模计算设计的 Azure VM 大小。 例如：

* 运行多实例 [MPI 工作负荷](batch-mpi.md)时，可为远程直接内存访问 (RDMA) 选择具备网络接口的 H 系列或其他大小。 这些大小均连接到用于节点间通信的 InfiniBand 网络，可加快 MPI 应用程序的速度。 

* 对于 CUDA 应用程序，可选择包含 NVIDIA Tesla 图形处理单元 (GPU) 卡的 N 系列大小。

本文介绍了在 Batch 池中使用 Azure 的某些专用大小的指南和示例。 有关规格和背景的信息，请参阅：

* 高性能计算 VM 大小（[Linux](../virtual-machines/linux/sizes-hpc.md)、[Windows](../virtual-machines/windows/sizes-hpc.md)） 

* 启用 GPU 的 VM 大小（[Linux](../virtual-machines/linux/sizes-gpu.md)、[Windows](../virtual-machines/windows/sizes-gpu.md)） 

> [!NOTE]
> 某些 VM 大小在创建批处理帐户的区域中可能无法使用。 若要检查大小是否可用，请参阅[可用产品（按区域）](https://azure.microsoft.com/regions/services/)以及[选择 Batch 池的 VM 大小](batch-pool-vm-sizes.md)。

## <a name="dependencies"></a>依赖项

Batch 中计算密集型大小的 RDMA 或 GPU 功能仅在某些操作系统中支持。 （支持的操作系统的列表是在这些大小中创建的虚拟机支持的操作系统的一个子集。）你可能需要在节点上安装或配置其他驱动程序或其他软件，具体取决于你创建 Batch 池的方式。 下表总结了这些依存关系。 有关详细信息，请参阅链接的文章。 有关配置 Batch 池的选项，请参阅本文后面部分。

### <a name="linux-pools---virtual-machine-configuration"></a>Linux 池 - 虚拟机配置

| 大小 | 功能 | 操作系统 | 所需软件 | 池设置 |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r、NC24rs_v2、NC24rs_v3、ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS 或<br/>基于 CentO 的 HPC<br/>（Azure 市场） | Intel MPI 5<br/><br/>Linux RDMA 驱动程序 | 启用节点间通信，禁用并发任务执行 |
| [NC、NCv2、NCv3、NDv2 系列](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU（因系列而异） | Ubuntu 16.04 LTS 或<br/>CentOS 7.3 或 7.4<br/>（Azure 市场） | NVIDIA CUDA 或 CUDA Toolkit 驱动程序 | N/A | 
| [NV、NVv2 系列](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS 或<br/>CentOS 7.3<br/>（Azure 市场） | NVIDIA GRID 驱动程序 | N/A |

<sup>*</sup>支持 RDMA 的 N 系列大小还包含 NVIDIA Tesla GPU

### <a name="windows-pools---virtual-machine-configuration"></a>Windows 池 - 虚拟机配置

| 大小 | 功能 | 操作系统 | 所需软件 | 池设置 |
| -------- | ------ | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r、NC24rs_v2、NC24rs_v3、ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016、2012 R2 或<br/>2012（Azure 市场） | Microsoft MPI 2012 R2 或更高版本，或<br/> Intel MPI 5<br/><br/>Windows RDMA 驱动程序 | 启用节点间通信，禁用并发任务执行 |
| [NC、NCv2、NCv3、ND、NDv2 系列](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU（因系列而异） | Windows Server 2016 或 <br/>2012 R2（Azure 市场） | NVIDIA CUDA 或 CUDA Toolkit 驱动程序| N/A | 
| [NV、NVv2 系列](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 或<br/>2012 R2（Azure 市场） | NVIDIA GRID 驱动程序 | N/A |

<sup>*</sup>支持 RDMA 的 N 系列大小还包含 NVIDIA Tesla GPU

### <a name="windows-pools---cloud-services-configuration"></a>Windows 池 - 云服务配置

> [!NOTE]
> 具有云服务配置的 Batch 池不支持 N 系列大小。
>

| 大小 | 功能 | 操作系统 | 所需软件 | 池设置 |
| -------- | ------- | -------- | -------- | ----- |
| [H16r、H16mr、A8、A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016、2012 R2、2012 或<br/>2008 R2（来宾 OS 系列） | Microsoft MPI 2012 R2 或更高版本，或<br/>Intel MPI 5<br/><br/>Windows RDMA 驱动程序 | 启用节点间通信，<br/> 禁用并发任务执行 |

## <a name="pool-configuration-options"></a>池配置选项

在为 Batch 池配置专用 VM 大小时，有若干选项来安装所需软件或驱动程序：

* 对于虚拟机配置中的池，请选择预先配置的 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/) VM 映像，其中包含预安装的驱动程序和软件。 示例： 

  * [基于 CentOS 的 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) - 包括 RDMA 驱动程序和 Intel MPI 5.1

  * 适用于 Linux 或 Windows 的 [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) - 包括 NVIDIA CUDA 驱动程序

  * 适用于 Batch 容器工作负载的 Linux 映像，还包括 GPU 和 RDMA 驱动程序：

    * [适用于 Azure Batch 容器池的 CentOS（包含 GPU 和 RDMA 驱动程序）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [适用于 Azure Batch 容器池的 Ubuntu 服务器（包含 GPU 和 RDMA 驱动程序）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* 创建[自定义 Windows 或 Linux VM 映像](batch-sig-images.md)，你已在其上安装了 VM 大小所需的驱动程序、软件或其他设置。 

* 从已压缩的驱动程序或应用程序安装程序创建 Batch [应用程序包](batch-application-packages.md)，并配置 Batch 以将程序包部署到池节点，并在创建每个节点时安装一次。 例如，如果应用程序包是安装程序，请创建一个[启动任务](batch-api-basics.md#start-task)命令行，以在所有池节点上静默安装该应用。 若工作负载取决于特定的驱动程序版本，请考虑使用应用程序包和池启动任务。

  > [!NOTE] 
  > 启动任务必须使用提升的（管理员）权限运行，且必须待其运行成功。 长时间运行的任务将增加配置 Batch 池的时间。
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) 将自动配置 GPU 和 RDMA 驱动程序，以便透明地用于 Azure Batch 上的容器化工作负荷。 Batch Shipyard 完全由配置文件驱动。 提供众多的示例配方配置来启用 GPU 和 RDMA 工作负荷，例如 [CNTK GPU 配方](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI)，它可在 N 系列 VM 上预先配置 GPU 驱动程序，并以 Docker 映像形式加载 Microsoft Cognitive Toolkit 软件。


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>示例： Windows NC VM 池上的 NVIDIA GPU 驱动程序

若要在 Windows NC 节点的池上运行 CUDA 应用程序，需要安装 NVDIA GPU 驱动程序。 以下示例步骤使用应用程序包来安装 NVIDIA GPU 驱动程序。 如果工作负载取决于特定的 GPU 驱动程序版本，则可以选择此选项。

1. 从 [NVIDIA 网站](https://www.nvidia.com/Download/index.aspx)下载 Windows Server 2016 上的 GPU 驱动程序的安装程序包 - 例如，[版本 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe)。 使用短名称（如 GPUDriverSetup.exe）在本地保存文件。
2. 为程序包创建 zip 文件。
3. 将程序包上载到 Batch 帐户。 有关详细步骤，请参阅[应用程序包](batch-application-packages.md)指南。 指定应用程序 ID（如 GPUDriver）和版本（如 411.82）。
1. 通过 Batch API 或 Azure 门户，在虚拟机配置中创建具有所需节点数和规模的池。 下表显示了使用启动任务静默安装 NVIDIA GPU 驱动程序的示例设置：

| 设置 | 值 |
| ---- | ----- | 
| **映像类型** | 市场 (Linux/Windows) |
| **发布者** | MicrosoftWindowsServer |
| **产品** | WindowsServer |
| **Sku** | 2016-Datacenter |
| **节点大小** | NC6 标准 |
| **应用程序包引用** | GPUDriver，版本 411.82 |
| **启用了启动任务** | 正确<br>**命令行** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**用户标识** - 池自动用户、管理员<br/>**等待成功** - True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>示例： Linux NC VM 池中的 NVIDIA GPU 驱动程序

若要在 Linux NC 节点的池上运行 CUDA 应用程序，需要从 CUDA Toolkit 安装必要的 NVIDIA Tesla GPU 驱动程序。 以下示例步骤使用 GPU 驱动程序创建和部署自定义 Ubuntu 16.04 LTS 映像：

1. 部署运行 Ubuntu 16.04 LTS 的 Azure NC 系列 VM。 例如：在美国中南部区域创建 VM。 
2. 使用 Azure 门户、连接到 Azure 订阅的客户端计算机或 Azure Cloud Shell 将 [NVIDIA GPU 驱动程序扩展](../virtual-machines/extensions/hpccompute-gpu-linux.md
)添加到 VM。 或者，按照以下步骤连接到 VM 并手动[安装 CUDA 驱动程序](../virtual-machines/linux/n-series-driver-setup.md)。
3. 按照以下步骤创建用于批处理的[共享映像库映像](batch-sig-images.md)。
4. 在支持 NC VM 的区域中创建 Batch 帐户。
5. 通过 Batch API 或 Azure 门户，[使用自定义映像](batch-sig-images.md)创建具有所需节点数和规模的池。 下表列出了映像的示例池设置：

| 设置 | 值 |
| ---- | ---- |
| **映像类型** | 自定义映像 |
| **自定义映像** | *映像名称* |
| **节点代理 SKU** | batch.node.ubuntu 16.04 |
| **节点大小** | NC6 标准 |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>示例： Windows H16r VM 池上的 Microsoft MPI

要在 Azure H16r VM 节点的池上运行 Windows MPI 应用程序，需要配置 HpcVmDrivers 扩展并安装 [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi)。 以下是使用必要的驱动程序和软件部署自定义 Windows Server 2016 映像的示例步骤：

1. 部署运行 Windows Server 2016 的 Azure H16r VM。 例如，在美国西部区域创建 VM。 
2. 通过从连接到 Azure 订阅的客户端计算机[运行 Azure PowerShell 命令](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
)或使用 Azure Cloud Shell，将 HpcVmDrivers 扩展添加到 VM。 
1. 远程桌面连接到 VM。
1. 下载最新版 Microsoft MPI 的[安装程序包](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) 并安装 Microsoft MPI。
1. 按照以下步骤创建用于批处理的[共享映像库映像](batch-sig-images.md)。
1. 使用 Batch Api 或 Azure 门户，[使用共享映像库](batch-sig-images.md)以及所需的节点数和缩放级别来创建池。 下表列出了映像的示例池设置：

| 设置 | 值 |
| ---- | ---- |
| **映像类型** | 自定义映像 |
| **自定义映像** | *映像名称* |
| **节点代理 SKU** | batch.node.windows amd64 |
| **节点大小** | H16r 标准 |
| **启用了节点间通信** | 正确 |
| **每个节点的任务上限** | 第 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>示例： Linux H16r VM 池上的 Intel MPI

要在 Linux H 系列节点的池上运行 MPI 应用程序，一种选择是使用 Azure 市场中[基于 CentOS 的 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) 映像。 预装了 Linux RDMA 驱动程序和 Intel MPI。 此映像还支持 Docker 容器工作负载。

通过 Batch API 或 Azure 门户，使用该映像创建具有所需节点数和规模的池。 下表列出了示例池设置：

| 设置 | 值 |
| ---- | ---- |
| **映像类型** | 市场 (Linux/Windows) |
| **发布者** | OpenLogic |
| **产品** | CentOS-HPC |
| **Sku** | 7.4 |
| **节点大小** | H16r 标准 |
| **启用了节点间通信** | 正确 |
| **每个节点的任务上限** | 第 |

## <a name="next-steps"></a>后续步骤

* 若要在 Azure Batch 池上运行 MPI 作业，请参阅 [Windows](batch-mpi.md) 或 [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) 示例。

* 有关 Batch 上的 GPU 工作负荷示例，请参阅 [Batch Shipyard](https://github.com/Azure/batch-shipyard/) 配方。
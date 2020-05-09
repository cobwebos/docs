---
title: Azure VM 大小-GPU |Microsoft Docs
description: 列出了 Azure 中虚拟机可用的不同 GPU 优化大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5d36ba05d2138a06ebb2ef4e49aadb6032b62b92
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627035"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 优化虚拟机大小

GPU 优化 VM 大小是专用虚拟机，可以使用单个、多个或部分 Gpu。 这些大小是针对计算密集型、图形密集型和可视化工作负荷设计的。 本文介绍有关 GPU、vCPU、数据磁盘和 NIC 的数量和类型的信息。 此分组中的每个大小还包括存储吞吐量及网络带宽。

- [NC 系列](nc-series.md)、 [NCv2 系列](ncv2-series.md)、 [NCv3 系列](ncv3-series.md)大小针对计算密集型和网络密集型应用程序和算法进行了优化。 一些示例包括基于 CUDA 和 OpenCL 的应用程序以及模拟、AI 和深度学习。 NCv3 系列专用于采用 NVIDIA Tesla V100 GPU 的高性能计算工作负载。 NC 系列使用 Intel 2690 v3 2.60 GHz v3 （Haswell）处理器，NCv2 系列和 NCv3 系列 Vm 使用 Intel 至强 E5-2690 v4 （Broadwell）处理器。

- [ND 系列](nd-series.md)和[NDv2 系列](ndv2-series.md)的大小侧重于定型和推理方案，以便进行深入学习。 它们使用 NVIDIA Tesla P40 GPU 和 Intel 2690 v4 （Broadwell）处理器。 NDv2 系列使用 Intel 强白金8168（Skylake）处理器。

- [NV 系列](nv-series.md)和[NVv3 系列](nvv3-series.md)大小经过优化，适用于使用 OpenGL 和 DirectX 等框架的远程可视化、流式处理、游戏、编码和 VDI 方案。 这些 VM 由 NVIDIA Tesla M60 GPU 提供支持。

- [NVv4 系列](nvv4-series.md)VM 大小经过优化，适用于 VDI 和远程可视化。 对于分区 Gpu，NVv4 为需要较小 GPU 资源的工作负荷提供适当的大小。 这些 Vm 由 AMD Radeon Instinct MI25 GPU 支持。 NVv4 Vm 当前仅支持 Windows 来宾操作系统。

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用 Azure N 系列 Vm 的 GPU 功能，必须安装 NVIDIA 或 AMD GPU 驱动程序。

- 对于 NVIDIA Gpu 支持的 Vm， [NVIDIA Gpu 驱动程序扩展](/azure/virtual-machines/extensions/hpccompute-gpu-windows)会安装适当的 nvidia CUDA 或网格驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的操作系统和部署步骤，请参阅 [NVIDIA GPU 驱动程序扩展文档](/azure/virtual-machines/extensions/hpccompute-gpu-windows)。 有关 VM 扩展的常规信息，请参阅 [Azure 虚拟机扩展和功能](/azure/virtual-machines/extensions/overview)。

   或者，你可以手动安装 NVIDIA GPU 驱动程序。 请参阅[在运行 Windows 的 n 系列 vm 上安装 NVIDIA gpu 驱动程序](/azure/virtual-machines/windows/n-series-driver-setup)或[在运行 Linux 的 n 系列 VM 上安装 nvidia gpu 驱动](/azure/virtual-machines/linux/n-series-driver-setup)程序，以支持操作系统、驱动程序、安装和验证步骤。

- 对于 AMD Gpu 支持的 Vm，请参阅[在运行 Windows 的 N 系列 vm 上安装 AMD GPU 驱动程序](/azure/virtual-machines/windows/n-series-amd-driver-setup)以获取支持的操作系统、驱动程序、安装和验证步骤。

## <a name="deployment-considerations"></a>部署注意事项

- 有关 N 系列 VM 的可用性，请查看[可用产品(按区域)](https://azure.microsoft.com/regions/services/)。

- N 系列 VM 只能按 Resource Manager 部署模型部署。

- N 系列的 VM 在对其磁盘支持的 Azure 存储类型方面有所不同。 NC 和 NV VM 仅支持标准磁盘存储 (HDD) 所支持的 VM 磁盘。 NCv2、NCv3、ND、NDv2 和 NVv2 VM 仅支持高级磁盘存储 (SSD) 所支持的 VM 磁盘。

- 如果需要部署的 N 系列 VM 较多，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

- 可能需要提高 Azure 订阅中的核心配额（按区域）以及单独针对 NC、NCv2、NCv3、ND、NDv2、NV 或 NVv2 核心的配额。 若要请求增加配额，请免费[打开联机客户支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 默认限制可能因订阅类别而异。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [高性能计算](sizes-hpc.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。

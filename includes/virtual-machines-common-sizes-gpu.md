---
title: include 文件
description: include 文件
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 402379a99be467e9db7fb692d7d532e9d39a54a6
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700781"
---
GPU 优化 VM 大小是具有单个或多个 NVIDIA GPU 的专用虚拟机。 这些大小是针对计算密集型、图形密集型和可视化工作负荷设计的。 本文介绍有关 GPU、vCPU、数据磁盘和 NIC 的数量和类型的信息。 此分组中的每个大小还包括存储吞吐量及网络带宽。

* **NC、NCv2、NCv3**大小针对计算密集型和网络密集型应用程序和算法进行了优化。 一些示例包括基于 CUDA 和 OpenCL 的应用程序和模拟、AI 和深度学习。 NCv3 系列专用于采用 NVIDIA Tesla V100 GPU 的高性能计算工作负载。 NC 系列使用 Intel 2690 v3 2.60 GHz v3 (Haswell) 处理器, NCv2 系列和 NCv3 系列 Vm 使用 Intel 至强 E5-2690 v4 (Broadwell) 处理器。

* **ND 和 NDv2**ND 系列重点介绍用于深度学习的定型和推理方案。 它使用 NVIDIA Tesla P40 GPU 和 Intel 2690 v4 (Broadwell) 处理器。 NDv2 系列使用 Intel 强白金 8168 (Skylake) 处理器。

* **NV 和 NVv3**大小经过优化, 适用于使用 OpenGL 和 DirectX 等框架的远程可视化、流式处理、游戏、编码和 VDI 方案。  这些 VM 由 NVIDIA Tesla M60 GPU 提供支持。

## <a name="nc-series"></a>NC 系列

高级存储：不受支持

高级存储缓存：不受支持

NC 系列 Vm 由[NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf)卡和 Intel 2690 E5-V3 (Haswell) 处理器提供支持。 通过将 CUDA 用于能源勘探应用、碰撞模拟、光纤跟踪渲染、深度学习等领域，用户可以更快地分析数据。 NC24r 配置提供了针对紧密耦合的并行计算工作负荷优化的低延迟、高吞吐量网络接口。

| Size | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = 半块 K80 卡。

*支持 RDMA

## <a name="ncv2-series"></a>NCv2 系列

高级存储：支持

高级存储缓存：支持

NCv2 系列 VM 采用 [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) GPU。 这些 GPU 可提供比 NC 系列高 2 倍以上的计算性能。 客户可将这些更新的 GPU 用于传统的 HPC 工作负荷，例如油藏模拟、DNA 测序、蛋白质分析、Monte Carlo 模拟和其他工作负荷。 除了 Gpu 外, NCv2 系列 Vm 还由 Intel 强 2690 v4 (Broadwell) Cpu 提供支持。

NC24rs v2 配置提供了针对紧密耦合的并行计算工作负荷优化的低延迟、高吞吐量网络接口。

> [!IMPORTANT]
> 对于此大小系列，订阅中的 vCPU（核心）配额在每个区域中最初都设置为 0。 可以请求在某个[可用区域](https://azure.microsoft.com/regions/services/)中[提高此系列的 vCPU 配额](../articles/azure-supportability/resource-manager-core-quotas-request.md)。
>

| Size | vCPU | 内存：GiB | 临时存储 (SSD):GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 GPU = 一个 P100 卡。

*支持 RDMA

## <a name="ncv3-series"></a>NCv3 系列

高级存储：支持

高级存储缓存：支持

NCv3 系列 VM 采用 [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) GPU。 这些 GPU 可提供 NCv2 系列的 1.5 倍计算性能。 客户可将这些更新的 GPU 用于传统的 HPC 工作负荷，例如油藏模拟、DNA 测序、蛋白质分析、Monte Carlo 模拟和其他工作负荷。 NC24rs v3 配置提供了针对紧密耦合的并行计算工作负荷优化的低延迟、高吞吐量网络接口。 除了 Gpu 外, NCv3 系列 Vm 还由 Intel 强 2690 v4 (Broadwell) Cpu 提供支持。

> [!IMPORTANT]
> 对于此大小系列，订阅中的 vCPU（核心）配额在每个区域中最初都设置为 0。 可以请求在某个[可用区域](https://azure.microsoft.com/regions/services/)中[提高此系列的 vCPU 配额](../articles/azure-supportability/resource-manager-core-quotas-request.md)。
>

| Size | vCPU | 内存：GiB | 临时存储 (SSD):GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000 / 200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 GPU = 一个 V100 卡。

*支持 RDMA

## <a name="ndv2-series-preview"></a>NDv2 系列（预览）

高级存储：支持

高级存储缓存：支持

Infiniband：不支持

NDv2 系列虚拟机是 GPU 系列的新成员，经过专门设计以满足 HPC、AI 和机器学习工作负荷方面的需求。 它由8个 NVIDIA Tesla V100 NVLINK 互连 Gpu 和 40 Intel 强白金 8168 (Skylake) 核心和 672 GiB 系统内存提供支持。 NDv2 实例为利用 Cuda、TensorFlow、Pytorch、Caffe 的 HPC 和 AI 以及其他框架提供卓越的 FP32 和 FP64 性能。

[注册并在预览期访问这些虚拟机](https://aka.ms/ndv2signup)。
<br>

| Size | vCPU | 内存：GiB | 临时存储 (SSD):GiB | GPU | GPU 内存:GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大网络带宽 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40s_v2 | 40 | 672 | 2948 | 8 V100 (NVLink) | 16 | 32 | 80000 / 800 | 24000 Mbps | 8 |

## <a name="nd-series"></a>ND 系列

高级存储：支持

高级存储缓存：支持

ND 系列虚拟机是针对 AI 和深度学习工作负荷设计的 GPU 系列的新成员。 它们在训练和推理方面性能卓越。 ND 实例由[NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) Gpu 和 Intel 2690 V4 (Broadwell) cpu 提供支持。 这些实例可以针对单精度浮点运算和利用 Microsoft 认知工具包、TensorFlow、Caffe 及其他框架的 AI 工作负荷提供卓越的性能。 ND 系列还提供了更大的 GPU 内存大小（24 GB），能够适应更大的神经网络模型。 与 NC 系列一样，ND 系列可通过 RDMA 和 InfiniBand 连接提供含辅助型低延迟、高吞吐量网络的配置，以便可运行跨多个 GPU 的大规模训练作业。

> [!IMPORTANT]
> 对于此大小系列，订阅中每个区域的 vCPU（核心）配额最初都设置为 0。 可以请求在某个[可用区域](https://azure.microsoft.com/regions/services/)中[提高此系列的 vCPU 配额](../articles/azure-supportability/resource-manager-core-quotas-request.md)。
>

| Size | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000 / 200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000 / 400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |

1 GPU = 一个 P40 卡。

*支持 RDMA

## <a name="nv-series"></a>NV 系列

高级存储：不受支持

高级存储缓存：不受支持

NV 系列虚拟机采用 [NVIDIA Tesla M60 ](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 和 NVIDIA GRID 技术，适用于桌面加速型应用程序和虚拟桌面，方便客户将其数据或模拟可视化。 用户可以在 NV 实例上直观显示其图形密集型工作流以获取高级图形功能，并可额外运行单精度工作负荷，例如编码和渲染。 NV 系列 Vm 还由 Intel 2690 v3 (Haswell) Cpu 提供支持。

NV 实例中的每个 GPU 都带有 GRID 许可证。 使用此许可证，可以灵活地将 NV 实例用作单个用户的虚拟工作站，或将 25 个并发用户都连接到用于虚拟应用程序方案的 VM。

| Size | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 | 虚拟工作站 | 虚拟应用程序 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = 半块 M60 卡。

## <a name="nvv3-series--sup1sup"></a>NVv3 系列<sup>1</sup>

高级存储：支持

高级存储缓存：支持

NVv3 系列虚拟机由[NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) gpu 和带有 Intel E5-2690 V4 (Broadwell) CPU 的 nvidia 网格技术提供支持。 此类虚拟机面向 GPU 加速图形应用程序和虚拟桌面，客户希望利用这些应用和桌面直观呈现数据、模拟要查看的结果、处理 CAD 或渲染和流式处理内容。 此外，这些虚拟机还能运行编码和渲染等单精度工作负荷。 NVv3 虚拟机支持高级存储, 并在与其前置 NV 系列进行比较时提供系统内存 (RAM) 的两倍。  

NVv3 实例中的每个 GPU 都附带了网格许可证。 使用此许可证，可以灵活地将 NV 实例用作单个用户的虚拟工作站，或将 25 个并发用户都连接到用于虚拟应用程序方案的 VM。

| Size | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数 | 虚拟工作站 | 虚拟应用程序 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000 / 200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000 / 400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000 / 800 | 8 | 4 | 100 |

1 GPU = 半块 M60 卡。

<sup>1</sup> NVv3 系列 Vm 功能 Intel 超线程技术

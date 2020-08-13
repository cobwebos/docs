---
title: NCas T4 v3 系列
description: NCas T4 v3 系列 Vm 的规格。
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: d07da12ecef7dfc6cf1a6df67f6beae01c4573d9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168110"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 系列 

NCasT4_v3 系列虚拟机由[Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU 和 AMD EPYC 7V12 提供支持 (罗马) cpu。 Vm 的功能最多支持4个 NVIDIA T4 Gpu，每个内存有 16 GB 内存，最多64个非多线程 AMD EPYC 7V12 (罗马) 处理器核心，440 GiB 的系统内存。 这些虚拟机非常适合用 CUDA、TensorFlow、Pytorch、Caffe 和其他框架或使用 NVIDIA 网格技术的图形工作负荷运行 ML 和 AI 工作负荷。 NCasT4_v3 系列非常适合用于运行推理工作负荷。

<br>

ACU：230-260

高级存储：支持

高级存储缓存：支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NV8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NV16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NV64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用运行 Windows 或 Linux 的 Azure NCasT4_v3 系列 Vm 的 GPU 功能，必须安装 Nvidia GPU 驱动程序。

若要手动安装 Nvidia GPU 驱动程序，请参阅适用于[Windows 的 N 系列 gpu 驱动程序设置](./windows/n-series-driver-setup.md)以获取支持的操作系统、驱动程序、安装和验证步骤。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。

---
title: Av2 系列-Azure 虚拟机
description: Av2 系列 Vm 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 1e14c633ed11bb628a68756e72e78073603643a1
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493847"
---
# <a name="av2-series"></a>Av2 系列

Av2 系列 VM 可以部署在各种不同的硬件类型和处理器上。 Av2 系列 Vm 具有最适合于入门级工作负荷（如开发和测试）的 CPU 性能和内存配置。 大小被限制为为正在运行的实例提供一致的处理器性能，而不考虑部署它的硬件。 若要判断此大小部署所在的物理硬件，请从虚拟机中查询虚拟硬件。 一些示例用例包括开发和测试服务器、低流量 web 服务器、小型到中型数据库、概念证明和代码存储库。

ACU：100

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量： IOPS/读取 MBps/写入 MBps | 最大数据磁盘/吞吐量： IOPS | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2/250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
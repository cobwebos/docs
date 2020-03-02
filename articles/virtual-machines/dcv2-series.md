---
title: DC 系列-Azure 虚拟机
description: DC 系列 Vm 的规格。
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: fd8f01f0fc7180d271404ffee4496ff9cbac7222
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205919"
---
# <a name="preview-dcv2-series"></a>预览版： DCv2 系列


在公有云中处理数据和代码时，DCv2 系列可帮助保护数据和代码的机密性和完整性。 这些计算机由最新的具有 SGX 技术的最新的 Intel 2288G 处理器生成提供支持。 利用 Intel Turbo 提升技术，这些计算机可以高达 5.0 GHz。 DCv2 系列实例使客户能够构建安全的基于 enclave 的应用程序，以在使用时保护其代码和数据。

示例用例包括机密多方数据共享、欺诈检测、反金钱 laundering、区块链、机密使用情况分析、智能分析和机密机器学习。

高级存储：支持 *

高级存储缓存：支持 *

实时迁移：不支持

内存保留更新：不支持

\* Standard_DC8_v2 除外



| 大小             | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 Nic 数/预期网络带宽（MBps） |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 （21）                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 （43）                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 （86）                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 （172）                                                         | 12800/192                                 | 2                                            |

- DCv2 系列 Vm 是[第2代 vm](./linux/generation-2.md#creating-a-generation-2-vm) ，仅支持 `Gen2` 映像。
- 目前只能在英国南部中使用。
- 上一代机密计算 Vm： [DC 系列](sizes-previous-gen.md)
- 使用 Azure 门户创建 DCv2 Vm[创建 vm-门户](./linux/quick-create-portal.md)



## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。

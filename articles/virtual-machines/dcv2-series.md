---
title: DC 系列-Azure 虚拟机
description: DC 系列 Vm 的规格。
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 431c1b1211165f43feb7fe1f93c73c2bf141e004
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871815"
---
# <a name="dcsv2-series"></a>DCsv2 系列


在公有云中处理数据和代码时，DCsv2 系列可帮助保护数据和代码的机密性和完整性。 这些计算机由最新的具有 SGX 技术的最新的 Intel 2288G 处理器生成提供支持。 利用 Intel Turbo 提升技术，这些计算机可以高达 5.0 GHz。 DCsv2 系列实例使客户能够构建安全的基于 enclave 的应用程序，以在使用时保护其代码和数据。

示例用例包括：机密多方数据共享、欺诈检测、反金钱 laundering、区块链、机密使用情况分析、智能分析和机密机器学习。

高级存储：支持 *

高级存储缓存：支持 *

实时迁移：不支持

内存保留更新：不支持

* Standard_DC8_v2 除外



| 大小             | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 Nic 数/预期网络带宽（MBps） | EPC 内存（MiB） |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 （21）                                                            | 1600/24                                   | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 （43）                                                            | 3200/48                                   | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 （86）                                                            | 6400/96                                   | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 （172）                                                         | 12800/192                                 | 2   | 168                                         |

- DCsv2 系列 Vm 是[第2代 vm](./linux/generation-2.md#creating-a-generation-2-vm) ，仅支持`Gen2`映像。
- 目前仅适用于英国南部、加拿大中部和美国东部。
- 上一代机密计算 Vm： [DC 系列](sizes-previous-gen.md#preview-dc-series)
- 使用[Azure 门户](./linux/quick-create-portal.md)或[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)创建 DCsv2 vm



## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。

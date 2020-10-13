---
title: DC 系列 - Azure 虚拟机
description: DC 系列 VM 的规格。
author: susaxen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 1809194ce551fc828389cc31f5c7d6f28a2cddd6
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972367"
---
# <a name="dcsv2-series"></a>DCsv2 系列


DCsv2 系列可以在公有云中处理数据和代码时帮助保护数据和代码的机密性和完整性。 这些虚拟机由最新一代采用 SGX 技术的 Intel XEON E-2288G 处理器提供支持。 借助 Intel Turbo Boost Technology，这些虚拟机最高可以达到 5.0GHz。 客户使用 DCsv2 系列实例可以构建安全的基于 enclave 的应用程序，目的是在使用该系列时保护其代码和数据。

示例用例包括：多方机密数据共享、欺诈检测、反洗钱、区块链、机密使用情况分析、情报分析和机密机器学习。

[高级存储](premium-storage-performance.md)：支持 *<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 生成支持](generation-2.md)：第1代和第2代<br>

*Standard_DC8_v2 除外

| 大小             | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大 NIC 数/预期网络带宽 (MBps) | EPC 内存 (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- DCsv2 系列 VM 是[第 2 代 VM](./generation-2.md#creating-a-generation-2-vm)，仅支持 `Gen2` 映像。
- 当前在 [此处](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)列出的区域中可用。
- 上一代机密计算 VM：[DC 系列](sizes-previous-gen.md#preview-dc-series)
- 使用 [Azure 门户](./linux/quick-create-portal.md)或 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)创建 DCsv2 VM



## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器：[定价计算器](https://azure.microsoft.com/pricing/calculator/)

有关磁盘类型的详细信息：[磁盘类型](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
---
title: HC 系列 VM 大小性能
description: 了解 Azure 中 HC 系列 VM 大小的性能测试结果。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.openlocfilehash: 0d63d9770dacf6a200e8b81e8d47d9f807a8a448
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603414"
---
# <a name="hc-series-virtual-machine-sizes"></a>HC 系列虚拟机大小

已对 HC 系列大小运行多个性能测试。 下面是此性能测试的部分结果。

| 工作负荷                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | 190 GB/s (Intel LIP.MLC AVX-512)   |
| 高性能 Linpack (HPL.DAT)                   | 3520 GigaFLOPS (Rpeak) ，2970 GigaFLOPS (Rmax)  |
| RDMA 延迟 & 带宽                        | 1.05 微秒，96.8 Gb/秒   |
| 本地 NVMe SSD 上的 FIO                           | 1.3 GB/s 读取，900 MB/秒写入 |  
| IOR on 4 Azure 高级 SSD (P30 托管磁盘，RAID0) * *  | 780 MB/s 读取，780 MB/写入 |

## <a name="mpi-latency"></a>MPI 延迟

运行 OSU microbenchmark suite 的 MPI 延迟测试。 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)上的示例脚本

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="Azure HC 上的 MPI 延迟。":::

## <a name="mpi-bandwidth"></a>MPI 带宽

运行 OSU microbenchmark suite 的 MPI 带宽测试。 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)上的示例脚本

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="Azure HC 上的 MPI 带宽。":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest 包](https://community.mellanox.com/s/article/perftest-package)具有许多不受影响的测试，如延迟 (ib_send_lat) 和带宽 (ib_send_bw) 。 下面是一个示例命令。

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>后续步骤

- 阅读有关最新的公告和一些高性能计算 (HPC) 示例和 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)中的结果。
- 有关运行 HPC 工作负荷的更高级结构视图，请参阅 [Azure 上的高性能计算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。

---
title: Azure Linux Vm 的计算基准测试分数
description: 比较运行 Linux 的 Azure VM 的 CoreMark 计算基准测试分数。
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/29/2020
ms.reviewer: davberg
ms.openlocfilehash: afe1bddd5822b506dcf2c177fc308b717ddc2129
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580445"
---
# <a name="compute-benchmark-scores-for-linux-vms"></a>Linux VM 的计算基准测试分数
以下 CoreMark 基准测试分数显示运行 Ubuntu 的 Azure 高性能 VM 产品阵容的计算性能。 此外，还提供了 [Windows VM](../windows/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的计算基准测试分数。

## <a name="standard_das_v4"></a>Standard_Das_v4
（12/11/2019 2:28:52 AM pbi 5851281）

|VM 大小| CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
|---| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|Standard_D2as_v4| AMD EPYC 7452 32-核处理器 | 2 | 1 | 7.8 | 29726 | 693 | 2.33% | 42 |
|Standard_D4as_v4| AMD EPYC 7452 32-核处理器 | 4 | 1 | 15.7 | 59224 | 1,595 | 2.69% | 42 |
|Standard_D8as_v4| AMD EPYC 7452 32-核处理器 | 8 | 1 | 31.4 | 116412 | 3613 | 3.10% | 42 |
|Standard_D16as_v4| AMD EPYC 7452 32-核处理器 | 16 | 2 | 62.9 | 229489 | 7209 | 3.14% | 35 |
|Standard_D32as_v4| AMD EPYC 7452 32-核处理器 | 32 | 4 | 125.9 | 461916 | 6746 | 1.46% | 35 |

## <a name="standard_da_v4"></a>Standard_Da_v4
（12/12/2019 12:01:48 AM pbi 5851281）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_D2a_v4 | AMD EPYC 7452 32-核处理器 | 2 | 1 | 7.8 | 30023 | 333 | 1.11% | 35 |
| Standard_D4a_v4 | AMD EPYC 7452 32-核处理器 | 4 | 1 | 15.7 | 59685 | 1141 | 1.91% | 77 |
| Standard_D8a_v4 | AMD EPYC 7452 32-核处理器 | 8 | 1 | 31.4 | 118346 | 1130 | 0.95% | 42 |
| Standard_D16a_v4 | AMD EPYC 7452 32-核处理器 | 16 | 2 | 62.9 | 231131 | 3830 | 1.66% | 35 |
| Standard_D32a_v4 | AMD EPYC 7452 32-核处理器 | 32 | 4 | 125.9 | 457266 | 10208 | 2.23% | 35 |
| Standard_D48a_v4 | AMD EPYC 7452 32-核处理器 | 48 | 6 | 188.9 | 664078 | 17241 | 2.60% | 35 |
| Standard_D64a_v4 | AMD EPYC 7452 32-核处理器 | 64 | 8 | 251.9 | 863911 | 24818 | 2.87% | 35 |
| Standard_D96a_v4 | AMD EPYC 7452 32-核处理器 | 96 | 12 | 377.9 | 1290455 | 13640 | 1.06% | 35 |

## <a name="standard_eas_v4"></a>Standard_Eas_v4
（12/11/2019 2:28:50 AM pbi 5851281）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E2as_v4 | AMD EPYC 7452 32-核处理器 | 2 | 1 | 15.7 | 29217 | 654 | 2.24% | 42 |
| Standard_E4as_v4 | AMD EPYC 7452 32-核处理器 | 4 | 1 | 31.4 | 58356 | 480 | 0.82% | 42 |
| Standard_E8as_v4 | AMD EPYC 7452 32-核处理器 | 8 | 1 | 62.9 | 115943 | 3526 | 3.04% | 35 |
| Standard_E16as_v4 | AMD EPYC 7452 32-核处理器 | 16 | 2 | 125.9 | 227383 | 5619 | 2.47% | 35 |
| Standard_E32as_v4 | AMD EPYC 7452 32-核处理器 | 32 | 4 | 251.9 | 454609 | 12746 | 2.80% | 35 |
| Standard_E48as_v4 | AMD EPYC 7452 32-核处理器 | 48 | 6 | 377.9 | 682769 | 9257 | 1.36% | 35 |
| Standard_E64as_v4 | AMD EPYC 7452 32-核处理器 | 64 | 8 | 503.9 | 881311 | 28357 | 3.22% | 35 |
| Standard_E96as_v4 | AMD EPYC 7452 32-核处理器 | 96 | 12 | 661.4 | 1299233 | 14997 | 1.15% | 70 |

##  <a name="standard_ea_v4"></a>Standard_Ea_v4
（12/11/2019 2:29:06 AM pbi 5851281）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E2a_v4 | AMD EPYC 7452 32-核处理器 | 2 | 1 | 15.7 | 29561 | 422 | 1.43% | 42 |
| Standard_E4a_v4 | AMD EPYC 7452 32-核处理器 | 4 | 1 | 31.4 | 58303 | 1280 | 2.20% | 42 |
| Standard_E8a_v4 | AMD EPYC 7452 32-核处理器 | 8 | 1 | 62.9 | 114650 | 2726 | 2.38% | 42 |
| Standard_E16a_v4 | AMD EPYC 7452 32-核处理器 | 16 | 2 | 125.9 | 226947 | 4661 | 2.05% | 35 |
| Standard_E32a_v4 | AMD EPYC 7452 32-核处理器 | 32 | 4 | 251.9 | 453666 | 10058 | 2.22% | 42 |
| Standard_E48a_v4 | AMD EPYC 7452 32-核处理器 | 48 | 6 | 377.9 | 665200 | 18714 | 2.81% | 35 |
| Standard_E64a_v4 | AMD EPYC 7452 32-核处理器 | 64 | 8 | 503.9 | 894718 | 25214 | 2.82% | 35 |
| Standard_E96a_v4 | AMD EPYC 7452 32-核处理器 | 96 | 12 | 661.4 | 1298074 | 15948 | 1.23% | 35 |

## <a name="av2---general-compute"></a>Av2 - 常规计算
(3/15/2019 12:06:55 AM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 1 | 1 | 1.9 | 6,483 | 120 | 1.85% | 273 |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 6,059 | 208 | 3.43% | 217 |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 6,367 | 453 | 7.12% | 217 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 3.9 | 13,161 | 194 | 1.48% | 266 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 12,067 | 401 | 3.32% | 203 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 12,527 | 797 | 6.37% | 238 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 2 | 1 | 15.7 | 13,167 | 179 | 1.36% | 273 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 15.7 | 12,133 | 336 | 2.77% | 210 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 15.7 | 12,401 | 656 | 5.29% | 224 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 7.8 | 26,307 | 231 | 0.88% | 231 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 7.8 | 24,552 | 720 | 2.93% | 224 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 7.8 | 24,963 | 1,625 | 6.51% | 252 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 4 | 1 | 31.4 | 26,238 | 292 | 1.11% | 259 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 31.4 | 24,250 | 491 | 2.02% | 189 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 31.4 | 24,725 | 1,553 | 6.28% | 259 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 1 | 15.7 | 53,237 | 687 | 1.29% | 266 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 15.7 | 49,655 | 585 | 1.18% | 147 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 15.7 | 49,005 | 2,162 | 4.41% | 294 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz | 8 | 2 | 62.9 | 52,627 | 902 | 1.71% | 266 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 62.9 | 49,838 | 633 | 1.27% | 182 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 62.9 | 49,123 | 2,483 | 5.05% | 259 |

> [!NOTE]
> Av2 系列 Vm 可以部署在各种不同的硬件类型和处理器上（如上所示）。 Av2 系列 VM 的 CPU 性能和内存配置非常适合部署和测试等入门级工作负荷。 其大小受到限制，以便为正在运行的实例提供相对一致的处理器性能，而不管它部署在什么硬件上；但是，利用特定的较新处理器优化的软件可能会在不同的处理器类型之间看到更显著差异。

## <a name="b---burstable"></a>B - 可迸发
（3/15/2019 12:27:08 AM pbi 3897709）（更新后 6/14/2019 7:09:29 AM pbi 4777081）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_B1ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 13,593 | 307 | 2.26% | 28 |
| Standard_B1ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 14,069 | 495 | 3.52% | 672 |
| Standard_B1s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 0.9 | 13,736 | 211 | 1.54% | 28 |
| Standard_B1s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 0.9 | 13,965 | 457 | 3.27% | 672 |
| Standard_B2ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 7.8 | 27,361 | 1,110 | 4.06% | 28 |
| Standard_B2ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 7.8 | 27,432 | 771 | 2.81% | 672 |
| Standard_B2s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 27,488 | 822 | 2.99% | 28 |
| Standard_B2s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 27,548 | 864 | 3.14% | 672 |
| Standard_B4ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 15.7 | 54,951 | 1,868 | 3.40% | 28 |
| Standard_B4ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 15.7 | 54,051 | 1,260 | 2.33% | 672 |
| Standard_B8ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 31.4 | 111,929 | 1,562 | 1.40% | 35 |
| Standard_B8ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 31.4 | 109,537 | 1,354 | 1.24% | 665 |
| Standard_B12ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 12 | 1 | 47.1 | 170777 | 3421 | 2.00% | 70 |
| Standard_B12ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 12 | 1 | 47.1 | 166676 | 1368 | 0.82% | 70 |
| Standard_B16ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 62.9 | 208373 | 30383 | 14.58% | 63 |
| Standard_B16ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 62.9 | 223203 | 1232 | 0.55% | 70 |
| Standard_B20ms | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 20 | 1 | 78.6 | 269561 | 25095 | 9.31% | 77 |
| Standard_B20ms | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 20 | 1 | 78.6 | 274007 | 3669 | 1.34% | 70 |


> [!NOTE]
> B 系列 Vm 适用于具有可突增性能要求的工作负荷。 当使用量小于其基线时，VM 实例会累积额度。 在累积了额度的情况下，VM 可以突发到基线之上，全力满足短暂的 CPU 突发需求。 突发时间取决于可用额度，这些额度是 VM 大小和时间的函数。  
>
> CoreMark 是一个短时间运行的测试，通常在可用的突发额度内完成。  因此，上面的数字通常代表 VM 的突发性能，反映了短暂突发的工作负载（在 B 系列上常见）性能。

## <a name="dsv3---general-compute--premium-storage"></a>DSv3 - 通用计算 + 高级存储
(3/12/2019 6:52:03 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_D2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 7.8 | 20,153 | 838 | 4.16% | 147 |
| Standard_D2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 7.8 | 20,903 | 1,324 | 6.33% | 553 |
| Standard_D4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 15.7 | 39,502 | 1,257 | 3.18% | 189 |
| Standard_D4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 15.7 | 40,547 | 1,935 | 4.77% | 511 |
| Standard_D8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 31.4 | 80,191 | 1,054 | 1.31% | 168 |
| Standard_D8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 31.4 | 79,884 | 3,073 | 3.85% | 532 |
| Standard_D16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 62.9 | 160,319 | 1,213 | 0.76% | 105 |
| Standard_D16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 62.9 | 156,325 | 2,176 | 1.39% | 588 |
| Standard_D32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 32 | 2 | 125.9 | 315,457 | 2,647 | 0.84% | 105 |
| Standard_D32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 1 | 125.9 | 312,058 | 1,661 | 0.53% | 595 |
| Standard_D64s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 251.9 | 627,378 | 4,447 | 0.71% | 700 |

## <a name="dv3---general-compute"></a>Dv3 - 通用计算
(3/12/2019 6:54:27 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_D2_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 7.8 | 20,359 | 799 | 3.93% | 154 |
| Standard_D2_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 7.8 | 20,737 | 1,422 | 6.86% | 546 |
| Standard_D4_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 15.7 | 40,095 | 1,501 | 3.74% | 147 |
| Standard_D4_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 15.7 | 41,147 | 2,706 | 6.58% | 546 |
| Standard_D8_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 31.4 | 80,383 | 1,486 | 1.85% | 133 |
| Standard_D8_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 31.4 | 80,511 | 3,916 | 4.86% | 560 |
| Standard_D16_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 62.9 | 160,932 | 2,200 | 1.37% | 140 |
| Standard_D16_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 62.9 | 158,679 | 4,550 | 2.87% | 560 |
| Standard_D32_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 32 | 2 | 125.9 | 314,208 | 4,250 | 1.35% | 189 |
| Standard_D32_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 1 | 125.9 | 312,472 | 3,173 | 1.02% | 511 |
| Standard_D64_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 251.9 | 627,470 | 9,651 | 1.54% | 700 |

## <a name="dsv2---storage-optimized"></a>DSv2 - 存储优化
(3/15/2019 12:53:13 AM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_DS1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 3.4 | 14,642 | 600 | 4.10% | 259 |
| Standard_DS1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 3.4 | 14,808 | 904 | 6.10% | 434 |
| Standard_DS2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 6.8 | 28,654 | 877 | 3.06% | 301 |
| Standard_DS2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 6.8 | 29,089 | 1,421 | 4.89% | 406 |
| Standard_DS3_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 13.7 | 57,255 | 1,633 | 2.85% | 238 |
| Standard_DS3_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 13.7 | 57,255 | 2,265 | 3.96% | 462 |
| Standard_DS4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 27.5 | 116,681 | 1,097 | 0.94% | 231 |
| Standard_DS4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 27.5 | 112,512 | 1,261 | 1.12% | 462 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 55.0 | 225,661 | 2,370 | 1.05% | 189 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 55.0 | 229,145 | 2,878 | 1.26% | 21 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 55.0 | 226,818 | 1,797 | 0.79% | 497 |
| Standard_DS11_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 13.7 | 28,571 | 920 | 3.22% | 238 |
| Standard_DS11_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 13.7 | 29,049 | 1,614 | 5.56% | 469 |
| Standard_DS11-1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 13.7 | 14,594 | 617 | 4.23% | 287 |
| Standard_DS11-1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 13.7 | 14,951 | 852 | 5.70% | 413 |
| Standard_DS12_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 27.5 | 57,503 | 1,398 | 2.43% | 217 |
| Standard_DS12_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 27.5 | 57,082 | 2,372 | 4.16% | 483 |
| Standard_DS12-1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 27.5 | 14,698 | 564 | 3.84% | 238 |
| Standard_DS12-1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 27.5 | 15,127 | 941 | 6.22% | 462 |
| Standard_DS12-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 27.5 | 28,711 | 981 | 3.42% | 259 |
| Standard_DS12-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 27.5 | 29,305 | 1,241 | 4.24% | 441 |
| Standard_DS13_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 55.0 | 116,875 | 1,286 | 1.10% | 203 |
| Standard_DS13_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 55.0 | 112,318 | 1,356 | 1.21% | 504 |
| Standard_DS13-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 55.0 | 29,105 | 1,154 | 3.97% | 224 |
| Standard_DS13-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 55.0 | 29,936 | 1,720 | 5.75% | 483 |
| Standard_DS13-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 55.0 | 56,992 | 1,814 | 3.18% | 280 |
| Standard_DS13-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 55.0 | 57,781 | 2,122 | 3.67% | 427 |
| Standard_DS14_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 110.2 | 224,149 | 3,450 | 1.54% | 196 |
| Standard_DS14_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 110.2 | 227,108 | 1,267 | 0.56% | 504 |
| Standard_DS14-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 2 | 110.2 | 56,211 | 2,154 | 3.83% | 189 |
| Standard_DS14-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 110.2 | 59,651 | 2,560 | 4.29% | 518 |
| Standard_DS14-8_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 2 | 110.2 | 112,280 | 4,430 | 3.95% | 196 |
| Standard_DS14-8_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 110.2 | 113,375 | 1,442 | 1.27% | 511 |
| Standard_DS15_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 20 | 2 | 137.7 | 279,359 | 4,032 | 1.44% | 665 |

## <a name="dv2---general-compute"></a>Dv2 - 常规计算
(3/12/2019 6:53:48 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_D1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 3.4 | 14,730 | 663 | 4.50% | 385 |
| Standard_D1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 3.4 | 15,057 | 1,319 | 8.76% | 322 |
| Standard_D2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 6.8 | 29,395 | 1,073 | 3.65% | 329 |
| Standard_D2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 6.8 | 29,564 | 2,145 | 7.26% | 378 |
| Standard_D3_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 13.7 | 58,150 | 1,340 | 2.30% | 343 |
| Standard_D3_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 13.7 | 57,820 | 2,944 | 5.09% | 364 |
| Standard_D4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 27.5 | 117,448 | 1,612 | 1.37% | 308 |
| Standard_D4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 27.5 | 114,082 | 3,369 | 2.95% | 399 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 55.0 | 226,370 | 4,722 | 2.09% | 147 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 55.0 | 225,035 | 5,026 | 2.23% | 119 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 55.0 | 227,883 | 3,259 | 1.43% | 441 |
| Standard_D11_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 13.7 | 29,260 | 1,012 | 3.46% | 308 |
| Standard_D11_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 13.7 | 29,306 | 1,763 | 6.02% | 399 |
| Standard_D12_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 27.5 | 58,322 | 1,391 | 2.39% | 329 |
| Standard_D12_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 27.5 | 57,999 | 3,533 | 6.09% | 371 |
| Standard_D13_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 55.0 | 117,218 | 1,514 | 1.29% | 329 |
| Standard_D13_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 55.0 | 114,344 | 3,307 | 2.89% | 378 |
| Standard_D14_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 110.2 | 224,348 | 5,477 | 2.44% | 280 |
| Standard_D14_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 110.2 | 228,221 | 2,733 | 1.20% | 427 |
| Standard_D15_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 20 | 2 | 137.7 | 281,494 | 7,976 | 2.83% | 672 |

## <a name="esv3---memory-optimized--premium-storage"></a>Esv3 - 内存优化 + 高级存储
(3/12/2019 7:17:33 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 15.7 | 20,957 | 1,200 | 5.73% | 672 |
| Standard_E4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 31.4 | 40,420 | 1,993 | 4.93% | 672 |
| Standard_E4-2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 31.4 | 20,774 | 1,133 | 5.45% | 672 |
| Standard_E8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 62.9 | 80,153 | 3,308 | 4.13% | 665 |
| Standard_E8-2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 62.9 | 21,178 | 1,334 | 6.30% | 665 |
| Standard_E8-4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 62.9 | 40,614 | 2,216 | 5.46% | 672 |
| Standard_E16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 125.9 | 156,137 | 2,160 | 1.38% | 672 |
| Standard_E16-4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 125.9 | 41,950 | 2,309 | 5.50% | 637 |
| Standard_E16-8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 125.9 | 81,196 | 3,179 | 3.91% | 658 |
| Standard_E20s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 20 | 1 | 157.4 | 196,619 | 1,325 | 0.67% | 672 |
| Standard_E32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 251.9 | 304,707 | 5,719 | 1.88% | 672 |
| Standard_E32-8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 2 | 251.9 | 83,576 | 3,693 | 4.42% | 672 |
| Standard_E32-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 2 | 251.9 | 158,023 | 4,317 | 2.73% | 672 |
| Standard_E64s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 425.2 | 628,540 | 3,982 | 0.63% | 49 |
| Standard_E64-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 2 | 425.2 | 169,611 | 3,265 | 1.92% | 42 |
| Standard_E64-32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 425.2 | 307,584 | 3,569 | 1.16% | 56 |

## <a name="eisv3---memory-opt--premium-storage-isolated"></a>Eisv3 - 内存优化 + 高级存储（独立）
(4/11/2019 10:07:29 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E64is_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 425.2 | 627,745 | 4,062 | 0.65% | 196 |

## <a name="ev3---memory-optimized"></a>Ev3 - 内存优化
(3/12/2019 6:52:13 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E2_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 15.7 | 21,171 | 1,772 | 8.37% | 693 |
| Standard_E4_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 31.4 | 41,181 | 3,148 | 7.64% | 700 |
| Standard_E8_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 62.9 | 81,211 | 5,055 | 6.22% | 700 |
| Standard_E16_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 125.9 | 158,152 | 4,033 | 2.55% | 700 |
| Standard_E20_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 20 | 1 | 157.4 | 197,739 | 2,731 | 1.38% | 693 |
| Standard_E32_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 32 | 2 | 251.9 | 307,286 | 8,353 | 2.72% | 700 |
| Standard_E64_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 425.2 | 628,451 | 9,235 | 1.47% | 707 |

## <a name="eiv3---memory-optimized-isolated"></a>Eiv3 - 内存优化（独立）
(3/12/2019 6:57:51 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_E64i_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 425.2 | 625,855 | 4,881 | 0.78% | 7 |
| Standard_E64i_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 64 | 2 | 425.2 | 629,151 | 9,756 | 1.55% | 217 |

## <a name="fsv2---compute--storage-optimized"></a>Fsv2 - 计算 + 存储优化
(3/12/2019 6:51:35 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_F2s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 2 | 1 | 3.9 | 28,219 | 1,843 | 6.53% | 700 |
| Standard_F4s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 4 | 1 | 7.8 | 53,911 | 1,002 | 1.86% | 707 |
| Standard_F8s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 8 | 1 | 15.7 | 106,467 | 1,101 | 1.03% | 707 |
| Standard_F16s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 16 | 1 | 31.4 | 211,311 | 1,724 | 0.82% | 707 |
| Standard_F32s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 32 | 1 | 62.9 | 423,175 | 4,346 | 1.03% | 707 |
| Standard_F64s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 64 | 2 | 125.9 | 829,537 | 21,574 | 2.60% | 707 |
| Standard_F72s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU @ 2.70GHz | 72 | 2 | 141.7 | 933,800 | 26,783 | 2.87% | 707 |

## <a name="fs---compute-and-storage-optimized"></a>Fs - 计算和存储优化
(3/15/2019 12:12:51 AM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_F1s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 14,552 | 504 | 3.46% | 350 |
| Standard_F1s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 14,784 | 858 | 5.80% | 357 |
| Standard_F2s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 28,664 | 895 | 3.12% | 245 |
| Standard_F2s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 29,188 | 1,228 | 4.21% | 455 |
| Standard_F4s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 7.8 | 57,192 | 1,700 | 2.97% | 259 |
| Standard_F4s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 7.8 | 57,412 | 2,215 | 3.86% | 448 |
| Standard_F8s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 15.7 | 117,008 | 1,139 | 0.97% | 259 |
| Standard_F8s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 15.7 | 112,610 | 1,595 | 1.42% | 441 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 31.4 | 225,444 | 2,328 | 1.03% | 210 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 31.4 | 228,919 | 3,380 | 1.48% | 28 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 31.4 | 227,015 | 1,543 | 0.68% | 462 |

## <a name="f---compute-optimized"></a>F - 计算优化
(3/12/2019 6:53:59 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_F1 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 1 | 1 | 1.9 | 14,937 | 593 | 3.97% | 350 |
| Standard_F1 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 1 | 1 | 1.9 | 15,460 | 1,326 | 8.58% | 350 |
| Standard_F2 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 2 | 1 | 3.9 | 29,324 | 1,196 | 4.08% | 343 |
| Standard_F2 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 2 | 1 | 3.9 | 29,299 | 1,908 | 6.51% | 364 |
| Standard_F4 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 4 | 1 | 7.8 | 58,314 | 1,245 | 2.14% | 364 |
| Standard_F4 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 4 | 1 | 7.8 | 58,280 | 3,581 | 6.14% | 336 |
| Standard_F8 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 8 | 1 | 15.7 | 117,516 | 1,460 | 1.24% | 308 |
| Standard_F8 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 8 | 1 | 15.7 | 114,361 | 3,868 | 3.38% | 399 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 1 | 31.4 | 226,487 | 4,140 | 1.83% | 154 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v3 @ 2.40GHz | 16 | 2 | 31.4 | 226,683 | 4,723 | 2.08% | 133 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz | 16 | 1 | 31.4 | 228,592 | 2,371 | 1.04% | 392 |

## <a name="gs---storage-optimized"></a>GS - 存储优化
（3/12/2019 10:22:33 PM pbi 3897709）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_GS1 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 2 | 1 | 27.5 | 28835 | 2222 | 7.71% | 287 |
| Standard_GS2 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 55.0 | 55568 | 3139 | 5.65% | 287 |
| Standard_GS3 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 110.2 | 106567 | 2188 | 2.05% | 287 |
| Standard_GS4 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 1 | 220.4 | 210586 | 4130 | 1.96% | 287 |
| Standard_GS4-4 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 220.4 | 58598 | 2,670 | 4.56% | 287 |
| Standard_GS4-8 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 220.4 | 108234 | 2392 | 2.21% | 287 |
| Standard_GS5 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 32 | 2 | 440.9 | 399835 | 8694 | 2.17% | 287 |
| Standard_GS5-8 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 2 | 440.9 | 116643 | 2354 | 2.02% | 287 |
| Standard_GS5-16 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 2 | 440.9 | 210984 | 2995 | 1.42% | 287 |

## <a name="g---compute-optimized"></a>G - 计算优化
（3/12/2019 10:23:51 PM pbi 3897709）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_G1 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 2 | 1 | 27.5 | 32808 | 2679 | 8.17% | 287 |
| Standard_G2 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 55.0 | 62907 | 4465 | 7.10% | 287 |
| Standard_G3 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 110.2 | 113471 | 4,346 | 3.83% | 287 |
| Standard_G4 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 1 | 220.4 | 212092 | 2857 | 1.35% | 287 |
| Standard_G5 | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 32 | 2 | 440.9 | 403315 | 6947 | 1.72% | 273 |

## <a name="h---high-performance-compute-hpc"></a>H - 高性能计算 (HPC)
（3/12/2019 10:50:51 PM pbi 3897709）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_H8 | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 8 | 1 | 55.0 | 149859 | 734 | 0.49% | 175 |
| Standard_H8m | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 8 | 1 | 110.2 | 149931 | 657 | 0.44% | 147 |
| Standard_H16 | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 110.2 | 282083 | 6738 | 2.39% | 84 |
| Standard_H16m | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 220.4 | 282106 | 7013 | 2.49% | 84 |
| Standard_H16mr | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 220.4 | 282167 | 6889 | 2.44% | 84 |
| Standard_H16r | Intel(R) Xeon(R) CPU E5-2667 v3 @ 3.20GHz | 16 | 2 | 110.2 | 280837 | 6587 | 2.35% | 84 |

## <a name="lv2---storage-optimized"></a>Lv2-优化存储
（3/14/2019 5:49:04 PM pbi 3897709）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_L8s_v2 | AMD EPYC 7551 32-核处理器 | 8 | 1 | 62.9 | 80528 | 404 | 0.50% | 119 |
| Standard_L16s_v2 | AMD EPYC 7551 32-核处理器 | 16 | 2 | 125.9 | 154829 | 3708 | 2.40% | 119 |
| Standard_L32s_v2 | AMD EPYC 7551 32-核处理器 | 32 | 4 | 251.9 | 310811 | 7751 | 2.49% | 119 |
| Standard_L64s_v2 | AMD EPYC 7551 32-核处理器 | 64 | 8 | 503.9 | 595140 | 14572 | 2.45% | 112 |
| Standard_L80s_v2 | AMD EPYC 7551 32-核处理器 | 80 | 10 | 629.9 | 773171 | 19559 | 2.53% | 119 |

## <a name="ls---storage-optimized"></a>Ls - 存储优化
（3/12/2019 10:22:29 PM pbi 3897709）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_L4s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 4 | 1 | 31.4 | 56488 | 2916 | 5.16% | 287 |
| Standard_L8s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 8 | 1 | 62.9 | 107017 | 2323 | 2.17% | 287 |
| Standard_L16s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 16 | 1 | 125.9 | 210865 | 3653 | 1.73% | 280 |
| Standard_L32s | Intel(R) Xeon(R) CPU E5-2698B v3 @ 2.00GHz | 32 | 2 | 251.9 | 399963 | 9254 | 2.31% | 287 |

## <a name="m---memory-optimized"></a>M - 内存优化
(4/11/2019 7:30:39 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_M8-2ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 2 | 1 | 215.2 | 22,605 | 29 | 0.13% | 42 |
| Standard_M8-4ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 4 | 1 | 215.2 | 44,488 | 183 | 0.41% | 42 |
| Standard_M16-4ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 4 | 1 | 430.6 | 44,451 | 269 | 0.61% | 42 |
| Standard_M16-8ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 8 | 1 | 430.6 | 88,238 | 1,243 | 1.41% | 42 |
| Standard_M32-8ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 8 | 1 | 861.2 | 88,521 | 1,353 | 1.53% | 42 |
| Standard_M32-16ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 16 | 1 | 861.2 | 174,674 | 3,104 | 1.78% | 42 |
| Standard_M64 | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1,007.9 | 683,022 | 11,929 | 1.75% | 42 |
| Standard_M64-16ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 16 | 2 | 1,763.9 | 169,386 | 4,737 | 2.80% | 42 |
| Standard_M64-32ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 2 | 1,763.9 | 337,599 | 4,738 | 1.40% | 42 |
| Standard_M64m | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1,763.9 | 677,466 | 14,478 | 2.14% | 42 |
| Standard_M64ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1,763.9 | 675,342 | 16,577 | 2.45% | 42 |
| Standard_M64s | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 1,007.9 | 674,785 | 15,983 | 2.37% | 42 |
| Standard_M128 | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 2,015.9 | 1,334,218 | 21,126 | 1.58% | 42 |
| Standard_M128-32ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 4 | 3,831.1 | 334,873 | 5,005 | 1.49% | 42 |
| Standard_M128-64ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 4 | 3,831.1 | 667,808 | 18,145 | 2.72% | 42 |
| Standard_M128m | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 3,831.1 | 1,335,873 | 19,642 | 1.47% | 42 |
| Standard_M128ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 3,831.1 | 1,329,151 | 24,295 | 1.83% | 42 |
| Standard_M128s | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 128 | 4 | 2,015.9 | 1,329,923 | 20,117 | 1.51% | 42 |
| Standard_M16ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 16 | 1 | 430.6 | 174,686 | 2,704 | 1.55% | 35 |
| Standard_M32ls | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 1 | 251.9 | 344,069 | 3,372 | 0.98% | 42 |
| Standard_M32ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 1 | 861.2 | 343,226 | 4,884 | 1.42% | 84 |
| Standard_M32ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 2 | 861.2 | 336,526 | 4,396 | 1.31% | 7 |
| Standard_M32ts | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 32 | 1 | 188.9 | 341,112 | 5,491 | 1.61% | 35 |
| Standard_M64ls | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 64 | 2 | 503.9 | 676,026 | 18,078 | 2.67% | 42 |
| Standard_M8ms | Intel(R) Xeon(R) CPU E7-8890 v3 @ 2.50GHz | 8 | 1 | 215.2 | 88,066 | 1,391 | 1.58% | 42 |

## <a name="ncsv3---gpu-enabled"></a>NCSv3 - 支持 GPU
(3/21/2019 5:48:37 PM pbi 3897709)

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_NC6s_v3 | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 6 | 1 | 110.2 | 106,929 | 353 | 0.33% | 49 |
| Standard_NC12s_v3 | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 12 | 1 | 220.4 | 213,585 | 875 | 0.41% | 42 |
| Standard_NC24rs_v3 | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 24 | 2 | 440.9 | 403,554 | 6,705 | 1.66% | 42 |
| Standard_NC24s_v3 | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 24 | 2 | 440.9 | 403,874 | 7,603 | 1.88% | 42 |

## <a name="ncsv2---gpu-enabled"></a>NCSv2-已启用 GPU
（3/12/2019 11:19:19 PM pbi 3897709）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_NC6s_v2 | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 6 | 1 | 110.2 | 107115 | 321 | 0.30% | 63 |
| Standard_NC12s_v2 | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 12 | 1 | 220.4 | 213814 | 656 | 0.31% | 63 |
| Standard_NC24rs_v2 | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 24 | 2 | 440.9 | 401728 | 6995 | 1.74% | 63 |
| Standard_NC24s_v2 | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 24 | 2 | 440.9 | 402808 | 7923 | 1.97% | 63 |

## <a name="nc---gpu-enabled"></a>NC-已启用 GPU
（3/12/2019 11:08:03 PM pbi 3897709）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_NC6 | Intel （R）强® CPU E5-2690 v3 @ 2.60 GHz | 6 | 1 | 55.0 | 102211 | 658 | 0.64% | 259 |
| Standard_NC12 | Intel （R）强® CPU E5-2690 v3 @ 2.60 GHz | 12 | 1 | 110.2 | 203523 | 2293 | 1.13% | 259 |
| Standard_NC24 | Intel （R）强® CPU E5-2690 v3 @ 2.60 GHz | 24 | 2 | 220.4 | 382897 | 8712 | 2.28% | 259 |
| Standard_NC24r | Intel （R）强® CPU E5-2690 v3 @ 2.60 GHz | 24 | 2 | 220.4 | 383171 | 9166 | 2.39% | 259 |

## <a name="nds--gpu-enabled"></a>已启用 NDs-GPU
（3/12/2019 11:19:10 PM pbi 3897709）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_ND6s | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 6 | 1 | 110.2 | 107095 | 353 | 0.33% | 63 |
| Standard_ND12s | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 12 | 1 | 220.4 | 212298 | 3457 | 1.63% | 63 |
| Standard_ND24rs | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 24 | 2 | 440.9 | 402749 | 7838 | 1.95% | 56 |
| Standard_ND24s | Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz | 24 | 2 | 440.9 | 401822 | 7776 | 1.94% | 63 |

## <a name="nv---gpu-enabled"></a>NV-已启用 GPU
（3/12/2019 11:08:13 PM pbi 3897709）

| VM 大小 | CPU | vCPU | NUMA 节点 | 内存 (GiB) | 平均分数 | Std Dev | 标准偏差百分比 | 运行次数 |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Standard_NV6 | Intel （R）强® CPU E5-2690 v3 @ 2.60 GHz | 6 | 1 | 55.0 | 101728 | 2094 | 2.06% | 259 |
| Standard_NV12 | Intel （R）强® CPU E5-2690 v3 @ 2.60 GHz | 12 | 1 | 110.2 | 203903 | 1,724 | 0.85% | 252 |
| Standard_NV24 | Intel （R）强® CPU E5-2690 v3 @ 2.60 GHz | 24 | 2 | 220.4 | 379879 | 8737 | 2.30% | 259 |


## <a name="about-coremark"></a>关于 CoreMark
Linux 分数是通过在 Ubuntu 上运行 [CoreMark](https://www.eembc.org/coremark/faq.php) 计算得出的。 CoreMark 中配置的线程数设置为虚拟 CPU 的数目，并发性设置为 PThreads。 目标迭代次数已根据预期性能进行调整，提供至少 20 秒（通常更长）的运行时。 最终分数表示已完成迭代次数除以运行测试所花费的秒数。 每项测试在每个 VM 上至少运行了七次。 测试运行日期如上所示。 于当天在支持 VM 的每个 Azure 公共区域中的多个 VM 上执行测试运行。 基本 A 和 B (Burstable) 系列未显示，因为其性能不稳定。 N 系列未显示，因为它们以 GPU 为中心，并且 Coremark 不测量 GPU 性能。

## <a name="next-steps"></a>后续步骤
* 有关存储容量、磁盘详细信息以及选择 VM 大小的注意事项，请参阅[虚拟机的大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 若要在 Linux VM 上运行 CoreMark 脚本，请下载 [CoreMark 脚本包](https://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip)。


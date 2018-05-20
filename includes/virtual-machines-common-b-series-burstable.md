---
title: include 文件
description: include 文件
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 7372a0c49f6e41ef4d336188ceb5cf4393f4c982
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
B 系列的 VM 系列具备使 CPU 性能突增到 Intel® Broadwell E5-2673 v4 2.3 GHz 或 Intel® Haswell 2.4 GHz E5-2673 v3 处理器 vCPU 的 100% 的能力，允许用户选择可为其工作负荷提供必要的基本级别性能的 VM 大小。

对于并非持续需要 CPU 完全性能的工作负荷（如 Web 服务器、小型数据库以及开发和测试环境）而言，B 系列 VM 十分理想。 这些工作负荷通常具有可突增的性能要求。 B 系列使用户可以购买具有基准性能的 VM 大小，并且该 VM 实例在 CPU 使用率未达到其基线时会增加积分。 在虚拟机累积积分后，当应用程序需要更高的 CPU 性能时，虚拟机可以使用高达 100% vCPU 突增到基线以上。

B 系列有以下六种 VM 大小：

| 大小          | vCPU | 内存：GiB | 临时存储 (SSD) GiB | VM 的基本 CPU 性能 | VM 的最大 CPU 性能 | 累积的积分/小时 | 最大累积积分 |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10%                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20%                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a"></a>问题解答 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>问：如何从 VM 获得 135% 的基线性能？
**答**：135% 的基线性能在组成 VM 大小的 8 个 vCPU 之间共享。 例如，如果应用程序使用 8 个核心中的 4 个进行批处理，并且这 4 个 vCPU 中的每个都以 30% 的利用率运行，那么 VM CPU 性能的总额就等于 120%。  这意味着 VM 将基于从基线性能起的 15% 增量来累积积分时间。  但它也意味着，如果有积分可用，则同一个 VM 可以使用所有 8 个 vCPU 的 100%，为该 VM 提供 800% 的最大 CPU 性能。


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>问：如何监视积分余额和消耗情况
**答**：我们将在未来几周内引入 2 个新指标，**积分**指标将允许用户查看 VM 已累积的积分，**已消耗的积分**指标将显示 VM 已从银行消耗的 CPU 积分数。    你将能够从门户的指标窗格中查看这些指标，也可以编程方式通过 Azure Monitor API 查看这些指标。

有关如何访问 Azure 指标数据的详细信息，请参阅 [Microsoft Azure 中的指标概述](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="q-how-are-credits-accumulated"></a>问：积分如何累积？
**答**：VM 累积和消耗率是这样设置的：完全在基本性能级别运行的 VM 既没有突增积分的净累积，也没有突增积分的净消耗。  每当在基本性能级别下运行时，VM 的积分都会净增加，而每当 VM 的 CPU 利用率高于其基本性能级别时，VM 的积分都将净减少。

**示例**：为小型时间和考勤数据库应用程序使用 B1ms 大小部署一个 VM。 此大小允许应用程序使用最多 20% 的 vCPU 作为基线，这是我每分钟可以使用或累积的 0.2 个积分。 

应用程序在员工工作日（在 7:00-9:00 AM 和 4:00 - 6:00PM 之间）的开始和结束时间处于繁忙状态。 在一天的其他 20 小时内，应用程序通常处于空闲状态，仅使用 10% 的 vCPU。 对于非高峰小时，我每分钟获得 0.2 个积分，但每分钟仅消耗 0.l 个积分，因此我的 VM 将每小时累积 0.1 x 60 = 6 个积分。  对于非高峰的 20 小时，我将累积 120 个积分。  

在高峰小时内，应用程序的平均 vCPU 使用率为 60%，我仍每分钟获得 0.2 个积分，但我每分钟消耗 0.6 个积分，净成本为一分钟 0.4 个积分，或者每小时 0.4 x 60 = 24 个积分。 我每天有 4 小时的高峰使用期，因此我的高峰使用期成本为 4 x 24 = 96 个积分。

如果我用在非高峰时间获得的 120 个积分减去在高峰时间内使用的 96 个积分，我每天将累积可用于其他突发活动的 24 个积分。


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>问：B 系列是否支持高级存储数据磁盘？
**答**：B 系列大小支持高级存储数据磁盘。   
    
### <a name="q-my-remaining-credit-are-set-to-0-after-a-redepoy-or-a-stopstart"></a>问：重新部署或停止/启动后，我的剩余信用额度设置为 0。
**答**：当 VM 为“已重新部署”（即，VM 移到另一个节点）时，累积的信用额度将丢失。 如果 VM 已停止/已启动，但仍保留在同一节点，VM 将保留累积的信用额度。 只要 VM 在节点上重新启动，它就会获取初始信用额度，对于 Standard_B8ms，该信用额度为 240 分钟。

    

    

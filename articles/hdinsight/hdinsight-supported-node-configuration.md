---
title: Azure HDInsight 支持的节点配置
description: 了解 HDInsight 群集节点的最小和建议配置。
keywords: vm 大小、群集大小、群集配置
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484780"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight 的默认和推荐的节点配置是什么？

本文介绍 Azure HDInsight 群集的默认和推荐的节点配置。

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>群集的默认和最低建议节点配置和虚拟机大小

下表列出了 HDInsight 群集的默认和推荐的虚拟机（VM）大小。  若要在创建 PowerShell 或 Azure CLI 脚本来部署 HDInsight 群集时要使用的 VM 大小，则需要此信息。

如果群集中需要32个以上的工作节点，请选择至少具有8个核心和 14 GB RAM 的头节点大小。 

只有启用了加速写入功能的 Kafka 和 HBase 群集才能使用具有数据磁盘的群集类型。 在这些情况下，HDInsight 支持 P30 和 S30 磁盘大小。

下表总结了本文档中使用的所有最低建议 VM 类型的规范。

| 大小              | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 临时存储的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (MBps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 16/16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 32/32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000/750/375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000/750/375                                        | 64/64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000/20/10                                           | 2/2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000/40/20                                           | 4/4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000/80/40                                           | 8/8x500               | 4 / 1000                     |

有关每种 VM 类型的规范的详细信息，请参阅以下文档：

* [常规用途虚拟机大小： Dv2 系列1-5](../virtual-machines/dv2-dsv2-series.md)
* [内存优化虚拟机大小： Dv2 系列11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [常规用途虚拟机大小： Av2 系列1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>除巴西南部和日本西部之外的所有受支持区域

> [!Note]
> 若要获取用于 powershell 和其他脚本的 SKU 标识符，请在下表中将 `Standard_` 添加到所有 VM Sku 的开头。 例如，`D12_v2` 将变为 `Standard_D12_v2`。

| 群集类型 | Hadoop | HBase | 交互式查询 | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| 头：默认 VM 大小 | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2， <br/>D13_v2 * | D12_v2 | D3_v2 |
| Head：建议的最小 VM 大小 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2， <br/>D13_v2 * | D12_v2 | D3_v2 |
| 辅助角色：默认 VM 大小 | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4个 D12_v2，每个 broker 有2个 S30 磁盘 |
| 辅助角色：建议的最小 VM 大小 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| Zookeeper：默认 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper：建议的最小 VM 大小 |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML 服务：默认 VM 大小 |  |  |  |  |  | D4_v2 |  |
| ML 服务：建议的最小 VM 大小 |  |  |  |  |  | D4_v2 |  |

\* = Spark 企业安全性套餐（ESP）群集的 VM 大小

### <a name="brazil-south-and-japan-west-only"></a>仅巴西南部和日本西部

| 群集类型 | Hadoop | HBase | 交互式查询 | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| 头：默认 VM 大小 | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head：建议的最小 VM 大小 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| 辅助角色：默认 VM 大小 | D4 | D4 | D14 | D3 | D13 | D4 |
| 辅助角色：建议的最小 VM 大小 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| Zookeeper：默认 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper：建议的最小 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML 服务：默认 VM 大小 |  |  |  |  |  | D4 |
| ML 服务：建议的最小 VM 大小 |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - 对于 Storm 群集类型，头称为 *Nimbus*。
> - 对于 Storm 群集类型，辅助角色称为“主管”。
> - 对于 HBase 群集类型，辅助角色称为“区域”。

## <a name="next-steps"></a>后续步骤

* [HDInsight 提供了哪些 Apache Hadoop 组件和版本？](hdinsight-component-versioning.md)

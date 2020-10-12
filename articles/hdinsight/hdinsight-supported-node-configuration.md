---
title: Azure HDInsight 支持的节点配置
description: 了解 HDInsight 群集节点的最小配置和建议配置。
keywords: vm 大小、群集大小、群集配置
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: 0bf5559590b66400fc4fc4dc27ea88c3522effb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83646615"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight 的默认和推荐节点配置是什么？

本文介绍 Azure HDInsight 群集的默认和推荐节点配置。

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>群集的默认和最小推荐节点配置以及虚拟机大小

下表列出了 HDInsight 群集的默认和推荐虚拟机 (VM) 大小。  在创建 PowerShell 或 Azure CLI 脚本以部署 HDInsight 群集时，要了解要使用的 VM 大小，此信息必不可少。

如果需要群集中有 32 个以上辅助节点，头节点大小应选择至少具有 8 个核心和 14 GB RAM。

只有启用了“加速写入功能”的 Kafka 和 HBase 群集才是具有数据磁盘的群集类型。 在这些方案中，HDInsight 支持 P30 和 S30 磁盘大小。 对于所有其他群集类型，HDInsight 向其提供托管磁盘空间。 从 2019 年 7 月 11 日开始，新创建的群集中每个节点的托管磁盘大小为 128 GB。 此项不能更改。

下表中总结了本文档中使用的所有最小推荐 VM 类型的规范。

| 大小              | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (MBps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 16             / 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 32            / 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000/750/375                                          | 64             / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000/750/375                                        | 64/64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000/20/10                                           | 2/2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000/40/20                                           | 4/4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000/80/40                                           | 8/8x500               | 4 / 1000                     |

有关每个 VM 类型规范的详细信息，请参阅以下文档：

* [常规用途虚拟机大小：Dv2 系列 1-5](../virtual-machines/dv2-dsv2-series.md)
* [内存优化虚拟机大小：Dv2 系列 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [常规用途虚拟机大小：Av2 系列 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>除巴西南部和日本西部外的所有受支持区域

> [!Note]
> 若要获取用于 Powershell 和其他脚本的 SKU 标识符，请在下表中将 `Standard_` 添加到所有 VM SKU 的开头。 例如，`D12_v2` 将变为 `Standard_D12_v2`。

| 群集类型 | Hadoop | HBase | 交互式查询 | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| 头：默认 VM 大小 | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| 头：建议的最小 VM 大小 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| 辅助角色：默认 VM 大小 | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2，每个中转站有 2 个 S30 磁盘 |
| 辅助角色：建议的最小 VM 大小 | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| Zookeeper：默认 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper：建议的最小 VM 大小 |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML 服务：默认 VM 大小 |  |  |  |  |  | D4_v2 |  |
| ML 服务：建议的最小 VM 大小 |  |  |  |  |  | D4_v2 |  |

\* = Spark 企业安全性套餐 (ESP) 群集的 VM 大小

### <a name="brazil-south-and-japan-west-only"></a>仅限巴西南部和日本西部

| 群集类型 | Hadoop | HBase | 交互式查询 | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| 头：默认 VM 大小 | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| 头：建议的最小 VM 大小 | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
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

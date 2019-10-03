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
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076207"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight 的默认和推荐的节点配置是什么？

本文介绍 Azure HDInsight 群集的默认和推荐的节点配置。

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>群集的默认节点配置和虚拟机大小

下表列出了 HDInsight 群集的默认和推荐的虚拟机 (VM) 大小。  若要在创建 PowerShell 或 Azure CLI 脚本来部署 HDInsight 群集时要使用的 VM 大小, 则需要此信息。 

如果群集中需要32个以上的工作节点, 请选择至少具有8个核心和 14 GB RAM 的头节点大小。 

只有启用了加速写入功能的 Kafka 和 HBase 群集才能使用具有数据磁盘的群集类型。 在这些情况下, HDInsight 支持 P30 和 S30 磁盘大小。

有关每个 VM 类型的规范的详细信息, 请参阅以下文档:

* [常规用途虚拟机大小:Dv2 系列1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [内存优化虚拟机大小:Dv2 系列11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [常规用途虚拟机大小:Av2 系列1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>除巴西南部和日本西部之外的所有受支持区域

> [!Note]
> 若要获取用于 powershell 和其他脚本的 SKU 标识符, 请将`Standard_`添加到下表中所有 VM sku 的开头。 例如, `D12_v2`将变为`Standard_D12_v2`。

| 群集类型 | Hadoop | HBase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| 头：默认 VM 大小 | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| 头：建议的 VM 大小 | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| 辅助角色：默认 VM 大小 | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4个 D12_v2, 每个 broker 2 个 S30 磁盘 |
| 辅助角色：建议的 VM 大小 | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| Zookeeper：默认 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| Zookeeper：建议的 VM 大小 |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| ML 服务: 默认 VM 大小 |  |  |  |  |  | D4_v2 |  |
| ML 服务: 建议的 VM 大小 |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>仅巴西南部和日本西部

| 群集类型 | Hadoop | HBase | Interactive Query | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| 头：默认 VM 大小 | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| 头：建议的 VM 大小 | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| 辅助角色：默认 VM 大小 | D4 | D4 | D14 | D3 | D13 | D4 |
| 辅助角色：建议的 VM 大小 | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Zookeeper：默认 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Zookeeper：建议的 VM 大小 |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| ML 服务: 默认 VM 大小 |  |  |  |  |  | D4 |
| ML 服务: 建议的 VM 大小 |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - 对于 Storm 群集类型，头称为 *Nimbus*。
> - 对于 Storm 群集类型，辅助角色称为“主管”。
> - 对于 HBase 群集类型，辅助角色称为“区域”。

## <a name="next-steps"></a>后续步骤

* [HDInsight 提供了哪些 Apache Hadoop 组件和版本？](hdinsight-component-versioning.md)

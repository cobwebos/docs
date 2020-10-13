---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535604"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

## <a name="release-date-09282020"></a>发布日期：09/28/2020

此版本适用于 HDInsight 3.6 和 HDInsight 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到以下更改，请耐心等待，几天后发行版会在你所在的区域推出。

## <a name="new-features"></a>新增功能
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>使用 HDInsight 4.0 的交互式查询的自动缩放现已正式发布
交互式查询群集类型的自动缩放现已正式发布 (适用于 HDInsight 4.0 的 GA) 。 4.0 年 8 2020 月27日之后创建的所有交互式查询群集都将为自动缩放提供 GA 支持。

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>HBase 群集支持高级 ADLS Gen2
HDInsight 现在支持将高级 ADLS Gen2 作为 HDInsight HBase 3.6 和4.0 群集的主要存储帐户。 与 [加速写入](./hbase/apache-hbase-accelerated-writes.md)一起，你可以为 HBase 群集提供更好的性能。

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Azure 容错域上的 Kafka 分区分布
容错域是 Azure 数据中心基础硬件的逻辑分组。 每个容错域共享公用电源和网络交换机。 在 HDInsight Kafka 可以将所有分区副本存储在同一容错域中。 从此版本开始，HDInsight 现在支持基于 Azure 容错域自动分发 Kafka 分区。 

### <a name="encryption-in-transit"></a>传输中加密
客户可以通过使用 IPSec 加密和平台托管密钥，在群集节点之间传输加密。 可以在创建群集时启用此选项。 查看有关 [如何在传输中启用加密的](./domain-joined/encryption-in-transit.md)更多详细信息。

### <a name="encryption-at-host"></a>主机加密
当你在主机上启用加密时，存储在 VM 主机上的数据将静态加密，并加密为存储服务。 在此版本中，可以在创建群集时，在 **临时数据磁盘上的主机上启用加密** 。 仅在 [有限区域中的特定 VM sku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal)上支持在主机上加密。 HDInsight 支持 [以下节点配置和 sku](./hdinsight-supported-node-configuration.md)。 有关 [如何在主机上启用加密的](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys)详细信息，请参阅。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 目前使用 Azure 虚拟机来预配群集。 从此版本开始，该服务将逐步迁移到 [Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。 整个过程可能需要几个月的时间。 迁移你的区域和订阅后，新创建的 HDInsight 群集将在无需客户操作的虚拟机规模集上运行。 不需要进行重大更改。

## <a name="deprecation"></a>弃用
此版本没有弃用。

## <a name="behavior-changes"></a>行为更改
此版本没有行为更改。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>能够为 Spark、Hadoop 和 ML 服务选择不同的 Zookeeper SKU
HDInsight 目前不支持更改 Spark、Hadoop 和 ML 服务群集类型的 Zookeeper SKU。 它为 Zookeeper 节点使用 A2_v2/A2 SKU，客户无需对此付费。 在即将推出的版本中，客户可以根据需要更改 Spark、Hadoop 和 ML 服务的 Zookeeper SKU。 会对不使用 A2_v2/A2 SKU 的 Zookeeper 节点收取费用。 默认 SKU 仍为 A2_V2/A2 并免费。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
此发行版未发生组件版本更改。 可以在[此文档](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)中查找 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。
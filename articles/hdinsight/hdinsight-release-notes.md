---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ef243d5b151f95a00e22ac7636a46b93090ccce3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006528"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

## <a name="release-date-07132020"></a>发布日期：07/13/2020

此发行版适用于 HDInsight 3.6 和 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到以下更改，请耐心等待，几天后发行版会在你所在的区域推出。

## <a name="new-features"></a>新增功能
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>支持 Microsoft Azure 客户密码箱
Azure HDInsight 现在支持 Azure 客户密码箱。 它为客户提供了一个界面，供客户查看和批准或拒绝客户数据访问请求。 当 Microsoft 工程师需要在支持请求期间访问客户数据时，可以使用此方法。 有关详细信息，请参阅[Microsoft Azure 客户密码箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview)。

### <a name="service-endpoint-policies-for-storage"></a>用于存储的服务终结点策略
现在，客户可以在 HDInsight 群集子网上使用服务终结点策略（SEP）。 了解有关[Azure 服务终结点策略](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)的详细信息。

## <a name="deprecation"></a>弃用
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>弃用 HDInsight 3.6 Spark 群集中的 Spark 2.1 和 2.2
从 2020 年 7 月 1 日起，客户无法使用 HDInsight 3.6 上的 Spark 2.1 和 2.2 创建新的 Spark 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前转移到 HDInsight 3.6 上的 Spark 2.3，以避免潜在的系统/支持中断。
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>弃用 HDInsight 4.0 Spark 群集中的 Spark 2.3
从 2020 年 7 月 1 日起，客户无法使用 HDInsight 4.0 上的 Spark 2.3 创建新的 Spark 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前转移到 HDInsight 4.0 上的 Spark 2.4，避免出现潜在的系统/支持中断。
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>弃用 HDInsight 4.0 Kafka 群集中的 Kafka 1.1
从 2020 年 7 月 1 日开始，客户将无法使用 HDInsight 4.0 上的 Kafka 1.1 创建新的 Kafka 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前转移到 HDInsight 4.0 上的 Spark 2.1，避免出现潜在的系统/支持中断。

## <a name="behavior-changes"></a>行为更改
无需注意任何行为更改。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>能够为 Spark、Hadoop 和 ML 服务选择不同的 Zookeeper SKU
HDInsight 目前不支持更改 Spark、Hadoop 和 ML 服务群集类型的 Zookeeper SKU。 它使用适用于 Zookeeper 节点 A2_v2/A2 SKU，客户不收取费用。 在即将推出的版本中，客户可以根据需要更改 Spark、Hadoop 和 ML 服务的 Zookeeper SKU。 将对 SKU 不是 A2_v2/A2 的 Zookeeper 节点收取费用。 默认 SKU 仍将 A2_V2/A2 并免费。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 
### <a name="fixed-hive-warehouse-connector-issue"></a>固定 Hive 仓库连接器问题
以前版本中的 Hive 仓库连接器可用性存在问题。 此问题已解决。 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>已修复 Zeppelin 笔记本截断前导零问题
Zeppelin 在字符串格式的表输出中错误地截断了前导零。 此版本修复了此问题。

## <a name="component-version-change"></a>组件版本更改
此发行版未发生组件版本更改。 可以在[此文档](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)中找到 hdinsight 4.0 和 hdinsight 3.6 的当前组件版本。

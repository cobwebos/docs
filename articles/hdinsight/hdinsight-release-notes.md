---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: b7489c49b7469feacfd5b982615419741d286998
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849699"
---
# <a name="release-notes"></a>发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>总结

Azure HDInsight 是 Azure 中最受企业客户青睐的开放源代码分析服务之一。

## <a name="release-date-01092020"></a>发行日期：2020 年 1 月 9 日

此版本适用于 HDInsight 3.6 和 4.0。 HDInsight 版本在几天内对所有区域可用。 此处的发行日期指第一个区域发行日期。 如果你看不到下面的更改，请等到几天后在你所在区域进行发行。

> [!IMPORTANT]  
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本控制文章](hdinsight-component-versioning.md)。

## <a name="new-features"></a>新增功能
### <a name="tls-12-enforcement"></a>强制执行 TLS 1.2
传输层安全性 (TLS) 和安全套接字层 (SSL) 是提供计算机网络通信安全的加密协议。 详细了解 [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)。 HDInsight 在公共 HTTPs 终结点上使用 TLS 1.2，但为了向后兼容性仍支持 TLS 1.1。 

在此版本中，客户可以仅针对通过公共群集终结点进行的所有连接选择使用 TLS 1.2。 为支持这种情况，引入了新属性 minSupportedTlsVersion，可以在群集创建过程中进行指定。 如果未设置该属性，则群集仍支持 TLS 1.0、1.1 和 1.2，这与当前的行为相同。 客户可以将此属性的值设置为“1.2”，这意味着群集仅支持 TLS 1.2 和更高版本。 有关详细信息，请参阅[传输层安全性](./transport-layer-security.md)。

### <a name="bring-your-own-key-for-disk-encryption"></a>为磁盘加密创建自己的密钥
通过 Azure 存储服务加密 (SSE) 保护 HDInsight 中的所有托管磁盘。 默认情况下，这些磁盘上的数据通过 Microsoft 托管密钥进行加密。 从此版本开始，可以为磁盘加密创建自己的密钥 (BYOK)，并使用 Azure Key Vault 来管理它。 BYOK 加密是群集创建期间的一步配置，无需额外费用。 只需将 HDInsight 注册为 Azure Key Vault 的托管标识，并在创建群集时添加加密密钥。 有关详细信息，请参阅[客户管理的密钥磁盘加密](https://docs.microsoft.com/azure/hdinsight/disk-encryption)。

## <a name="deprecation"></a>弃用
此版本没有任何弃用功能。 若要为即将推出的弃用功能做好准备，请参阅[即将推出的更改](#upcoming-changes)。

## <a name="behavior-changes"></a>行为变更
此版本没有行为变更。 若要为即将推出的更改做好准备，请参阅[即将推出的更改](#upcoming-changes)。

## <a name="upcoming-changes"></a>即将推出的更改
即将推出的版本中将发生以下更改。 

### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>弃用 HDInsight 3.6 Spark 群集中的 Spark 2.1 和 2.2
从 2020 年 7 月 1 日开始，客户将无法使用 HDInsight 3.6 上的 Spark 2.1 和 2.2 创建新的 Spark 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前转移到 HDInsight 3.6 上的 Spark 2.3，以避免潜在的系统/支持中断。

### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>弃用 HDInsight 4.0 Spark 群集中的 Spark 2.3
从 2020 年 7 月 1 日开始，客户将无法使用 HDInsight 4.0 上的 Spark 2.3 创建新的 Spark 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前转移到 HDInsight 4.0 上的 Spark 2.4，避免出现潜在的系统/支持中断。

### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>弃用 HDInsight 4.0 Kafka 群集中的 Kafka 1.1
从 2020 年 7 月 1 日开始，客户将无法使用 HDInsight 4.0 上的 Kafka 1.1 创建新的 Kafka 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前转移到 HDInsight 4.0 上的 Spark 2.1，避免出现潜在的系统/支持中断。

### <a name="hbase-20-to-216"></a>HBase 2.0 到 2.1.6
在即将推出的 HDInsight 4.0 版本中，HBase 版本将从版本 2.0 升级到 2.1.6

### <a name="spark-240-to-244"></a>Spark 2.4.0 到 2.4.4
在即将推出的 HDInsight 4.0 版本中，Spark 版本将从版本 2.4.0 升级到 2.4.4

### <a name="kafka-210-to-211"></a>Kafka 2.1.0 到 2.1.1
在即将推出的 HDInsight 4.0 版本中，Kafka 版本将从版本 2.1.0 升级到 2.1.1

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>头节点至少需要 4 核 VM 
头节点至少需要 4 核 VM，以确保 HDInsight 群集的高可用性和可靠性。 从 2020 年 4 月 6 日开始，客户只能选择 4 核或以上的 VM 作为新 HDInsight 群集的头节点。 现有群集将继续按预期方式运行。 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 群集节点大小更改 
在即将推出的版本中，ESP Spark 群集允许的最小节点大小将更改为 Standard_D13_V2。 由于 CPU 和内存容量相对较低，因此 A 系列 VM 可能会导致 ESP 群集问题。 创建新 ESP 群集时将弃用 A 系列 VM。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 现在使用 Azure 虚拟机来预配群集。 在即将推出的版本中，HDInsight 将改为使用 Azure 虚拟机规模集。 请参阅有关 Azure 虚拟机规模集的详细信息。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 继续提高群集可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
此版本没有组件版本更改。 可在其中找到适用于 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。


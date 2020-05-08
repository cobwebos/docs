---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 458925dd9f7f7386a9159256fdb024d027f7016c
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929309"
---
# <a name="release-notes"></a>发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

## <a name="release-date-01092020"></a>发行日期：01/09/2020

此发行版适用于 HDInsight 3.6 和 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到以下更改，请等待几天，让发行版在你所在的区域推出。

> [!IMPORTANT]  
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本控制文章](hdinsight-component-versioning.md)。

## <a name="new-features"></a>新增功能
### <a name="tls-12-enforcement"></a>强制执行 TLS 1.2
传输层安全性 (TLS) 和安全套接字层 (SSL) 是提供计算机网络通信安全的加密协议。 详细了解 [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)。 HDInsight 在公共 HTTPs 终结点上使用 TLS 1.2，但仍支持使用 TLS 1.1 以实现后向兼容。 

在此发行版中，客户只能为通过公共群集终结点建立的所有连接启用 TLS 1.2。 为了支持此方案，我们引入了新属性 **minSupportedTlsVersion**，在创建群集期间可以指定此属性。 如果不设置该属性，群集仍支持 TLS 1.0、1.1 和 1.2，这与当前的行为相同。 客户可将此属性的值设置为“1.2”，这意味着，群集仅支持 TLS 1.2 和更高版本。 有关详细信息，请参阅[传输层安全性](./transport-layer-security.md)。

### <a name="bring-your-own-key-for-disk-encryption"></a>创建自己的密钥进行磁盘加密
通过 Azure 存储服务加密 (SSE) 保护 HDInsight 中的所有托管磁盘。 这些磁盘上的数据默认已使用 Microsoft 托管的密钥进行加密。 从此发行版开始，可以创建自己的密钥 (BYOK) 进行磁盘加密，并使用 Azure Key Vault 管理该密钥。 BYOK 加密是创建群集期间完成的单步配置，不额外收费。 只需将 HDInsight 作为托管标识注册到 Azure Key Vault，并在创建群集时添加加密密钥。 有关详细信息，请参阅[客户管理的密钥磁盘加密](https://docs.microsoft.com/azure/hdinsight/disk-encryption)。

## <a name="deprecation"></a>弃用
此版本无弃用。 若要为即将到来的弃用做好准备，请参阅[即将推出的变更](#upcoming-changes)。

## <a name="behavior-changes"></a>行为更改
此版本无行为变更。 若要为即将推出的更改做好准备，请参阅[即将推出的更改](#upcoming-changes)。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。 

### <a name="deprecate-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>HDInsight 中的弃用 Spark 2.1 和 2.2 3.6 Spark 群集
从7月 1 2020 开始，客户将无法在 HDInsight 3.6 上创建新的 Spark 群集和 Spark 2.1 和2.2。 现有群集将按原样运行，无需 Microsoft 的支持。 请考虑在2.3 年 6 30 2020 月的 HDInsight 3.6 上迁移到 Spark，以避免潜在的系统/支持中断。

### <a name="deprecate-spark-23-in-hdinsight-40-spark-cluster"></a>HDInsight 4.0 Spark 群集中的弃用 Spark 2。3
从7月 1 2020 开始，客户将无法在 HDInsight 4.0 上利用 Spark 2.3 创建新的 Spark 群集。 现有群集将按原样运行，无需 Microsoft 的支持。 请考虑在2.4 年 6 30 2020 月的 HDInsight 4.0 上迁移到 Spark，以避免潜在的系统/支持中断。

### <a name="deprecate-kafka-11-in-hdinsight-40-kafka-cluster"></a>HDInsight 4.0 Kafka 群集中的弃用 Kafka 1。1
从7月 1 2020 开始，客户将无法在 HDInsight 4.0 上创建 Kafka 1.1 的新 Kafka 群集。 现有群集将按原样运行，无需 Microsoft 的支持。 请考虑在2.1 年 6 30 2020 月的 Kafka 上移动到 HDInsight 4.0 上的，以避免潜在的系统/支持中断。

### <a name="hbase-20-to-216"></a>HBase 2.0 到2.1。6
在即将推出的 HDInsight 4.0 版本中，HBase 版本将从2.0 版升级到2.1。6

### <a name="spark-240-to-244"></a>Spark 2.4.0 到2.4。4
在即将推出的 HDInsight 4.0 版本中，Spark 版本将从版本2.4.0 升级到2.4。4

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>头节点需要至少4核 VM 
头节点需要最少的4核 VM，以确保 HDInsight 群集的高可用性和可靠性。 从 6 2020 年4月开始，客户只能选择4核或以上的 VM 作为新 HDInsight 群集的头节点。 现有群集将继续按预期方式运行。 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 群集节点大小变更 
在即将发布的版本中，ESP Spark 群集允许的最小节点大小将更改为 Standard_D13_V2。 由于 CPU 和内存容量相对较低，因此 A 系列 Vm 可能会导致 ESP 群集问题。 为创建新的 ESP 群集，将不推荐使用 A 系列 Vm。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 目前使用 Azure 虚拟机来预配群集。 在即将发布的版本中，HDInsight 将改为使用 Azure 虚拟机规模集。 详细了解 Azure 虚拟机规模集。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
此版本没有更改组件版本。 可在此处查找 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。


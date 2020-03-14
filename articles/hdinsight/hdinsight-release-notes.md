---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 等的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: b83828c3c78913598c103730e11222969fe1fddb
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370162"
---
# <a name="release-notes"></a>发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>总结

Azure HDInsight 是 Azure 上的开源分析企业客户中最受欢迎的服务之一。

## <a name="release-date-01092020"></a>发布日期：01/09/2020

此版本适用于 HDInsight 3.6 和4.0。 HDInsight 版本将在几天内提供给所有区域。 此处的发布日期指示第一个区域发布日期。 如果看不到下面的更改，请等待几天内发布在你的区域中。

> [!IMPORTANT]  
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本控制文章](hdinsight-component-versioning.md)。

## <a name="new-features"></a>新增功能
### <a name="tls-12-enforcement"></a>强制执行 TLS 1.2
传输层安全性 (TLS) 和安全套接字层 (SSL) 是提供计算机网络通信安全的加密协议。 详细了解[TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)。 HDInsight 在公共 HTTPs 终结点上使用 TLS 1.2，但仍支持使用 TLS 1.1 来实现向后兼容性。 

在此版本中，客户可以仅针对通过公共群集终结点进行的所有连接选择使用 TLS 1.2。 为支持这种情况，会引入新的属性**minSupportedTlsVersion** ，并可在创建群集期间指定。 如果未设置该属性，则群集仍支持 TLS 1.0、1.1 和1.2，这与今天的行为相同。 客户可以将此属性的值设置为 "1.2"，这意味着群集仅支持 TLS 1.2 和更高版本。 有关详细信息，请参阅[规划虚拟网络-传输层安全性](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security)。

### <a name="bring-your-own-key-for-disk-encryption"></a>自带密钥进行磁盘加密
通过 Azure 存储服务加密 (SSE) 保护 HDInsight 中的所有托管磁盘。 默认情况下，这些磁盘上的数据是由 Microsoft 管理的密钥加密的。 从此版本开始，你可以创建自己的密钥（BYOK）进行磁盘加密，并使用 Azure Key Vault 来管理它。 BYOK 加密是在群集创建过程中进行的一步配置，无需额外付费。 只需使用 Azure Key Vault 将 HDInsight 注册为托管标识，并在创建群集时添加加密密钥。 有关详细信息，请参阅[客户托管的密钥磁盘加密](https://docs.microsoft.com/azure/hdinsight/disk-encryption)。

## <a name="deprecation"></a>弃用
此版本没有弃用功能。 若要为即将推出的弃用功能做好准备，请参阅[即将发生的更改](#upcoming-changes)。

## <a name="behavior-changes"></a>行为更改
此版本不会更改行为。 若要准备即将发生的更改，请参阅[即将发生的更改](#upcoming-changes)。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将发生以下更改。 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>头节点需要至少4核 VM 
头节点需要最少的4核 VM，以确保 HDInsight 群集的高可用性和可靠性。 从2020年4月6日开始，客户只能选择4核或以上的 VM 作为新 HDInsight 群集的头节点。 现有群集将继续按预期方式运行。 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 群集节点大小更改 
在即将发布的版本中，ESP Spark 群集允许的最小节点大小将更改为 Standard_D13_V2。 由于 CPU 和内存容量相对较低，因此 A 系列 Vm 可能会导致 ESP 群集问题。 为创建新的 ESP 群集，将不推荐使用 A 系列 Vm。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 现在使用 Azure 虚拟机来设置群集。 在即将发布的版本中，HDInsight 将改为使用 Azure 虚拟机规模集。 详细了解 Azure 虚拟机规模集。

### <a name="hbase-20-to-21"></a>HBase 2.0 到2。1
在即将推出的 HDInsight 4.0 版本中，HBase 版本将从版本2.0 升级到2.1。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 继续提高群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
此版本没有更改组件版本。 可在此处找到 HDInsight 4.0 ad HDInsight 3.6 的当前组件版本。

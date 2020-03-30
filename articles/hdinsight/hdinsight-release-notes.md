---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e5a96d2eb67937ce4eeaa1999d8168e7f5d3d926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130189"
---
# <a name="release-notes"></a>发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>总结

Azure HDInsight 是企业客户中最受欢迎的 Azure 开源分析服务之一。

## <a name="release-date-01092020"></a>发布日期： 01/09/2020

此发行版适用于 HDInsight 3.6 和 4.0。 HDInsight 版本可在几天内提供给所有区域。 此处的发布日期表示第一个区域发布日期。 如果您没有看到以下更改，请等待该版本在几天内在您所在的地区中使用。

> [!IMPORTANT]  
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本控制文章](hdinsight-component-versioning.md)。

## <a name="new-features"></a>新增功能
### <a name="tls-12-enforcement"></a>强制执行 TLS 1.2
传输层安全性 (TLS) 和安全套接字层 (SSL) 是提供计算机网络通信安全的加密协议。 了解有关[TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)的更多。 HDInsight 在公共 TSP 终结点上使用 TLS 1.2，但 TLS 1.1 仍支持向后兼容性。 

使用此版本，客户只能选择通过公共群集终结点的所有连接加入 TLS 1.2。 为此，引入了新属性**minSupportTlsVersion，** 可以在群集创建期间指定。 如果未设置该属性，群集仍支持 TLS 1.0、1.1 和 1.2，这与今天的行为相同。 客户可以将此属性的值设置为"1.2"，这意味着群集仅支持 TLS 1.2 及以上。 有关详细信息，请参阅[规划虚拟网络 - 传输层安全性](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security)。

### <a name="bring-your-own-key-for-disk-encryption"></a>自带密钥进行磁盘加密
通过 Azure 存储服务加密 (SSE) 保护 HDInsight 中的所有托管磁盘。 默认情况下，这些磁盘上的数据由 Microsoft 管理的密钥加密。 从此版本开始，您可以自带密钥 （BYOK） 进行磁盘加密，并使用 Azure 密钥保管库对其进行管理。 BYOK 加密是在群集创建期间进行的单步配置，无需额外费用。 只需将 HDInsight 注册为 Azure 密钥保管库的托管标识，并在创建群集时添加加密密钥。 有关详细信息，请参阅[客户管理密钥磁盘加密](https://docs.microsoft.com/azure/hdinsight/disk-encryption)。

## <a name="deprecation"></a>弃用
此版本无弃用。 若要为即将到来的弃用做好准备，请参阅[即将推出的变更](#upcoming-changes)。

## <a name="behavior-changes"></a>行为更改
此版本无行为变更。 要为即将发生的更改做好准备，请参阅[即将发生的更改](#upcoming-changes)。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>头节点至少需要 4 核 VM 
头节点至少需要 4 核 VM，以确保 HDInsight 群集的高可用性和可靠性。 从 2020 年 4 月 6 日起，客户只能选择 4 核或以上 VM 作为新 HDInsight 群集的头节点。 现有群集将继续按预期运行。 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 群集节点大小变更 
在即将发布的版本中，ESP Spark 群集的最小允许节点大小将更改为Standard_D13_V2。 由于 CPU 和内存容量相对较低，A 系列 VM 可能会导致 ESP 群集问题。 A 系列 VM 将被弃用以创建新的 ESP 群集。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 目前使用 Azure 虚拟机来预配群集。 在即将发布的版本中，HDInsight 将改用 Azure 虚拟机缩放集。 详细了解 Azure 虚拟机规模集。

### <a name="hbase-20-to-21"></a>HBase 2.0 到 2.1
在即将推出的 HDInsight 4.0 版本中，HBase 版本将从 2.0 升级到 2.1。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
此版本的组件版本未更改。 可在此处查找 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。

## <a name="known-issues"></a>已知问题

截至 2020 年 3 月 18 日，西欧或北欧的一些 Azure HDInsight 客户在这些地区创建或扩展 HDInsight 群集时收到错误通知。 与此问题相关的错误包括：

- 处理请求时发生内部服务器错误。 请重试请求或联系支持人员。
- 至少有一个资源部署操作失败。 有关详细信息，请列出部署操作。 有关使用https://aka.ms/DeployOperations详情，请参阅
- 用户订阅 Id\<'\>订阅 ID ' 没有内核用于创建\<资源"群集名称>"。 要求： \<\>X ， 可用： 0.

工程师们意识到了这个问题，并正在积极调查。

有关其他帮助，请创建[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 等的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435402"
---
# <a name="release-notes"></a>发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源 Apache Hadoop 和 Apache Spark 分析服务之一。

## <a name="release-date-12172019"></a>发布日期：12/17/2019

此版本适用于 HDInsight 3.6 和4.0。

> [!IMPORTANT]  
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本控制文章](hdinsight-component-versioning.md)。


## <a name="new-features"></a>新增功能

### <a name="service-tags"></a>服务标记
服务标记通过使你能够轻松地限制对 Azure 服务的网络访问，简化了 Azure 虚拟机和 Azure 虚拟网络的安全性。 可以在网络安全组（NSG）规则中使用服务标记，以允许或拒绝全局或每个 Azure 区域的特定 Azure 服务的流量。 Azure 提供每个标记的基础 IP 地址的维护。 网络安全组（Nsg）的 HDInsight 服务标记是运行状况和管理服务的 IP 地址组。 这些组有助于最大程度地降低安全规则创建的复杂性。 HDInsight 客户可以通过 Azure 门户、PowerShell 和 REST API 启用服务标记。 有关详细信息，请参阅[Azure HDInsight 的网络安全组（NSG）服务标记](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags)。

### <a name="custom-ambari-db"></a>自定义 Ambari DB
HDInsight 现在允许你将自己的 SQL DB 用于 Apache Ambari。 可以从 Azure 门户或 resource manager 模板配置此自定义 Ambari DB。  此功能可让你为处理和容量需求选择合适的 SQL DB。 你还可以轻松升级，以满足业务增长要求。 有关详细信息，请参阅[设置具有自定义 AMBARI DB 的 HDInsight 群集](hdinsight-custom-ambari-db.md)。

![自定义 Ambari DB](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>弃用
此版本没有弃用功能。 若要为即将推出的弃用功能做好准备，请参阅[即将发生的更改](#upcoming-changes)。

## <a name="behavior-changes"></a>行为变更
此版本不会更改行为。 若要准备即将发生的行为更改，请参阅[即将发生的更改](#upcoming-changes)。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将发生以下更改。 

### <a name="transport-layer-security-tls-12-enforcement"></a>传输层安全性（TLS）1.2 强制执行
传输层安全性 (TLS) 和安全套接字层 (SSL) 是提供计算机网络通信安全的加密协议。 有关详细信息，请参阅[传输层安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)。 虽然 Azure HDInsight 群集接受公有 HTTPS 终结点上的 TLS 1.2 连接，但仍支持 TLS 1.1，以便与较旧的客户端向后兼容。

从下一版本开始，你将能够选择加入并配置你的新 HDInsight 群集，以仅接受 TLS 1.2 连接。 

稍后，从6/30/2020 开始，Azure HDInsight 将为所有 HTTPS 连接强制实施 TLS 1.2 或更高版本。 建议确保所有客户端都已准备好处理 TLS 1.2 或更高版本。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 现在使用 Azure 虚拟机来设置群集。 从时间2020开始（将在稍后传达准确的日期），HDInsight 将改为使用 Azure 虚拟机规模集。 详细了解[Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 群集节点大小更改 
在即将发布的版本中：
- 最小允许的 ESP Spark 群集节点大小将更改为 Standard_D13_V2。 
- 将不推荐使用 a 系列 Vm 来创建新的 ESP 群集，因为一个系列 Vm 可能会因为相对较低的 CPU 和内存容量而导致 ESP 群集问题。

### <a name="hbase-20-to-21"></a>HBase 2.0 到2。1
在即将推出的 HDInsight 4.0 版本中，HBase 版本将从版本2.0 升级到2.1。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 继续提高群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
我们已将 HDInsight 3.6 支持扩展到2020年12月31日。 可以在[支持的 HDInsight 版本](hdinsight-component-versioning.md#supported-hdinsight-versions)中找到更多详细信息。

没有更改 HDInsight 4.0 的组件版本。

Apache Zeppelin on HDInsight 3.6：0.7.0 编写--> iisnode-inspector-0.7.3.dll。 

你可以从[该文档](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)中找到最新的组件版本。

## <a name="new-regions"></a>新区域

### <a name="uae-north"></a>阿拉伯联合酋长国北部
阿拉伯联合酋长国北部的管理 Ip 是： `65.52.252.96` 和 `65.52.252.97`。

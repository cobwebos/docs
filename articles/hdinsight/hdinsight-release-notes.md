---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 54e28a9d434500915aa8cc8e07ade3592e5aa96c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185603"
---
# <a name="release-notes"></a>发行说明

本文提供有关**最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源 Apache Hadoop 和 Apache Spark 分析服务之一。

## <a name="release-date-11072019"></a>发布日期：11/07/2019

此版本适用于 HDInsight 3.6 和4.0。

> [!IMPORTANT]  
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本控制文章](hdinsight-component-versioning.md)。


## <a name="new-features"></a>新增功能

### <a name="hdinsight-identity-broker-hib-preview"></a>HDInsight 标识代理（HIB）（预览版）

HDInsight 标识代理（HIB）可让用户使用多重身份验证（MFA）登录到 Apache Ambari，并获取所需的 Kerberos 票证，而不需要 Azure Active Directory 域服务（AAD-DS）中的密码哈希。 当前 HIB 仅适用于通过 ARM 模板部署的群集。

### <a name="kafka-rest-api-proxy-preview"></a>Kafka Rest API 代理（预览）

Kafka Rest API 代理通过安全 AAD 授权和 OAuth 协议，通过 Kafka 群集提供一次单击部署高可用性 REST 代理。 

### <a name="auto-scale"></a>自动缩放

Azure HDInsight 的自动缩放现已在适用于 Apache Spark 和 Hadoop 群集类型的所有区域公开提供。 此功能可让你以更具成本效益和高效的方式管理大数据分析工作负荷。 现在，你可以优化 HDInsight 群集的使用，并且仅为所需的部分付费。

根据你的要求，你可以选择基于负载和计划的自动缩放。 基于负载的自动缩放可根据当前资源需求，根据当前资源需求调整群集大小，而基于计划的自动缩放可以根据预定义的计划更改群集大小。 

HBase 和 LLAP 工作负荷的自动缩放支持也是公共预览。 有关详细信息，请参阅[自动缩放 Azure HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters)。

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>Apache HBase 的 HDInsight 加速写入 

加速写入使用 Azure 高级 SSD 托管磁盘，可以改善 Apache HBase 预写日志 (WAL) 的性能。 有关详细信息，请参阅[适用于 Apache HBase 的 Azure HDInsight 加速写入](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes)。

### <a name="custom-ambari-db"></a>自定义 Ambari DB

HDInsight 现在提供了新容量，使客户能够使用自己的 SQL DB 进行 Ambari。 现在，客户可以选择合适的 SQL DB 进行 Ambari，并根据自己的业务增长要求轻松地对其进行升级。 部署是使用 Azure 资源管理器模板来完成的。 有关详细信息，请参阅[设置具有自定义 AMBARI DB 的 HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db)。

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>现在，HDInsight 提供了 F 系列虚拟机

F 系列虚拟机（Vm）非常适合用于满足轻型处理要求的 HDInsight。 根据每个 vCPU 的 Azure 计算单位 (ACU)，在较低的小时价列表中，F 系列在 Azure 产品组合中具有最高性价比。 有关详细信息，请参阅[为 Azure HDInsight 群集选择正确的 VM 大小](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size)。

## <a name="deprecation"></a>弃用

### <a name="g-series-virtual-machine-deprecation"></a>G 系列虚拟机弃用
在此版本中，HDInsight 中不再提供 G 系列 Vm。

### <a name="dv1-virtual-machine-deprecation"></a>Dv1 虚拟机弃用
在此版本中，已弃用将 Dv1 Vm 与 HDInsight 配合使用。 将自动为 Dv1 的任何客户请求提供 Dv2。 Dv1 和 Dv2 Vm 之间没有价格差别。

## <a name="behavior-changes"></a>行为更改

### <a name="cluster-managed-disk-size-change"></a>群集托管磁盘大小更改
HDInsight 通过群集提供托管磁盘空间。 在此版本中，新创建的群集中每个节点的托管磁盘大小更改为 128 GB。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将发生以下更改。 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 现在使用 Azure 虚拟机来设置群集。 从12月开始，HDInsight 将改为使用 Azure 虚拟机规模集。 详细了解[Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。

### <a name="hbase-20-to-21"></a>HBase 2.0 到2。1
在即将推出的 HDInsight 4.0 版本中，HBase 版本将从版本2.0 升级到2.1。

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>ESP 群集的 A 系列虚拟机弃用
A 系列 Vm 可能会导致 ESP 群集出现问题，因为 CPU 和内存容量相对较低。 在即将发布的版本中，将不推荐使用 A 系列 Vm 来创建新的 ESP 群集。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 继续提高群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
此版本没有更改组件版本。 可在[此处](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)找到 HDInsight 4.0 ad HDInsight 3.6 的当前组件版本。

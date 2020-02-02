---
title: 排查 Azure HDInsight 群集创建失败问题
description: 了解如何排查 Azure HDInsight 的 Apache 群集创建问题。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.openlocfilehash: 1e13c7ef8eae81ef2a12a8761b0596f6329f94dc
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937911"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>排查 Azure HDInsight 群集创建失败问题

以下问题是导致群集创建失败的最常见的根本原因：

- 权限问题
- 资源策略限制
- 防火墙
- 资源锁
- 组件版本不受支持
- 存储帐户名称限制
- 服务中断

## <a name="permissions-issues"></a>权限问题

如果你使用的是 Azure Data Lake Storage Gen2，并收到错误 ```AmbariClusterCreationFailedErrorCode```，请 ```Internal server error occurred while processing the request. Please retry the request or contact support.```，打开 Azure 门户，访问你的存储帐户，并在 "访问控制（IAM）" 下，确保**存储 Blob 数据参与者**或**存储 blob 数据所有者**角色已分配给该订阅的**用户分配的托管标识**。 有关详细说明，请参阅[在 Data Lake Storage Gen2 帐户上设置托管标识的权限](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account)。

如果使用 Azure Data Lake Storage Gen1，请参阅[此处](../hdinsight-hadoop-use-data-lake-store.md)的设置和配置说明。 HBase 群集不支持 Data Lake Storage Gen1，HDInsight 版本4.0 不支持此项。

如果使用 Azure 存储，请确保在创建群集期间存储帐户名称有效。

## <a name="resource-policy-restrictions"></a>资源策略限制

基于订阅的 Azure 策略可以拒绝创建公共 IP 地址。 创建 HDInsight 群集需要两个公共 IP。  

通常，以下策略可能会影响群集创建：

* 禁止在订阅中创建 IP 地址 & 负载平衡器的策略。
* 阻止创建存储帐户的策略。
* 禁止删除网络资源的策略（IP 地址/Load 均衡器）。

## <a name="firewalls"></a>防火墙

虚拟网络或存储帐户上的防火墙可以拒绝与 HDInsight 管理 IP 地址的通信。

允许来自下表中的 IP 地址的流量。

| 源 IP 地址 | 目标 | 方向 |
|---|---|---|
| 168.61.49.99 | *：443 | 入站 |
| 23.99.5.239 | *：443 | 入站 |
| 168.61.48.131 | *：443 | 入站 |
| 138.91.141.162 | *：443 | 入站 |

还要添加特定于创建群集的区域的 IP 地址。 有关每个 Azure 区域的地址列表，请参阅[HDInsight 管理 IP 地址](../hdinsight-management-ip-addresses.md)。

如果你使用的是快速路由或你自己的自定义 DNS 服务器，请参阅为[Azure HDInsight 规划虚拟网络-连接多个网络](../hdinsight-plan-virtual-network-deployment.md#multinet)。

## <a name="resources-locks"></a>资源锁  

确保[你的虚拟网络和资源组](../../azure-resource-manager/management/lock-resources.md)没有任何锁。 如果资源组被锁定，则无法创建或删除群集。 

## <a name="unsupported-component-versions"></a>组件版本不受支持

确保你使用的是[受支持版本的 Azure HDInsight](../hdinsight-component-versioning.md)和解决方案中的任何[Apache Hadoop 组件](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)。  

## <a name="storage-account-name-restrictions"></a>存储帐户名称限制

存储帐户名称不能超过24个字符，并且不能包含特殊字符。 这些限制也适用于存储帐户中的默认容器名称。

其他命名限制还适用于群集创建。 有关详细信息，请参阅[群集名称限制](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)。

## <a name="service-outages"></a>服务中断

检查[Azure 状态](https://status.azure.com)以了解任何潜在的中断或服务问题。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 虚拟网络扩展 Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [将 Azure 存储与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-blob-storage.md)
* [使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他组件在 HDInsight 中设置群集](../hdinsight-hadoop-provision-linux-clusters.md)

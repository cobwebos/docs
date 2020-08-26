---
title: 群集管理最佳做法 - Azure HDInsight
description: 了解管理 HDInsight 群集的最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "74782016"
---
# <a name="hdinsight-cluster-management-best-practices"></a>HDInsight 群集管理最佳做法

了解管理 HDInsight 群集的最佳做法。

## <a name="how-do-i-create-hdinsight-clusters"></a>如何创建 HDInsight 群集？

| 选项 | 文档 |
|---|---|
| Azure 数据工厂 | [使用 Azure 数据工厂在 HDInsight 中创建按需 Apache Hadoop 群集](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| 自定义资源管理器模板 | [使用资源管理器模板在 HDInsight 中创建 Apache Hadoop 群集](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| 快速入门模板 | [HDInsight 快速入门模板](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure 示例 | [HDInsight Azure 示例](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Azure 门户 | [使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [使用 Azure CLI 创建 HDInsight 群集](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [使用 Azure PowerShell 在 HDInsight 中创建基于 Linux 的群集](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [使用 Azure REST API 创建 Apache Hadoop 群集](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK（.NET、Python、Java） | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)、[Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)、[Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)、[Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> 如果要创建群集，并重新使用以前创建的群集的群集名称，请等待此前的群集删除操作完成，然后再创建群集。

## <a name="how-do-i-customize-hdinsight-clusters"></a>如何自定义 HDInsight 群集？

| 选项 | 文档 |
|---|---|
| 脚本操作 | [使用脚本操作自定义 Azure HDInsight 群集](./hdinsight-hadoop-customize-cluster-linux.md) |
| Bootstrap | [使用 Bootstrap 自定义 HDInsight 群集](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| 外部元存储 | [在 Azure HDInsight 中使用外部元数据存储](./hdinsight-use-external-metadata-stores.md) |
| 自定义 Ambari DB | [使用自定义 Ambari 数据库设置 HDInsight 群集](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>创建群集时，我可能会遇到什么错误？

| 错误 | 详细信息 |
|---|---|
| 没有配额 | 可以在每个区域的订阅上创建配额数的配额。 有关详细信息，请参阅[容量规划：配额](./hdinsight-capacity-planning.md)。 |
| 没有更多的可用 IP 地址 | 每个 VNet 都有有限数量的 IP 地址。 创建 HDInsight 群集时，每个节点（包括 zookeeper 和网关节点）将使用其中一些分配的 IP 地址。 如果所有 IP 地址都在使用中，则会遇到此错误。  |
| 网络安全组 (NSG) 规则不允许与 HDInsight 资源提供程序通信 | 如果使用 NSG 或用户定义的路由 (UDR) 来控制流向 HDInsight 群集的入站流量，则必须确保群集能够与关键的 Azure 运行状况和管理服务通信。 有关详细信息，请参阅 [Azure HDInsight 的网络安全组 (NSG) 服务标记](./hdinsight-service-tags.md) |
| 重新使用群集名称 | 使用之前已用过的群集名称时，需要等待 X 分钟，然后再重新创建群集。 否则会看到一条消息，指出该资源已存在。 |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>如何管理正在运行的 HDInsight 群集？

| 选项 | 文档 |
|---|---|
| 自动缩放 | [自动缩放 Azure HDInsight 群集](./hdinsight-autoscale-clusters.md) |
| 手动缩放 | [缩放 Azure HDInsight 群集](./hdinsight-scaling-best-practices.md) |
| 使用 Ambari 进行监视| [在 Azure HDInsight 中监视群集性能](./hdinsight-key-scenarios-to-monitor.md) |
| 使用 Azure Monitor 日志进行监视 | [使用 Azure Monitor 日志监视 HDInsight 群集](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>如何检查已删除的 HDInsight 群集？

### <a name="azure-monitor-logs"></a>Azure Monitor 日志

可以使用以下查询和 Azure Monitor 日志来监视已删除的群集。

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>后续步骤

* [HDInsight 群集的容量规划](./hdinsight-capacity-planning.md)
* [Azure HDInsight 的默认和推荐节点配置是什么？](./hdinsight-supported-node-configuration.md)
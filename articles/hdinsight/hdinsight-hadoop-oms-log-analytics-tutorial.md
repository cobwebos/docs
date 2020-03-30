---
title: 使用 Azure 监视器日志监视 Azure HDInsight 群集
description: 了解如何使用 Azure 监视器日志监视在 HDInsight 群集中运行的作业。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162780"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>使用 Azure Monitor 日志监视 HDInsight 群集

了解如何启用 Azure 监视器日志以监视 HDInsight 中的 Hadoop 群集操作，以及如何添加 HDInsight 监视解决方案。

[Azure 监视器日志](../log-analytics/log-analytics-overview.md)是 Azure 监视器中的一项服务，用于监视云和本地环境，以保持其可用性和性能。 它可以收集云和本地环境中的资源生成的数据以及其他监视工具的数据，针对多个源提供分析。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

如果没有 Azure 订阅，请先[创建一个免费帐户](https://azure.microsoft.com/free/)。"

## <a name="prerequisites"></a>先决条件

* Log Analytics 工作区。 您可以将此工作区视为具有自己的数据存储库、数据源和解决方案的唯一 Azure 监视器日志环境。 有关说明，请参阅[创建 Log Analytics 工作区](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)。

* 一个 Azure HDInsight 群集。 目前，您可以将 Azure 监视器日志与以下 HDInsight 群集类型一起使用：

  * Hadoop
  * HBase
  * 交互式查询
  * Kafka
  * Spark
  * Storm

  有关如何创建 HDInsight 群集的说明，请参阅 [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。  

* Azure PowerShell Az 模块。  请参阅[介绍新的 Azure PowerShell Az 模块](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)。 确保您拥有最新版本。 如有必要，运行`Update-Module -Name Az`。

> [!NOTE]  
> 建议将 HDInsight 群集和 Log Analytics 工作区放置在同一区域中，以实现更好的性能。 Azure 监视器日志并非在所有 Azure 区域都可用。

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>使用门户启用 Azure 监视器日志

在本部分中，将配置现有 HDInsight Hadoop 群集，以使用 Azure Log Analytics 工作区来监视作业、调试日志等等。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择群集。  有关说明，请参阅[列出和显示群集](./hdinsight-administer-use-portal-linux.md#showClusters)。 群集会在新的门户页中打开。

1. 从左侧，在 **"监视"** 下，选择**Azure 监视器**。

1. 从主视图，在**Azure 监视器集成**下，选择**启用**。

1. 在“选择工作区”下拉列表中，选择现有的 Log Analytics 工作区****。

1. 选择“保存”。****  需要几分钟来保存设置。

    ![启用对 HDInsight 群集的监控](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "启用对 HDInsight 群集的监控")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>使用 Azure PowerShell 启用 Azure 监视器日志

您可以使用 Azure PowerShell Az 模块[启用-AzHDInsight 监视](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring)cmdlet 启用 Azure 监视器日志。

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

要禁用，请使用[禁用-AzHDInsight 监控](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)cmdlet：

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>安装 HDInsight 群集管理解决方案

HDInsight 提供了特定于群集的管理解决方案，您可以为 Azure 监视器日志添加这些解决方案。 [管理解决方案](../log-analytics/log-analytics-add-solutions.md)向 Azure 监视器日志添加功能，提供其他数据和分析工具。 这些解决方案从 HDInsight 群集中收集重要的性能指标，并提供搜索指标的工具。 这些解决方案还为 HDInsight 支持的大多数群集类型提供可视化和仪表板。 使用解决方案收集指标后，即可利用这些指标创建自定义监视规则和警报。

以下是可用的 HDInsight 解决方案：

* HDInsight Hadoop 监视
* HDInsight HBase 监视
* HDInsight 交互式查询监视
* HDInsight Kafka 监视
* HDInsight Spark 监视
* HDInsight Storm 监视

有关安装管理解决方案的说明，请参阅 [Azure 中的管理解决方案](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)。 要进行试验，请安装 HDInsight Hadoop 监控解决方案。 完成后，您将看到**摘要**下列出的**HDInsightHadoop**磁贴。 选择“HDInsightHadoop”磁贴****。 HDInsightHadoop 解决方案如下所示：

![HDInsight 监视解决方案视图](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

由于群集是全新的群集，因此报告不会显示任何活动。

## <a name="configuring-performance-counters"></a>配置性能计数器

Azure 监视器还支持收集和分析群集中节点的性能指标。 有关启用和配置此功能的详细信息，请参阅[Azure 监视器 中的 Linux 性能数据源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters)。

## <a name="cluster-auditing"></a>群集审核

HDInsight 通过导入以下类型的日志来支持使用 Azure 监视器日志进行群集审核：

* `log_gateway_audit_CL`- 此表提供来自群集网关节点的审核日志，这些节点显示成功的登录尝试和失败的登录尝试。
* `log_auth_CL`- 此表为 SSH 日志提供成功且失败的登录尝试。
* `log_ambari_audit_CL`- 此表提供来自 Ambari 的审核日志。
* `log_ranger_audti_CL`- 此表在 ESP 群集上提供来自 Apache Ranger 的审核日志。

## <a name="next-steps"></a>后续步骤

* [查询 Azure 监视器日志以监视 HDInsight 群集](hdinsight-hadoop-oms-log-analytics-use-queries.md)

---
title: 使用 Log Analytics 监视 Azure HDInsight 群集
description: 了解如何使用 Azure Log Analytics 监视在 HDInsight 群集中运行的作业。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jasonh
ms.openlocfilehash: 990a836a405c7baf6327e625aa31a9828f217d9f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600971"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>使用 Azure Log Analytics 监视 HDInsight 群集

了解如何启用 Azure Log Analytics 监视 HDInsight 中的 Hadoop 群集操作，以及如何添加 HDInisght 监视解决方案。

[Log Analytics](../log-analytics/log-analytics-overview.md) 是一个服务，用于监视云和本地环境，使其保持较高的可用性和性能。 它可以收集云和本地环境中的资源生成的数据以及其他监视工具的数据，针对多个源提供分析。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

* Log Analytics 工作区。 可将工作区视为独特的 Log Analytics 环境，其中包含自身的数据存储库、数据源和解决方案。 有关说明，请参阅[创建 Log Analytics 工作区](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)。

* **一个 Azure HDInsight 群集**。 目前，可以将 Log Analytics 与以下 HDInsight 群集类型配合使用：

  * Hadoop
  * HBase
  * 交互式查询
  * Kafka
  * Spark
  * Storm

  有关如何创建 HDInsight 群集的说明，请参阅 [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

> [!NOTE]
> 建议将 HDInsight 群集和 Log Analytics 工作区放置在同一区域中，以实现更好的性能。 请注意，Azure Log Analytics 并非在所有 Azure 区域中均可用。

## <a name="enable-log-analytics-by-using-the-portal"></a>使用门户启用 Log Analytics

在本部分中，将配置现有 HDInsight Hadoop 群集，以使用 Azure Log Analytics 工作区来监视作业、调试日志等等。

1. 在 Azure 门户中打开 HDInsight 群集。
2. 在左窗格中，选择“监视”。
3. 在右窗格中，选择“启用”，选择现有的 Log Analytics 工作区，然后选择“保存”。

    ![启用 HDInsight 群集监视](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Enable monitoring for HDInsight clusters")

    需要几分钟来保存设置。

## <a name="enable-log-analytics-by-using-azure-powershell"></a>使用 Azure PowerShell 启用 Log Analytics

可以使用 Azure PowerShell 启用 Log Analytics。 cmdlet 为：

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

请参阅 [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0)。

若要禁用，cmdlet 为：

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

请参阅 [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0)。

## <a name="install-hdinsight-cluster-management-solutions"></a>安装 HDInsight 群集管理解决方案

HDInsight 提供特定于群集的管理解决方案，可为 Azure Log Analytics 添加这些解决方案。 [管理解决方案](../log-analytics/log-analytics-add-solutions.md)可将功能添加到 Log Analytics，提供更多数据和分析工具。 这些解决方案从 HDInsight 群集中收集重要的性能指标，并提供搜索指标的工具。 这些解决方案还为 HDInsight 支持的大多数群集类型提供可视化和仪表板。 使用解决方案收集指标后，即可利用这些指标创建自定义监视规则和警报。

以下是可用的 HDInsight 解决方案：

* HDInsight Hadoop 监视
* HDInsight HBase 监视
* HDInsight 交互式查询监视
* HDInsight Kafka 监视
* HDInsight Spark 监视
* HDInsight Storm 监视

有关安装管理解决方案的说明，请参阅 [Azure 中的管理解决方案](../monitoring/monitoring-solutions.md#install-a-management-solution)。 若要进行试验，请安装 HDInsight Hadoop 监视解决方案。 完成后，将看到“摘要”下列出的“HDInsightHadoop”磁贴。 选择“HDInsightHadoop”磁贴。 HDInsightHadoop 解决方案如下所示：

![HDInsight OMS Hadoop 监视解决方案视图](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

由于群集是全新的群集，因此报告不会显示任何活动。

## <a name="next-steps"></a>后续步骤

* [查询 Azure Log Analytics，监视 Azure HDInsight 群集](hdinsight-hadoop-oms-log-analytics-use-queries.md)

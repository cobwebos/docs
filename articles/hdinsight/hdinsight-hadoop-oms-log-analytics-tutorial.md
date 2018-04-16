---
title: 使用 Azure Log Analytics 监视 Azure HDInsight 群集 | Microsoft Docs
description: 了解如何使用 Azure Log Analytics 监视在 HDInsight 群集中运行的作业。
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: be71b065999c30546432895804df8633da21b43e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>使用 Azure Log Analytics 监视 HDInsight 群集

了解如何使用 Azure Log Analytics 监视 HDInsight 中的 Hadoop 群集操作。

[Log Analytics](../log-analytics/log-analytics-overview.md) 是一个服务，用于监视云和本地环境，使其保持较高的可用性和性能。 它可以收集云和本地环境中的资源生成的数据以及其他监视工具的数据，针对多个源提供分析。 

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 在开始学习本教程之前，必须有一个 Azure 订阅。 请参阅[立即创建免费 Azure 帐户](https://azure.microsoft.com/free)。

* **一个 Azure HDInsight 群集**。 目前，可以将 Log Analytics 与以下 HDInsight 群集类型配合使用：

    * Hadoop
    * HBase
    * 交互式查询
    * Kafka
    * Spark
    * Storm

    有关如何创建 HDInsight 群集的说明，请参阅 [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

* Log Analytics 工作区。 可将工作区视为独特的 Log Analytics 环境，其中包含自身的数据存储库、数据源和解决方案。 必须具有一个已创建的此类工作区，该工作区可以与 Azure HDInsight 群集关联。 有关说明，请参阅[创建 Log Analytics 工作区](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)。

## <a name="enable-log-analytics-by-using-the-portal"></a>使用门户启用 Log Analytics

在本部分中，将配置现有 HDInsight Hadoop 群集，以使用 Azure Log Analytics 工作区来监视作业、调试日志等等。

1. 在 Azure 门户中打开 HDInsight 群集。
2. 在左窗格中，单击“监视”。
3. 在右窗格中，单击“启用”，选择现有的 Log Analytics 工作区，然后单击“保存”。

    ![启用 HDInsight 群集监视](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Enable monitoring for HDInsight clusters")

    需要几分钟来保存设置。  完成后，可以在顶部看到“打开 OMS 仪表板”按钮。 

    ![打开操作管理套件仪表板](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "打开 OMS 仪表板")

5. 单击“打开 OMS 仪表板”。
6. 在系统提示时输入 Azure 凭据。

    ![Operations Management Suite 门户](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite portal")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>使用 Azure PowerShell 启用 Log Analytics

可以使用 Azure PowerShell 启用 Log Analytics。 cmdlet 为：

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

请参阅 [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0)。

若要禁用，cmdlet 为： 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

请参阅 [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0)。


## <a name="next-steps"></a>后续步骤
* [将 HDInsight 群集管理解决方案添加到 Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)

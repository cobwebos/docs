---
title: "使用 Azure Log Analytics 监视 Azure HDInsight 群集 | Microsoft Docs"
description: "了解如何使用 Azure Log Analytics 监视在 HDInsight 群集中运行的作业。"
services: hdinsight
documentationcenter: 
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
ms.date: 09/12/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: af5b05a366c1abbe7c91d186358dba2b4a957f92
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>使用 Azure Log Analytics 监视 HDInsight 群集（预览版）

本文介绍如何使用 Azure Log Analytics 监视 HDInsight Hadoop 群集操作。

Log Analytics 是 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中的一个服务，用于监视云和本地环境，使其保持较高的可用性和性能。 它可以收集云和本地环境中的资源生成的数据以及其他监视工具的数据，针对多个源提供分析。 

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 在开始学习本教程之前，必须有一个 Azure 订阅。 请参阅[立即创建免费 Azure 帐户](https://azure.microsoft.com/free)。

* **一个 Azure HDInsight 群集**。 目前，可以将 Azure OMS 与以下 HDInsight 群集类型配合使用：
    * Hadoop
    * Spark
    * HBase
    * Storm
    * Kafka
    * Interactive Hive

    有关如何创建 HDInsight 群集的说明，请参阅 [Azure HDInsight 入门](hdinsight-hadoop-linux-tutorial-get-started.md)。


* Log Analytics 工作区。 可将工作区视为独特的 Log Analytics 环境，其中包含自身的数据存储库、数据源和解决方案。 必须具有一个已创建的此类工作区，该工作区可以与 Azure HDInsight 群集关联。 有关说明，请参阅[创建 Log Analytics 工作区](../log-analytics/log-analytics-get-started.md#2-create-a-workspace)。

## <a name="configure-hdinsight-cluster-to-use-azure-log-analytics"></a>配置 HDInsight 群集以使用 Azure Log Analytics

在本部分中，将配置现有 HDInsight Hadoop 群集，以使用 Azure Log Analytics 工作区来监视作业、调试日志等等。

1. 在 Azure 门户中的左窗格中，单击“HDInsight 群集”，然后单击想要使用 Azure Log Analytics 配置的群集的名称。

2. 在群集边栏选项卡的左窗格中，单击“监视”。

3. 在右窗格中，单击“启用”，然后选择现有的 Log Analytics 工作区。 单击“保存” 。

    ![启用 HDInsight 群集监视](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Enable monitoring for HDInsight clusters")

4. 群集配置为使用 Log Analytics 进行监视后，选项卡顶部显示“打开 OMS 仪表板”选项。单击按钮。

    ![打开 OMS 仪表板](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Open OMS dashboard")

5. 在系统提示时输入 Azure 凭据。 你将定向到 Microsoft OMS 仪表板。

    ![Operations Management Suite 门户](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite portal")

## <a name="next-steps"></a>后续步骤
* [将 HDInsight 群集管理解决方案添加到 Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)


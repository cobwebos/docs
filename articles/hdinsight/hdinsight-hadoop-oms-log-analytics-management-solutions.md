---
title: 将 HDInsight 群集管理解决方案添加到 Azure Log Analytics | Microsoft Docs
description: 了解如何使用 Azure Log Analytics 创建 HDInsight 群集的自定义视图。
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
ms.openlocfilehash: ab959d2cdaf840a1a88a71a0b8f29d84a8979330
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>将 HDInsight 群集管理解决方案添加到 Log Analytics

HDInsight 提供特定于群集的管理解决方案，可为 Azure Log Analytics 添加这些解决方案。 [管理解决方案](../log-analytics/log-analytics-add-solutions.md)可将功能添加到 Log Analytics，提供更多数据和分析工具。 这些解决方案从 HDInsight 群集中收集重要的性能指标，并提供搜索指标的工具。 这些解决方案还为 HDInsight 支持的大多数群集类型提供可视化和仪表板。 使用解决方案收集指标后，即可利用这些指标创建自定义监视规则和警报。 

本文介绍如何将特定于群集的管理解决方案添加到 Log Analytics 工作区。

## <a name="prerequisites"></a>先决条件

* 必须先配置 HDInsight 群集，才能使用 Azure Log Analytics。 如需说明，请参阅[通过 HDInsight 群集使用 Azure Log Analytics](hdinsight-hadoop-oms-log-analytics-tutorial.md)。

## <a name="add-cluster-specific-management-solutions"></a>添加特定于群集的管理解决方案

在此部分中，会将 HBase 群集管理解决方案添加到现有的 Log Analytics 工作区。

1. 在 Azure 门户中打开一个 HDInsight 群集，依次单击“监视”、“打开 OMS 仪表板”。

    ![打开操作管理套件仪表板](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "打开 OMS 仪表板")

1. 在仪表板的左侧窗格中，单击“解决方案库”或“视图设计器”图标。

    ![在 Log Analytics 中添加管理解决方案](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "在 Operations Management Suite 中添加管理解决方案")

2. 在解决方案库中，单击以下磁贴之一：

    - HDInsight Hadoop 监视
    - HDInsight HBase 监视(预览版)
    - HDInsight Kafka 监视
    - HDInsight Storm 监视
    - HDInsight Spark 监视

3. 在下一屏幕中，单击“添加”。  以下屏幕截图显示“HBase 监视”的“添加”按钮。

     ![添加 HBase 管理解决方案](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Add HBase management solution")

4. 可在仪表板上看到“HBase 管理解决方案”的磁贴。 如果与 Operations Management Suite 关联（作为本文的先决条件的一部分）的群集是 HBase 群集，该磁贴将显示群集的名称和群集中的节点数。

    ![已添加 HBase 管理解决方案](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "HBase management solution added")

## <a name="next-steps"></a>后续步骤

* [查询 Azure Log Analytics，监视 Azure HDInsight 群集](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>另请参阅

* [使用 Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [在 Log Analytics 中创建警报规则](../log-analytics/log-analytics-alerts-creating.md)

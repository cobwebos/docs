---
title: 在 Azure HDInsight 上使用 Grafana
description: 了解如何在 Azure HDInsight 中使用 Apache Hadoop 群集访问格拉法纳仪表板
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082857"
---
# <a name="access-grafana-in-azure-hdinsight"></a>在 Azure HDInsight 中访问 Grafana

[Grafana](https://grafana.com/) 是一个受欢迎的开源图形和仪表板生成器。 Grafana 功能丰富；它不仅可以让用户创建可自定义和可共享的仪表板，还可提供模板化/脚本化仪表板、LDAP 集成、多个数据源等。

目前，在 Azure HDInsight 中，Spark、HBase、Kafka 和交互式查询群集类型支持 Grafana。 对于启用了企业安全包的群集，不支持它。

如果没有 Azure 订阅，请先创建[一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。"

## <a name="create-an-apache-hadoop-cluster"></a>创建 Apache Hadoop 群集

请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。 对于**群集类型**，选择**Spark、****卡夫卡****、HBase**或**交互式查询**。

## <a name="access-the-grafana-dashboard"></a>访问 Grafana 仪表板

1. 从 Web 浏览器导航到`https://CLUSTERNAME.azurehdinsight.net/grafana/`CLUSTERNAME 是群集名称的位置。

1. 输入 Hadoop 群集用户凭据。

1. 此时将显示 Grafana 仪表板，如下例所示：

    ![HDInsight 格拉法纳网络仪表板](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight格拉法纳仪表板")

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请使用以下步骤删除创建的群集：

1. 登录到 Azure[门户](https://portal.azure.com/)。

1. 在顶部的“搜索”框中，键入 **HDInsight**。****

1. 选择“服务”下的“HDInsight 群集”********。

1. 在显示的 HDInsight 群集列表中，选择您创建的群集旁边的 **...**

1. 选择 **"删除**"。 选择 **“是”**。

## <a name="next-steps"></a>后续步骤

有关如何使用 HDInsight 分析数据的详细信息，请参阅以下文章：

* [使用阿帕奇蜂巢与HDInsight。](../hadoop/hdinsight-use-hive.md)

* [使用地图减少与HDInsight。](../hadoop/hdinsight-use-mapreduce.md)

* [开始使用视觉工作室Hadoop工具的HDInsight。](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

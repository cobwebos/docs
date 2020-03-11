---
title: 在 Azure HDInsight 上使用 Grafana
description: 了解如何使用 Azure HDInsight 中的 Apache Hadoop 群集访问 Grafana 仪表板
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082857"
---
# <a name="access-grafana-in-azure-hdinsight"></a>在 Azure HDInsight 中访问 Grafana

[Grafana](https://grafana.com/) 是一个受欢迎的开源图形和仪表板生成器。 Grafana 功能丰富；它不仅可以让用户创建可自定义和可共享的仪表板，还可提供模板化/脚本化仪表板、LDAP 集成、多个数据源等。

目前，在 Azure HDInsight 中，Spark、HBase、Kafka 和交互式查询群集类型都支持 Grafana。 启用了企业安全包的群集不支持此功能。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-apache-hadoop-cluster"></a>创建 Apache Hadoop 群集

请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。 对于**群集类型**，请选择**Spark**、 **Kafka**、 **HBase**或**交互式查询**。

## <a name="access-the-grafana-dashboard"></a>访问 Grafana 仪表板

1. 在 web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net/grafana/`，其中 CLUSTERNAME 是群集的名称。

1. 输入 Hadoop 群集用户凭据。

1. 此时将显示 Grafana 仪表板，如下例所示：

    ![HDInsight Grafana web 仪表板](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana 仪表板")

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请使用以下步骤删除创建的群集：

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在顶部的“搜索”框中，键入 **HDInsight**。

1. 选择“服务”下的“HDInsight 群集”。

1. 在出现的 HDInsight 群集列表中，选择你创建的群集旁边的 " **..."。**

1. 选择“删除”。 请选择“是”。

## <a name="next-steps"></a>后续步骤

有关如何使用 HDInsight 分析数据的详细信息，请参阅以下文章：

* [将 Apache Hive 与 HDInsight 配合使用](../hadoop/hdinsight-use-hive.md)。

* [将 MapReduce 与 HDInsight 配合使用](../hadoop/hdinsight-use-mapreduce.md)。

* [开始使用适用于 HDInsight 的 Visual Studio Hadoop 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

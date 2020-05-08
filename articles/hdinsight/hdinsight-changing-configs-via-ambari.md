---
title: 在 Azure HDInsight 中通过 Apache Ambari 优化群集
description: 使用 Apache Ambari web UI 配置和优化 Azure HDInsight 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: a819c3a57efdc0ae87cf969fd7471818c51895f6
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793153"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>在 Azure HDInsight 中通过 Apache Ambari 优化群集

HDInsight 为大规模数据处理应用程序提供 Apache Hadoop 群集。 对这些复杂的多节点群集进行管理、监视和优化可能会很困难。 Apache Ambari 是一种用于管理和监视 HDInsight Linux 群集的 web 界面。

有关使用 Ambari Web UI 的简介，请参阅[使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)

使用群集凭据通过 `https://CLUSTERNAME.azurehdidnsight.net` 登录到 Ambari。 初始屏幕显示了概述仪表板。

![已显示 Apache Ambari 用户仪表板](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Ambari web UI 用于管理主机、服务、警报、配置和视图。 Ambari 不能用于创建 HDInsight 群集或升级服务。 此外，也无法管理堆栈和版本、解除或重用主机或将服务添加到群集。

## <a name="manage-your-clusters-configuration"></a>管理群集的配置

配置设置可帮助优化特定服务。 若要修改服务的配置设置，请从 "**服务**" 边栏（左侧）中选择该服务。 然后导航到服务详细信息页中的 "配置 **" 选项卡**。

![Apache Ambari Services 边栏](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>修改 NameNode Java 堆大小

NameNode Java 堆大小取决于许多因素，例如群集上的负载。 还可以是文件数和块数。 默认大小 1 GB 能够很好地满足大多数群集的需要，不过，某些工作负荷可能需要更多或更少的内存。

修改 NameNode Java 堆大小：

1. 从“服务”边栏中选择“HDFS”，然后导航到“配置”选项卡。********

    ![Apache Ambari HDFS 配置](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. 找到“NameNode Java 堆大小”设置。**** 也可以使用“筛选器”文本框键入和查找特定的设置。**** 选择设置名称旁边的**笔**图标。

    ![Apache Ambari NameNode Java 堆大小](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. 在文本框中键入新值，然后按 **Enter** 保存更改。

    ![Ambari 编辑 NameNode Java 堆 name> 时](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. NameNode Java 堆大小已从 2 GB 更改为 1 GB。

    ![已编辑 NameNode Java 堆大小 2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. 单击配置屏幕顶部的绿色“保存”按钮保存所做的更改。****

    !["Apache Ambari 保存配置"](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>后续步骤

* [使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [优化 Apache HBase](./optimize-hbase-ambari.md)
* [优化 Apache Hive](./optimize-hive-ambari.md)
* [优化 Apache Pig](./optimize-pig-ambari.md)

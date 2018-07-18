---
title: 安装已发布应用程序 - StreamSets Data Collector - Azure HDInsight | Microsoft Docs
description: 安装并使用 StreamSets Data Collector 第三方 Hadoop 应用程序。
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: e433de82576f8b943988881ed0b6673c0dccd77e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401017"
---
# <a name="install-published-application---streamsets-data-collector"></a>安装已发布应用程序 - StreamSets Data Collector

本文介绍如何在 Azure HDInsight 上安装和运行 [StreamSets Data Collector for HDInsight](https://streamsets.com/) 发布的 Hadoop 应用程序。 有关 HDInsight 应用程序平台的概述以及可用独立软件供应商 (ISV) 发布的应用程序的列表，请参阅[安装第三方 Hadoop 应用程序](hdinsight-apps-install-applications.md)。 有关如何安装自己的应用程序的说明，请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。

## <a name="about-streamsets-data-collector"></a>关于 StreamSets Data Collector

StreamSets Data Collector 部署在 Azure HDInsight 应用程序之上。 StreamSets Data Collector 提供一个全功能的集成开发环境 (IDE)，可在其中设计、测试、部署和管理全面互通引入管道。 这些管道可以汇合流和批处理数据，并提供各种流中转换，所有这一切都无需编写自定义代码。

StreamSets Data Collector 可以使用许多大数据组件（如 HDFS、Kafka、Solr、Hive、HBASE 和 Kudu）生成数据流。 StreamSets Data Collector 在边缘服务器上或 Hadoop 群集中运行后，你便可以实时监视数据异常和数据流操作。 此监视功能包括基于阈值的警报、异常检测和错误记录的自动修正。

StreamSets Data Collector 旨在在管道中从逻辑上隔离每个阶段，因此你可以通过增加新的处理器和连接器，而无需编码只需最少的停机时间即可满足新的业务要求。

### <a name="streamsets-resource-links"></a>StreamSets 资源链接

* [文档](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [博客](https://streamsets.com/blog/)
* [教程](https://github.com/streamsets/tutorials)
* [开发人员支持论坛](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Slack 公共 StreamSets 通道](https://streamsetters.slack.com/)
* [源代码](https://github.com/streamsets)

## <a name="prerequisites"></a>先决条件

若要在新的 HDInsight 群集或现有群集上安装此应用，必须采用以下配置：

* 群集层：标准或高级
* 群集版本：3.5 及更高版本

## <a name="install-the-streamsets-data-collector-published-application"></a>安装 StreamSets Data Collector 发布的应用程序

有关安装此应用程序和其他可用 ISV 应用程序的分步说明，请阅读[安装第三方 Hadoop 应用程序](hdinsight-apps-install-applications.md)。

## <a name="launch-streamsets-data-collector"></a>启动 StreamSets Data Collector

1. 安装完成后，可以通过转到 Azure 门户中的“设置”窗格，然后选择“常规”类别下的“应用程序”从群集启动 StreamSets。 “已安装的应用”窗格将列出已安装的应用程序。

    ![已安装的 StreamSets 应用](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. 选择 StreamSets Data Collector 时，会看到网页链接和 SSH 终结点路径。 选择网页链接。

3. 在“登录”对话框中，使用以下凭据登录：`admin` 和 `admin`。

4. 在“入门”页中，单击“新建管道”。

    ![新建管道](./media/hdinsight-apps-install-streamsets/get-started.png)

5. 在“新建管道”窗口中，输入管道的名称 ("Hello World")，（可选）输入说明，然后选择“保存”。

6. 将显示 Data Collector 控制台。 “属性”面板显示管道属性。
 
    ![Data Collector 控制台](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. 现在可以按照 [StreamSets 教程](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html)进行操作了。 该教程提供了有关创建第一个管道的分步说明。

## <a name="next-steps"></a>后续步骤

* [StreamSets Data Collector 文档](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq)。
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure Marketplace。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试和托管 HDInsight 应用程序。

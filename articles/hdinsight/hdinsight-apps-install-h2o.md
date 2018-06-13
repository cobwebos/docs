---
title: 安装已发布的应用程序 - H2O Sparkling Water - Azure HDInsight | Microsoft Docs
description: 安装并使用 H2O Sparkling Water 第三方 Hadoop 应用程序。
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
ms.openlocfilehash: 9a03588b3327c3ab231f5c2cae17488f4d63bde7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31402101"
---
# <a name="install-published-application---h2o-sparkling-water"></a>安装已发布的应用程序 - H2O Sparkling Water

本文介绍如何在 Azure HDInsight 上安装和运行 [H2O Sparkling Water](http://www.h2o.ai/) 发布的 Hadoop 应用程序。 有关 HDInsight 应用程序平台的概述以及可用独立软件供应商 (ISV) 发布的应用程序的列表，请参阅[安装第三方 Hadoop 应用程序](hdinsight-apps-install-applications.md)。 有关如何安装自己的应用程序的说明，请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。

## <a name="about-h2o-sparkling-water"></a>关于 H2O Sparkling Water

H2O Sparkling Water 是具有线性缩放能力的开源、完全分布式内存中机器学习平台。 使用 H2O Sparkling Water 可将 H2O 的快速、可缩放机器学习算法与 Spark 的功能相结合。 借助 Sparkling Water，用户可以使用 H2O Flow UI 推动 Scala、R 和 Python 的计算。

H2O Sparkling Water 提供：

* **易用的 WebUI 和熟悉的界面** – 使用 H2O 的基于 Web 的直观 Flow GUI 或编程环境（例如 R、Python、Java、Scala、JSON）和 H2O API 进行设置和快速入门。
* **对所有常见数据库和文件类型的数据不可知支持** – 从 Microsoft Excel、R Studio、Tableau 等工具的内部轻松探索和建模大数据。 从 HDFS、S3、SQL 和 NoSQL 数据源连接到数据。
* **大规模的大数据修整和分析** – H2O Big Joins 的执行速度比 R data.table 操作快 7 倍，并可线性扩展到 100 亿 x 100 亿行联接。
* **实时数据评分** – 使用无格式普通 Java 对象 (POJO)、模型优化的 Java 对象 (MOJO) 或 H2O REST API，将模型快速部署到生产环境。

### <a name="resource-links"></a>资源链接

* [H2O.ai 工程路线图](https://jira.h2o.ai/)
* [H2O.ai 主页](http://www.h2o.ai/)
* [H2O.ai 文档](http://docs.h2o.ai/)
* [H2O.ai 支持](https://support.h2o.ai/)
* [H2O.ai 开源代码库](https://github.com/h2oai/)

## <a name="prerequisites"></a>先决条件

若要在新的 HDInsight 群集或现有群集上安装此应用，必须采用以下配置：

* 群集层：标准或高级
* 群集类型：Spark
* 群集版本：3.5 或 3.6

## <a name="install-the-h2o-sparkling-water-published-application"></a>安装 H2O Sparkling Water 发布的应用程序

有关安装此应用程序和其他可用 ISV 应用程序的分步说明，请阅读[安装第三方 Hadoop 应用程序](hdinsight-apps-install-applications.md)。

## <a name="launch-h2o-sparkling-water"></a>启动 H2O Sparkling Water

1. 安装完成后，可以在 Azure 门户中打开 Jupyter Notebook (`https://<ClusterName>.azurehdinsight.net/jupyter`)，通过群集开始使用 H2O Sparkling Water (h2o sparklingwater)。 转到 Jupyter 的另一种方法是在门户上的群集窗格中选择“群集仪表板”，然后选择“Jupyter Notebook”。 系统会提示输入凭据。 输入创建群集时指定的群集 Hadoop 凭据。

2. Jupyter 中显示了三个文件夹：H2O-PySparkling-Examples、PySpark Examples 和 Scala Examples。 选择“H2O-PySparkling-Examples”文件夹。

    ![Jupyter Notebook 主页](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. 创建新 Notebook 时的第一个步骤是配置 Spark 环境。 **Sentiment_analysis_with_Sparkling_Water** 示例中包含了此信息。 配置 Spark 环境时，请务必使用正确的 jar，并指定第一个单元输出中提供的 IP 地址。

    ![Jupyter Notebook 主页](./media/hdinsight-apps-install-h2o/spark-config.png)

4. 启动 H2O 群集。

    ![启动群集](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. H2O 群集启动并运行后，转到 **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`** 打开 H2O Flow。

    > [!NOTE]
    > 如果无法打开 H2O Flow，请尝试清除浏览器缓存。 如果仍然无法访问它，则可能表示群集中没有足够的资源。 请尝试在群集窗格中的“缩放群集”选项下增加工作节点的数目。

    ![H2O Flow 仪表板](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. 在右侧菜单中选择“Million_Songs.flow”示例。 出现提示和警告时，请单击“加载 Notebook”。 几分钟后，将使用真实数据运行此演示。 目的是使用二元分类，基于数据预测该歌曲是在 2004 年之前还是之后发行的。

    ![选择 Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. 查找包含 **milsongs-cls-train.csv.gz** 的路径，并将整个路径替换为 **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**。

8. 查找包含 **milsongs-cls-test.csv.gz** 的路径，并将该路径替换为 **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**。

9. 若要执行 Notebook 单元中的所有语句，请在工具栏上选择“全部运行”按钮。

    ![全部运行](./media/hdinsight-apps-install-h2o/run-all.png)

10. 几分钟后，应会看到如下所示的输出。

    ![输出](./media/hdinsight-apps-install-h2o/output.png)

就这么简单！ 只花费了几分钟时间，你就掌握了 Spark 中的人工智能。 现在，可以继续探索 H2O Flow 中演示不同类型的机器学习算法的其他示例。

## <a name="next-steps"></a>后续步骤

* [H2O 文档](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure Marketplace。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试和托管 HDInsight 应用程序。

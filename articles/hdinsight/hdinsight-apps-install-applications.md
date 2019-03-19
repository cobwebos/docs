---
title: 在 Azure HDInsight 上安装第三方应用程序
description: 了解如何在 Azure HDInsight 上安装第三方 Hadoop 应用程序。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: hrasheed
ms.openlocfilehash: a5033b9eab637a62c6bea4f95f4e604313125994
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542041"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上安装第三方 Apache Hadoop 应用程序

了解如何在 Azure HDInsight 上安装第三方 [Apache Hadoop](https://hadoop.apache.org/) 应用程序。 有关如何安装自己的应用程序的说明，请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。

HDInsight 应用程序是用户可以在 HDInsight 群集上安装的应用程序。 这些应用程序可能是 Microsoft、独立软件供应商 (ISV) 或自己开发的。  

以下列表显示已发布的应用程序：

|应用程序 |群集类型 | 描述 |
|---|---|---|
|AtScale 智能平台 |Hadoop |AtScale 将允许您查询几十亿行数据以交互方式使用 BI 工具已经知道，拥有，并喜爱 – 从 Microsoft Excel、 PowerBI、 Tableau Software qlikview 到横向扩展 OLAP 服务器，在 HDInsight 群集。 |
|CDAP 4.2，HDInsight 的 4.3 |HBase |CDAP 是加速了值的 Hadoop 以及让 IT 人员提供自助服务数据的大数据的第一个统一的集成平台。 开放源代码和可扩展的 CDAP 消除障碍创新。 要求：4 个区域节点，最小值 D3 v2。 |
|Datameer |Hadoop |Datameer 自助服务可扩展的平台来准备，探索的东西，和用于管理你的数据分析加速将复杂的多源数据转变为有价值的面向业务的信息，在整个企业范围内提供更快、 更智能见解。 |
|在 HDInsight 上的 Dataiku DSS |Hadoop、Spark |在企业数据科学平台，可让数据科学家和数据分析师中 Dataiku DSS 协作来设计和运行新的数据产品和服务更有效地将原始数据转变为有影响力的预测。 |
|WANdisco Fusion HDI App-2.12.3、 2.12.1、 2.11.2 |Hadoop、 Spark、 HBase、 Storm、 Kafka |使数据保持一致的分布式环境中是一个大规模的数据操作挑战。 WANdisco Fusion，一个企业级的软件平台，通过跨任何环境中实现非结构化的数据的一致性，解决了此问题。 |
|HDInsight 的 H2O SparklingWater |Spark |H2O Sparkling Water 支持以下分布式的算法：GLM、 朴素贝叶斯、 分布式随机林、 渐变提升机、 深度学习，K 平均值、 PCA、 通用化低排名模型、 异常情况检测、 Autoencoders 深度神经网络。 |
|Striim 的实时数据集成到 HDInsight |Hadoop、 HBase、 Storm、 Spark、 Kafka |Striim （读作"流"） 是端到端流式处理数据集成 + 智能平台，启用持续引入、 处理和分析不同的数据流。 |
|有关 HDInsight Jumbune |Hadoop、Spark |在高级别，Jumbune 协助企业，1。 加速 Tez，MapReduce 和 Spark 引擎基于 Hive、 Java、 Scala 工作负荷的性能。 2. 主动监视，3 的 Hadoop 群集。 建立分布式的文件系统上的数据质量管理。 |
|Kyligence 企业 |Hadoop、 HBase、 Spark |Kyligence Enterprise 由 Apache Kylin 提供支持，使大数据 BI。 作为企业 OLAP 引擎的 hadoop，Kyligence 企业使业务分析师使用行业标准数据仓库和 BI 方法构建的 Hadoop 上的 BI。 |
|Spark Job Server for KNIME Spark Executor |Spark |Spark Job Server for KNIME Spark Executor 用于将 KNIME Analytics Platform 连接到 HDInsight 群集。 |
|在 Azure HDInsight，星爆图案 Presto (v0.213 e) 上的 Presto 的星爆图案 |Hadoop |Presto 是一个快速且可缩放分布式的 SQL 查询引擎。 Presto 针对存储和计算的分离进行了架构设计，非常适用于查询 Azure Data Lake Storage、Azure Blob 存储、SQL 和 NoSQL 数据库以及其他数据源中的数据。 |
|StreamSets Data Collector for HDInsight 云 |Hadoop、 HBase、 Spark、 Kafka |StreamSets Data Collector 是一个轻量、 功能强大的引擎，流式传输实时数据。 使用数据收集器将数据流中的路由和处理数据。 它附带 30 天试用版许可证。 |
|[Trifacta Wrangler 企业](https://www.trifacta.com/) |Hadoop、 Spark、 HBase |有关 HDInsight Trifacta Wrangler Enterprise 支持企业级数据整理适用于任何规模的数据。 在 Azure 上运行 Trifacta 的成本是 Trifacta 订阅成本与虚拟机的 Azure 基础结构成本的组合。 |
|Unifi 数据平台 3.1 |Hadoop、 HBase、 Storm、 Spark |Unifi 数据平台是自助服务数据工具，旨在使该驱动器增量式收入的业务用户就可以处理大数据挑战，减少操作复杂性或成本的无缝集成的套件。 |
|Unraveldata APM |Spark |解决适用于 HDInsight Spark 群集的数据应用。 |
|吃水数据目录 |Spark |吃水目录，组织，并决定使用 AI 到自动标记数据与业务术语表示的数据。 Waterline 的商业文献目录是一个用于自助分析、合规性和管理，以及 IT 管理计划的关键成功组件。 |

本文提供的说明将使用 Azure 门户。 也可以从门户导出 Azure 资源管理器模板或从供应商处获取 Resource Manage 模板的副本，并使用 Azure PowerShell 和 Azure 经典 CLI 部署模板。  请参阅[使用资源管理器模板在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

## <a name="prerequisites"></a>先决条件
如果想要在现有的 HDInsight 群集上安装 HDInsight 应用程序，必须有一个 HDInsight 群集。 若要创建群集，请参阅 [创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。 也可以在创建 HDInsight 群集时安装 HDInsight 应用程序。

## <a name="install-applications-to-existing-clusters"></a>将应用程序安装到现有群集
下面的过程演示如何将 HDInsight 应用程序安装到现有的 HDInsight 群集。

**安装 HDInsight 应用程序**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从左侧菜单中，导航到**所有服务** > **Analytics** > **HDInsight 群集**。
3. 从列表中选择 HDInsight 群集。  如果没有群集，必须先创建一个。  请参阅 [创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。
4. 下**设置**类别中，选择**应用程序**。 可以查看安装在主窗口中的应用程序的列表。 
   
    ![HDInsight 应用程序门户菜单](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 选择 **+ 添加**菜单中。 可以看到可用的应用程序的列表。  如果 **+ 添加**并灰显，表示是此版本的 HDInsight 群集的应用程序。
   
    ![HDInsight 应用程序可用应用程序](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. 选择一个可用的应用程序，然后按照说明接受法律条款。

可以看到门户通知 （选择在门户顶部的铃铛图标） 的安装状态。 安装应用程序后，应用程序会出现在“已安装的应用”列表中。

## <a name="install-applications-during-cluster-creation"></a>在群集创建期间安装应用程序
可以选择在创建群集时安装 HDInsight 应用程序。 在此过程中，HDInsight 应用程序会在群集创建并处于运行状态后安装。 若要使用 Azure 门户创建群集期间安装应用程序，请使用**自定义**而不是默认选项**快速创建**选项。

## <a name="list-installed-hdinsight-apps-and-properties"></a>列出已安装的 HDInsight 应用和属性
门户会显示群集的已安装 HDInsight 应用程序列表，以及每个已安装应用程序的属性。

**列出 HDInsight 应用程序并显示属性**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从左侧菜单中，导航到**所有服务** > **Analytics** > **HDInsight 群集**。
3. 从列表中选择 HDInsight 群集。
4. 下**设置**类别中，选择**应用程序**。 可以查看安装在主窗口中的应用程序的列表。 
   
    ![HDInsight 应用程序已安装的应用](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. 选择其中一个已安装的应用程序以显示属性。 属性列表：

    |属性 | 描述 |
    |---|---|
    |应用程序名称 |应用程序名称。 |
    |状态 |应用程序状态。 |
    |网页 |已部署到边缘节点的 Web 应用程序的 URL。 此凭据与针对群集配置的 HTTP 用户凭据相同。 |
    |SSH 终结点 |可以使用 SSH 连接到边缘节点。 SSH 凭据与针对群集配置的 SSH 用户凭据相同。 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。 |
    |描述 | 应用程序说明。 |

6. 若要删除应用程序，请右键单击应用程序，并单击上下文菜单中的“删除”。

## <a name="connect-to-the-edge-node"></a>连接到边缘节点
可以使用 HTTP 和 SSH 连接到边缘节点。 可在 [门户](#list-installed-hdinsight-apps-and-properties)中找到终结点信息。 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

HTTP 终结点凭据是针对 HDInsight 群集配置的 HTTP 用户凭据；SSH 终结点凭据是针对 HDInsight 群集配置的 SSH 凭据。

## <a name="troubleshoot"></a>故障排除
请参阅 [故障排除安装问题](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)。

## <a name="next-steps"></a>后续步骤
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure 市场。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [使用资源管理器模板在 HDInsight 中创建基于 Linux 的 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)：了解如何调用资源管理器模板创建 HDInsight 群集。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试 HDInsight 应用程序以及托管 HDInsight 应用程序。


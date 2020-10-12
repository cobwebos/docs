---
title: 在 Azure HDInsight 上安装第三方应用程序
description: 了解如何在 Azure HDInsight 上安装第三方 Apache Hadoop 应用程序。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: e0d1f9ad99e1b64560321312a22f61f5a2ef3dea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89016030"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上安装第三方 Apache Hadoop 应用程序

了解如何在 Azure HDInsight 上安装第三方 [Apache Hadoop](https://hadoop.apache.org/) 应用程序。 有关如何安装自己的应用程序的说明，请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。

HDInsight 应用程序是用户可以在 HDInsight 群集上安装的应用程序。 这些应用程序可能是 Microsoft、独立软件供应商 (ISV) 或自己开发的。  

以下列表显示已发布的应用程序：

|应用程序 |群集类型 | 说明 |
|---|---|---|
|[AtScale 智能平台](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale 可将 HDInsight 群集转换为 OLAP 横向扩展服务器，使你能够使用熟悉的、自有的和喜爱的商业智能 (BI) 工具以交互的方式查看数十亿行数据；这些工具包括 Microsoft Excel、Power BI、Tableau Software 和 QlikView 等等。 |
|[适用于 HDInsight 的 CDAP](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP 是第一个用于大数据的统一集成平台，它加速 Hadoop 的价值实现，让 IT 能够提供自助服务数据。 CDAP 采用开源设计且可扩展，消除了创新所面临的障碍。 要求：4 个区域节点，至少需安装 D3 v2。 |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Datameer 是一个可缩放的自助服务平台，用于准备、浏览和管理要分析的数据，将复杂的多源数据更快地转变成可供业务使用的宝贵信息，在企业级别提供更快、更智能的见解。 |
|[HDInsight 上的 Dataiku DSS](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop、Spark |Dataiku DSS 位于企业数据科学平台上，可让数据科学家和数据分析师协同合作，更有效地设计和运行新的数据产品和服务，将原始数据转变成有影响力的预测。 |
|[WANdisco Fusion HDI 应用](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.wandisco-liveanalytics) |Hadoop、Spark、HBase、Storm、Kafka |在分布式环境中保持数据一致性是一个很大的数据操作难题。 企业级软件平台 WANdisco Fusion 可在任一环境中实现非结构化数据的一致性，从而解决了这一问题。 |
|[适用于 HDInsight 的 H2O SparklingWater](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water 支持以下分布式算法：GLM、朴素贝叶斯、分布式随机森林、梯度增强机、深度神经网络、深度学习、K-平均、PCA、广义低阶模型、异常情况检测和自编码器。 |
|[用于将实时数据集成到 HDInsight 的 Striim](https://azuremarketplace.microsoft.com/marketplace/apps/striim.striimbyol) |Hadoop、HBase、Storm、Spark、Kafka |Striim（读作“stream”）是一个端到端流式处理数据集成和智能平台，可用于实现不同数据流的持续引入、处理和分析。 |
|[Jumbune Enterprise - 加速大数据分析](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop、Spark |大致来说，Jumbune 通过以下方式为企业提供帮助：1. 加速基于 Tez、MapReduce 和 Spark 引擎的 Hive、Java 和 Scala 工作负载性能； 2. 主动监视 Hadoop 群集；3. 在分布式文件系统上建立数据质量管理。 |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop、HBase、Spark |Kyligence Enterprise 由 Apache Kylin 提供支持，可基于大数据实现商业智能 (BI)。 作为 Hadoop 上的企业 OLAP 引擎，Kyligence Enterprise 使业务分析师能够通过行业标准的数据仓库和 BI 方法在 Hadoop 上构建 BI。 |
|[适用于 Azure HDInsight 的 Starburst Presto](https://azuremarketplace.microsoft.com/marketplace/apps/starburstdatainc1579800938563.starburst-presto?tab=Overview) |Hadoop |Presto 是一种快速的可缩放分布式 SQL 查询引擎。 Presto 针对存储和计算的分离进行了架构设计，非常适用于查询 Azure Data Lake Storage、Azure Blob 存储、SQL 和 NoSQL 数据库以及其他数据源中的数据。 |
|[适用于 HDInsight 云的 StreamSets 数据收集器](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop、HBase、Spark、Kafka |StreamSets 数据收集器是一种轻量级的功能强大的引擎，可实时流式处理数据。 该数据收集器可用来路由和处理数据流中的数据。 该产品附带 30 天试用版许可证。 |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.trifacta-db?tab=Overview) |Hadoop、Spark、HBase |Trifacta Wrangler Enterprise for HDInsight 支持对任何规模的数据进行企业范围的数据整理。 在 Azure 上运行 Trifacta 所产生的成本是 Trifacta 订阅成本及虚拟机的 Azure 基础结构成本之和。 |
|[Unifi Data Platform](https://unifisoftware.com/platform/) |Hadoop、HBase、Storm、Spark |Unifi Data Platform 是一套无缝集成的自助服务数据工具，旨在使业务用户能够解决促进收入增长、降低成本或运营复杂性的数据难题。 |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |适用于 HDInsight Spark 群集的 Unravel Data 应用。 |
|[Waterline AI 驱动的数据目录](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline 使用 AI 来编录、整理和管理数据，从而使用业务术语自动标记数据。 Waterline 的商业文献目录是一个用于自助分析、符合性和管理，以及 IT 管理计划的关键成功组件。 |

本文提供的说明将使用 Azure 门户。 也可以从门户导出 Azure 资源管理器模板或从供应商处获取 Resource Manage 模板的副本，并使用 Azure PowerShell 和 Azure 经典 CLI 部署模板。  请参阅[使用资源管理器模板在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

## <a name="prerequisites"></a>先决条件
如果想要在现有的 HDInsight 群集上安装 HDInsight 应用程序，必须有一个 HDInsight 群集。 若要创建群集，请参阅 [创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md)。 也可以在创建 HDInsight 群集时安装 HDInsight 应用程序。

## <a name="install-applications-to-existing-clusters"></a>将应用程序安装到现有群集
下面的过程演示如何将 HDInsight 应用程序安装到现有的 HDInsight 群集。

**安装 HDInsight 应用程序**

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，导航到“所有服务” > “Analytics” > “HDInsight 群集”  。
3. 从列表中选择一个 HDInsight 群集。  如果没有群集，必须先创建一个。  请参阅 [创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md)。
4. 在“设置”目录下，选择“应用程序” 。 可在主窗口中看到已安装应用程序的列表。 
   
    ![HDInsight 应用程序门户菜单](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 在菜单中选择“+添加”。 可看到可用应用程序的列表。  如果“+添加”呈灰色，表示任何应用程序可用于此版本的 HDInsight 群集。
   
    ![HDInsight 应用程序可用应用程序](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. 选择某个可用的应用程序，然后按照说明来接受法律条款。

可通过门户通知查看安装状态（选择门户顶部的铃铛图标）。 安装应用程序后，应用程序会出现在“已安装的应用”列表中。

## <a name="install-applications-during-cluster-creation"></a>在群集创建期间安装应用程序

可以选择在创建群集时安装 HDInsight 应用程序。 在此过程中，HDInsight 应用程序会在群集创建并处于运行状态后安装。 若要通过 Azure 门户在群集创建期间安装应用程序，请从“配置与定价”选项卡中选择“+添加应用程序” 。

![Azure 门户群集配置应用程序](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>列出已安装的 HDInsight 应用和属性
门户会显示群集的已安装 HDInsight 应用程序列表，以及每个已安装应用程序的属性。

**列出 HDInsight 应用程序并显示属性**

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，导航到“所有服务” > “Analytics” > “HDInsight 群集”  。
3. 从列表中选择一个 HDInsight 群集。
4. 在“设置”目录下，选择“应用程序” 。 可在主窗口中看到已安装应用程序的列表。 
   
    ![HDInsight 应用程序已安装的应用](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. 选择一个已安装的应用程序来查看属性。 属性列表：

    |属性 | 说明 |
    |---|---|
    |应用程序名称 |应用程序名称。 |
    |状态 |应用程序状态。 |
    |网页 |已部署到边缘节点的 Web 应用程序的 URL。 此凭据与针对群集配置的 HTTP 用户凭据相同。 |
    |SSH 终结点 |可以使用 SSH 连接到边缘节点。 SSH 凭据与针对群集配置的 SSH 用户凭据相同。 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。 |
    |说明 | 应用程序说明。 |

6. 若要删除应用程序，请右键单击应用程序，并单击上下文菜单中的“删除”。

## <a name="connect-to-the-edge-node"></a>连接到边缘节点
可以使用 HTTP 和 SSH 连接到边缘节点。 可在 [门户](#list-installed-hdinsight-apps-and-properties)中找到终结点信息。 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

HTTP 终结点凭据是针对 HDInsight 群集配置的 HTTP 用户凭据；SSH 终结点凭据是针对 HDInsight 群集配置的 SSH 凭据。

## <a name="troubleshoot"></a>疑难解答
请参阅 [故障排除安装问题](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)。

## <a name="next-steps"></a>后续步骤
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure 市场。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [使用资源管理器模板在 HDInsight 中创建基于 Linux 的 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)：了解如何调用资源管理器模板创建 HDInsight 群集。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试 HDInsight 应用程序以及托管 HDInsight 应用程序。


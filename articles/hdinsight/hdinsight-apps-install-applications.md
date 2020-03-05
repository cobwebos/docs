---
title: 在 Azure HDInsight 上安装第三方应用程序
description: 了解如何在 Azure HDInsight 上安装第三方 Apache Hadoop 应用程序。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: a7d83d2bc418e62bc2858e2a7fb35d2113640c83
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271974"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上安装第三方 Apache Hadoop 应用程序

了解如何在 Azure HDInsight 上安装第三方 [Apache Hadoop](https://hadoop.apache.org/) 应用程序。 有关如何安装自己的应用程序的说明，请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。

HDInsight 应用程序是用户可以在 HDInsight 群集上安装的应用程序。 这些应用程序可能是 Microsoft、独立软件供应商 (ISV) 或自己开发的。  

以下列表显示已发布的应用程序：

|应用程序 |群集类型 | 说明 |
|---|---|---|
|[AtScale 智能平台](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale 将你的 HDInsight 群集转换为横向扩展 OLAP 服务器，使你能够使用已了解的 BI 工具（拥有和喜爱）以交互方式查询数十亿行的数据，从 Microsoft Excel、Power BI、Tableau 软件到 QlikView。 |
|[CDAP for HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP 是适用于大数据的第一个统一集成平台，可加速 Hadoop 价值的时间，并使其能够提供自助服务数据。 开源和可扩展，CDAP 消除了创新的障碍。 要求：4个区域节点，最小 D3 v2。 |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Datameer 的可缩放平台，可用于准备、浏览和管理数据进行分析，加快将复杂的 multisource 数据转变成有价值的业务就绪信息，在企业范围内提供更快速、更智能的见解。 |
|[HDInsight 上的 Dataiku DSS](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop、Spark |企业数据科学平台中的 Dataiku DSS，可让数据科学家和数据分析人员协作地设计和运行新的数据产品和服务，将原始数据转换为有影响力预测。 |
|[WANdisco 合成 HDI 应用](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop、Spark、HBase、风暴、Kafka |在分布式环境中保持数据一致性是一项巨大的数据操作挑战。 企业级软件平台的 WANdisco 融合通过在任何环境中启用非结构化数据一致性来解决此问题。 |
|[适用于 HDInsight 的 H2O SparklingWater](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling 水源支持以下分布式算法： GLM、简单的 Bayes、分布式随机林、渐变提升机、深层神经网络、深度学习、K 平均值、PCA、通用低排名模型、异常检测、Autoencoders。 |
|[Striim 实时数据集成到 HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |Hadoop、HBase、风暴、Spark、Kafka |Striim （发音为 "流"）是端到端的流数据集成 + 智能平台，可实现不同数据流的持续引入、处理和分析。 |
|[Jumbune 企业-加速 BigData 分析](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop、Spark |从高层次来看，Jumbune 以1为依据。 加速 Tez、MapReduce & 基于 Spark 引擎的 Hive、Java、Scala 工作负荷性能。 2. 主动 Hadoop 群集监视，3。 在分布式文件系统上建立数据质量管理。 |
|[Kyligence 企业](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop、HBase、Spark |由 Apache Kylin 提供支持，Kyligence Enterprise 可为大数据启用 BI。 作为 Hadoop 上的企业 OLAP 引擎，Kyligence Enterprise 使业务分析人员能够使用行业标准数据仓库和 BI 方法为 Hadoop 构建 BI。 |
|[适用于 Azure HDInsight 的爆炸形 Presto](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto 是一种快速且可扩展的分布式 SQL 查询引擎。 Presto 针对存储和计算的分离进行了架构设计，非常适用于查询 Azure Data Lake Storage、Azure Blob 存储、SQL 和 NoSQL 数据库以及其他数据源中的数据。 |
|[适用于 HDInsight 云的 Streamsets data collector 数据收集器](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop、HBase、Spark、Kafka |Streamsets data collector 数据收集器是一种可实时流式处理数据的轻型、功能强大的引擎。 使用数据收集器来路由和处理数据流中的数据。 它附带30天试用版许可证。 |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |Hadoop、Spark、HBase |Trifacta Wrangler Enterprise for HDInsight 支持企业范围的数据整理，适用于任何规模的数据。 在 Azure 上运行 Trifacta 的成本是 Trifacta 订阅成本加上虚拟机的 Azure 基础结构成本的组合。 |
|[Unifi 数据平台](https://unifisoftware.com/platform/) |Hadoop、HBase、风暴、Spark |Unifi 数据平台是一套无缝集成的自助服务数据工具，旨在使业务用户能够应对不断增加收入、降低成本或运营复杂性的数据挑战。 |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |适用于 HDInsight Spark 群集的难以数据应用。 |
|[等高线-驱动数据目录](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |使用 AI 对数据进行分类、组织和控制，并使用业务术语自动标记数据。 Waterline 的商业文献目录是一个用于自助分析、合规性和管理，以及 IT 管理计划的关键成功组件。 |

本文提供的说明将使用 Azure 门户。 也可以从门户导出 Azure 资源管理器模板或从供应商处获取 Resource Manage 模板的副本，并使用 Azure PowerShell 和 Azure 经典 CLI 部署模板。  请参阅[使用资源管理器模板在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

## <a name="prerequisites"></a>必备条件
如果想要在现有的 HDInsight 群集上安装 HDInsight 应用程序，必须有一个 HDInsight 群集。 若要创建群集，请参阅 [创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。 也可以在创建 HDInsight 群集时安装 HDInsight 应用程序。

## <a name="install-applications-to-existing-clusters"></a>将应用程序安装到现有群集
下面的过程演示如何将 HDInsight 应用程序安装到现有的 HDInsight 群集。

**安装 HDInsight 应用程序**

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 从左侧菜单中，导航到 "**所有服务**" > **Analytics** > **HDInsight 群集**"。
3. 从列表中选择一个 HDInsight 群集。  如果没有群集，必须先创建一个。  请参阅 [创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。
4. 在 "**设置**" 类别下，选择 "**应用程序**"。 你可以在主窗口中查看已安装应用程序的列表。 
   
    ![HDInsight 应用程序门户菜单](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 从菜单中选择 " **+ 添加**"。 您可以查看可用应用程序的列表。  如果 **+ Add**灰显，则意味着此版本的 HDInsight 群集没有应用程序。
   
    ![HDInsight 应用程序可用应用程序](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. 选择一个可用的应用程序，然后按照说明接受法律条款。

可以通过门户通知查看安装状态（选择门户顶部的钟形图标）。 安装应用程序后，应用程序会出现在“已安装的应用”列表中。

## <a name="install-applications-during-cluster-creation"></a>在群集创建期间安装应用程序

可以选择在创建群集时安装 HDInsight 应用程序。 在此过程中，HDInsight 应用程序会在群集创建并处于运行状态后安装。 若要使用 Azure 门户在群集创建过程中安装应用程序，请从 "**配置 + 定价**" 选项卡中选择 " **+ 添加应用程序**"。

![Azure 门户群集配置应用程序](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>列出已安装的 HDInsight 应用和属性
门户会显示群集的已安装 HDInsight 应用程序列表，以及每个已安装应用程序的属性。

**列出 HDInsight 应用程序并显示属性**

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 从左侧菜单中，导航到 "**所有服务**" > **Analytics** > **HDInsight 群集**"。
3. 从列表中选择一个 HDInsight 群集。
4. 在 "**设置**" 类别下，选择 "**应用程序**"。 你可以在主窗口中查看已安装应用程序的列表。 
   
    ![HDInsight 应用程序已安装的应用](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. 选择一个已安装的应用程序以显示属性。 属性列表：

    |properties | 说明 |
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

## <a name="troubleshoot"></a>故障排除
请参阅 [故障排除安装问题](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)。

## <a name="next-steps"></a>后续步骤
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure 市场。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [使用资源管理器模板在 HDInsight 中创建基于 Linux 的 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)：了解如何调用资源管理器模板创建 HDInsight 群集。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试 HDInsight 应用程序以及托管 HDInsight 应用程序。


---
title: 使用 web 浏览器、Azure HDInsight 创建 Apache Hadoop 群集
description: 了解如何使用 web 浏览器和 Azure 门户，在 Linux 上的 HDInsight 中创建 Apache Hadoop、Apache HBase、Apache Storm 或 Apache Spark 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/28/2019
ms.openlocfilehash: 3a64ba088a24f6202b74ae84ad7fb6c011d3bcbf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494770"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 门户是一种基于 Web 的管理工具，用于管理 Microsoft Azure 云中托管的服务和资源。 本文介绍如何使用门户创建基于 Linux 的 Azure HDInsight 群集。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **一个 Azure 订阅**。 请参阅 [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)（如何获取用于在 HDInsight 中测试 Hadoop 的 Azure 免费试用版）。
* **一个新式 Web 浏览器**。 Azure 门户使用 HTML5 和 JavaScript， 可能无法在旧版 Web 浏览器中正常运行。

## <a name="create-clusters"></a>创建群集

Azure 门户会公开大部分的群集属性。 使用 Azure 资源管理器模板可以隐藏许多详细信息。 有关详细信息，请参阅[使用资源管理器模板在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单中，导航到“+ 创建资源” **“Analytics”** “HDInsight” >   > 。

    ![在 Azure 门户中创建新群集](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "在 Azure 门户中创建新群集")

1. 从 "**创建 HDInsight 群集**" 页上，选择 "**转向经典创建体验**"。

    ![转向经典创建体验](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. 在“HDInsight”页上，选择“自定义(大小、设置、应用)”。

1. 选择“1 基础知识”。 然后输入以下信息。

    ![HDInsight 创建群集基础知识](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "在 Azure 门户中创建新群集")

    * 输入**群集名称**。 该名称必须全局唯一。

    * 从“订阅”下拉列表中选择用于此群集的 Azure 订阅。

    * 选择“群集类型”。 然后，选择要创建的群集的类型。 例如，Hadoop 和 Apache Spark。 “操作系统”将为 Linux。 接下来，选择群集类型版本。 如果不知道要选择哪个版本，请使用默认版本。 有关详细信息，请参阅 [HDInsight 群集版本](hdinsight-component-versioning.md)。
     
        > [!IMPORTANT]  
        > HDInsight 群集有各种类型。 这些类型与该群集进行优化的工作负荷或技术相对应。 没有任何方法支持创建组合多种类型的群集， 例如，一个群集同时具有 Storm 和 HBase 类型。
        
    * 对于“群集登录用户名”和“群集登录密码”，请分别为管理员用户提供用户名和密码。

    * 输入“SSH 用户名”。 如果希望 SSH 密码与在前面指定的管理员密码相同，则选中“使用与群集登录相同的密码”复选框。 否则，请提供“密码”或“公钥”来验证 SSH 用户。 建议的方法是公钥。 选择底部的“选择”，保存凭据配置。
   
        有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

    * 对于 **资源组**，请指定是创建新的资源组，还是使用现有资源组。

    * 指定在其中创建群集的数据中心**位置**。

    * 选择“下一步”转到下一页。

1. 在“2 安全性 + 网络”中，可以使用所提供的下拉菜单将群集连接到虚拟网络。 如果要将群集放入虚拟网络，请选择 Azure 虚拟网络和子网。 有关将 HDInsight 与虚拟网络配合使用的信息，请参阅为[Azure HDInsight 群集计划虚拟网络部署](hdinsight-plan-virtual-network-deployment.md)。 本文包含虚拟网络的特定配置要求。

    如果要使用**企业安全性套餐**，请按照以下说明进行操作：[使用 Azure Active Directory 域服务配置包含企业安全性套餐的 HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)。

    选择“下一步”转到下一页。

1. 从“3 存储”中，指定是要将 Azure 存储还是将 Azure Data Lake Storage 作为默认存储。 有关详细信息，请参阅下表。

     ![HDInsight 创建群集存储](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "在 Azure 门户中创建新群集")

     | 存储                                      | 说明 |
     |----------------------------------------------|-------------|
     | **将 Azure 存储 Blob 作为默认存储**   | <ul><li>对于“主存储类型”，选择“Azure 存储”。 如果要指定属于 Azure 订阅的存储帐户，则请选择“我的订阅”作为“选择方法”， 然后选择存储帐户。 否则，请选择“访问密钥”， 然后提供想要从 Azure 订阅外部选择的存储帐户的信息。</li><li>对于“默认容器”，请选择门户建议的默认容器名称或自行指定。</li><li>如果 Azure Blob 存储为默认存储，则也可选择“其他存储帐户”，以指定要与群集关联的其他存储帐户。 对于“Azure 存储密钥”，请选择“添加存储密钥”。 然后，可以从 Azure 订阅或其他订阅提供一个存储帐户。 提供存储帐户访问密钥。</li><li>如果 Blob 存储为默认存储，则也可选择“Data Lake Storage 访问权限”，将 Azure Data Lake Storage 指定为其他存储。 有关详细信息，请参阅[快速入门：在 HDInsight 中设置群集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)。</li></ul> |
     | **将 Azure Data Lake Storage 作为默认存储** | 对于“主存储类型”，请选择“Azure Data Lake Storage Gen1”或“Azure Data Lake Storage Gen2”， 然后参阅文章[快速入门：在 HDInsight 中设置群集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)了解相关说明。 |
     | **外部元存储**                      | 以选项的方式指定一个 SQL 数据库，用于保存与群集关联的 Apache Hive 和 Apache Oozie 元数据。 对于“为 Hive 选择 SQL 数据库”选项，请选择一个 SQL 数据库， 然后为数据库提供用户名和密码。 为 Oozie 元数据重复以上这些步骤。<br><br>将 Azure SQL 数据库用于元存储时的一些注意事项如下所示： <ul><li>用于元存储的 Azure SQL 数据库必须允许连接到其他 Azure 服务，包括 Azure HDInsight。 在 Azure SQL 数据库仪表板的右侧选择服务器名称。 此服务器是运行 SQL 数据库实例的服务器。 进入服务器视图以后，选择“配置”。 然后，对于“Azure 服务”，请选择“是”。 再选择“保存”。</li><li>创建元存储时，请勿使用短划线或连字符来命名数据库。 这些字符可能导致群集创建过程失败。</li></ul> |

     > [!WARNING]  
     > 不支持在 HDInsight 群集之外的其他位置使用别的存储帐户。

     选择“下一步”转到下一页。

1. 从“4 应用程序(可选)”中，选择任何所需的应用程序。 Microsoft、独立软件供应商 (ISV) 或你自己都可以开发这些应用程序。 有关详细信息，请参阅[在群集创建期间安装应用程序](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)。

    选择“下一步”转到下一页。

1. “5 群集大小”显示用于此群集的节点的相关信息。 设置群集所需的工作节点数。 运行群集的估计成本也会显示出来。

    ![HDInsight 创建群集节点](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "指定群集节点数")

   > [!IMPORTANT]  
   > 如果计划使用 32 个以上的辅助角色节点，则请选择至少具有 8 个核心和 14 GB RAM 的头节点大小。 可以在创建群集时计划节点，也可以在创建群集之后通过缩放群集来计划节点。
   >
   > 有关节点大小和相关费用的详细信息，请参阅 [Azure HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

    选择“下一步”转到下一页。

1. 从“6 脚本操作”中，可以自定义群集以安装自定义组件。 如果要在创建群集时使用自定义脚本来自定义群集，请使用此选项。 有关脚本操作的详细信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

   选择“下一步”转到下一页。

1. 从“7 摘要”中，验证之前输入的信息， 然后，选择“创建”。

     ![HDInsight 创建群集摘要](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "指定群集节点数")
    
    > [!NOTE]  
    > 创建群集需要一些时间，通常约 20 分钟左右。 监视“通知”以检查预配进程。

1. 创建进程完成后，选择“部署成功”通知中的“转到资源”。 群集窗口会提供以下信息。

    ![HDI Azure 门户群集概述](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "群集属性")

    窗口中的图标解释如下：

    * “概览”选项卡提供有关群集的所有基本信息。 例如，名称、其所属的资源组、位置、操作系统、群集仪表板 URL。
    * “仪表板”你将定向到与群集关联的 Ambari 门户。
    * **安全外壳**提供使用 SSH 访问群集时所需的信息。
    * 使用“缩放群集”可以增加与群集关联的辅助角色节点数。
    * 通过“删除”来删除 HDInsight 群集。

## <a name="customize-clusters"></a>自定义群集
* [使用 Bootstrap 自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="next-steps"></a>后续步骤

你已成功创建 HDInsight 群集。 现在可以了解如何使用群集了。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 群集

* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](hadoop/hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 群集

* [HDInsight 中的 Apache HBase 入门](hbase/apache-hbase-tutorial-get-started-linux.md)
* [为 Apache HBase on HDInsight 开发 Java 应用程序](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 群集

* [为 Apache Storm on HDInsight 开发 Java 拓扑](storm/apache-storm-develop-java-topology.md)
* [在 Apache Storm on HDInsight 中使用 Python 组件](storm/apache-storm-develop-python-topology.md)
* [使用 Apache Storm on HDInsight 部署和监视拓扑](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark 群集

* [使用 Scala 创建独立的应用程序](spark/apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](spark/apache-spark-machine-learning-mllib-ipython.md)

---
title: 使用 web 浏览器、Azure HDInsight 创建 Apache Hadoop 群集
description: 了解如何使用 web 浏览器和 Azure 门户，在 Linux 上的 HDInsight 中创建 Apache Hadoop、Apache HBase、Apache Storm 或 Apache Spark 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/19/2020
ms.openlocfilehash: bcc71b7244ba3498b2fcde023d372e67f41d6117
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623182"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 门户是一种基于 Web 的管理工具，用于管理 Microsoft Azure 云中托管的服务和资源。 本文介绍如何使用门户创建基于 Linux 的 Azure HDInsight 群集。 "[创建 HDInsight 群集](./hdinsight-hadoop-provision-linux-clusters.md)" 中提供了更多详细信息。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure 门户会公开大部分的群集属性。 使用 Azure 资源管理器模板可以隐藏许多详细信息。 有关详细信息，请参阅[使用资源管理器模板在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-clusters"></a>创建群集

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在顶部菜单中，选择 " **+ 创建资源**"。

    ![在 Azure 门户中创建新群集](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "在 Azure 门户中创建新群集")

1. 选择 "**分析** > **Azure HDInsight** "，以打开 "**创建 HDInsight 群集**" 页。

## <a name="basics"></a>基础

![HDInsight 创建群集基础知识](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "在 Azure 门户中创建新群集")

从 "**基本**信息" 选项卡中，提供以下信息：

|properties |说明 |
|---|---|
|订阅|从下拉列表中，选择用于群集的 Azure 订阅。|
|资源组|从下拉列表中，选择现有资源组，或选择 "**新建**"。|
|群集名称|输入任何全局唯一的名称。|
|区域|从下拉列表中，选择要在其中创建群集的区域。|
|群集类型|选择 "**选择群集类型**" 以打开列表。 从列表中选择所需的群集类型。 HDInsight 群集有各种类型。 这些类型与该群集进行优化的工作负荷或技术相对应。 没有任何方法支持创建组合多种类型的群集，|
|版本|从下拉列表中选择一个**版本**。 如果不知道要选择哪个版本，请使用默认版本。 有关详细信息，请参阅 [HDInsight 群集版本](hdinsight-component-versioning.md)。|
|群集登录用户名|提供用户名，默认值为**admin**。|
|群集登录密码|提供密码。|
|确认群集登录密码|重新输入密码|
|安全外壳 (SSH) 用户名|提供用户名，默认值为**sshuser**|
|对 SSH 使用群集登录密码|如果需要的 SSH 密码与之前指定的管理员密码相同，请选中 "**使用 ssh 的群集登录密码**" 复选框。 否则，请提供“密码”或“公钥”来验证 SSH 用户。 建议的方法是公钥。 选择底部的“选择”，保存凭据配置。  有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。|

选择 "**下一步：存储 >" >** 转到下一个选项卡。

## <a name="storage"></a>存储

![HDInsight 创建群集存储](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "在 Azure 门户存储中创建新群集")

### <a name="primary-storage"></a>主存储

从 "**主要存储类型**" 下拉列表中，选择默认存储类型。 要完成的后续字段将因你的选择而异。 对于**Azure 存储**：

1. 对于 "选择**方法**"，请选择 "**从列表中选择**"，或**使用 "访问密钥**"。
    * 对于 "**从列表中选择**"，请从下拉列表中选择你的**主存储帐户**，或选择 "**新建**"。
    * 对于 "**使用访问密钥**"，请输入**存储帐户名称**。 然后提供**访问密钥**。

1. 对于 "**容器**"，接受默认值，或者输入一个新值。

### <a name="additional-azure-storage"></a>其他 Azure 存储

可选：对于其他群集存储，请选择 "**添加 Azure 存储空间**"。 不支持在 HDInsight 群集之外的其他区域中使用其他存储帐户。

### <a name="metastore-settings"></a>元存储设置

可选：指定现有的 SQL 数据库以保存群集外 Apache Hive、Apache Oozie 和/或 Apache Ambari 元数据。 用于元存储的 Azure SQL 数据库必须允许连接到其他 Azure 服务，包括 Azure HDInsight。 创建元存储时，请勿使用短划线或连字符来命名数据库。 这些字符可能导致群集创建过程失败。

选择 "**下一步：安全性 + 网络 > >** 转到下一个选项卡。

## <a name="security--networking"></a>安全性 + 网络

![HDInsight 创建群集安全网络](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight 创建群集安全网络")

在 "**安全 + 网络**" 选项卡中，提供以下信息：

|properties |说明 |
|---|---|
|企业安全数据包|可选：选中此复选框可使用**企业安全性套餐**。 有关详细信息，请参阅[使用 Azure Active Directory 域服务配置具有企业安全性套餐的 HDInsight 群集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)。|
|TLS|可选：从下拉列表中选择一个 TLS 版本。 有关详细信息，请参阅[传输层安全性](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security)。|
|虚拟网络|可选：从下拉列表中选择现有的虚拟网络和子网。 有关信息，请参阅为[Azure HDInsight 群集计划虚拟网络部署](hdinsight-plan-virtual-network-deployment.md)。 本文包含虚拟网络的特定配置要求。|
|磁盘加密设置|可选：选中此复选框可以使用加密。 有关详细信息，请参阅[客户托管的密钥磁盘加密](./disk-encryption.md)。|
|Kafka REST 代理|此设置仅适用于群集类型 Kafka。 有关详细信息，请参阅[使用 REST 代理](./kafka/rest-proxy.md)。|
|标识|可选：从下拉列表中选择现有的用户分配服务标识。 有关详细信息，请参阅[Azure HDInsight 中的托管标识](./hdinsight-managed-identities.md)。|

选择 "**下一步：配置 + 定价 > >** 转到下一个选项卡。

## <a name="configuration--pricing"></a>配置 + 定价

![HDInsight 创建群集配置](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "配置和定价选项卡")

从 "**配置 + 定价**" 选项卡中，提供以下信息：

|properties |说明 |
|---|---|
|+ 添加应用程序|可选：选择所需的任何应用程序。 Microsoft、独立软件供应商 (ISV) 或你自己都可以开发这些应用程序。 有关详细信息，请参阅[在群集创建期间安装应用程序](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)。|
|节点大小|可选：选择不同大小的节点。|
|节点数|可选：输入指定节点类型的节点数。 如果计划使用超过32个工作节点，请选择至少具有8个核心和 14 GB RAM 的头节点大小。 可以在创建群集时计划节点，也可以在创建群集之后通过缩放群集来计划节点。|
|启用自动缩放|可选：选中此复选框以启用该功能。 有关详细信息，请参阅[自动缩放 Azure HDInsight 群集](./hdinsight-autoscale-clusters.md)。|
|+ 添加脚本操作|可选：如果要在创建群集时使用自定义脚本自定义群集，则可以使用此选项。 有关脚本操作的详细信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。|

选择 "查看" "创建" " **> >** 验证群集配置，然后转到最后一个选项卡。

## <a name="review--create"></a>查看 + 创建

![HDInsight 创建群集摘要](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "指定群集节点数")

查看设置。 选择“创建”可创建群集。

创建群集需要一些时间，通常约 20 分钟左右。 监视“通知”以检查预配进程。

## <a name="post-creation"></a>后期创建

创建进程完成后，选择“部署成功”通知中的“转到资源”。 群集窗口会提供以下信息。

![HDI Azure 门户群集概述](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "群集属性")

窗口中的某些图标如下所述：

|properties | 说明 |
|---|---|
|概述|提供有关群集的所有基本信息。 例如，名称、其所属的资源组、位置、操作系统、群集仪表板 URL。|
|群集仪表板|将用户定向到与群集关联的 Ambari 门户。|
|SSH + 群集登录|提供使用 SSH 访问群集时所需的信息。|
|删除|删除 HDInsight 群集。|

## <a name="customize-clusters"></a>自定义群集

* [使用 Bootstrap 自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>删除群集

请参阅[使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集](./hdinsight-delete-cluster.md)。

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](./hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>后续步骤

你已成功创建 HDInsight 群集。 现在可以了解如何使用群集了。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 群集

* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
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

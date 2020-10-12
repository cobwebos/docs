---
title: 使用 Web 浏览器创建 Apache Hadoop 群集 - Azure HDInsight
description: 了解如何在 HDInsight 上创建 Apache Hadoop、Apache HBase、Apache Storm 或 Apache Spark 群集。 使用 web 浏览器和 Azure 门户。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/06/2020
ms.openlocfilehash: 1ba2f5e4b88ae6ae0ed15dbfbbc4fa5c55c45a77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87874021"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 门户是一种基于 Web 的管理工具，用于管理 Microsoft Azure 云中托管的服务和资源。 本文介绍如何使用门户创建基于 Linux 的 Azure HDInsight 群集。 [创建 HDInsight 群集](./hdinsight-hadoop-provision-linux-clusters.md)一文提供了其他详细信息。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure 门户会公开大部分的群集属性。 使用 Azure 资源管理器模板可以隐藏许多详细信息。 有关详细信息，请参阅[使用资源管理器模板在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-clusters"></a>创建群集

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在顶部菜单中，选择“+ 创建资源”。

    ![在 Azure 门户中创建新群集](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "在 Azure 门户中创建新群集")

1. 选择“分析” > “Azure HDInsight”，转到“创建 HDInsight 群集”页。

## <a name="basics"></a>基础知识

![HDInsight 创建群集基本信息](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "在 Azure 门户中创建新群集")

在“基本信息”选项卡中提供以下信息：

|属性 |说明 |
|---|---|
|订阅|从下拉列表中选择用于此群集的 Azure 订阅。|
|资源组|从下拉列表中选择现有资源组，或选择“新建”。|
|群集名称|输入任何全局唯一的名称。|
|区域|从下拉列表中，选择在其中创建群集的区域。|
|群集类型|单击“选择群集类型”，打开一个列表。 从列表中选择所需的群集类型。 HDInsight 群集有不同的类型。 这些类型与该群集进行优化的工作负荷或技术相对应。 没有任何方法支持创建组合多种类型的群集，|
|版本|从下拉列表中，选择一个**版本**。 如果不知道要选择哪个版本，请使用默认版本。 有关详细信息，请参阅 [HDInsight 群集版本](hdinsight-component-versioning.md)。|
|群集登录用户名|提供用户名，默认为 **admin**。|
|群集登录密码|提供密码。|
|确认群集登录密码|重新输入密码|
|安全外壳 (SSH) 用户名|提供用户名，默认为 **sshuser**|
|对 SSH 使用群集登录密码|如果希望 SSH 密码与此前指定的管理员密码相同，则选中“对 SSH 使用群集登录密码”复选框。 否则，请提供“密码”或“公钥”来验证 SSH 用户。 建议的方法是公钥。 选择底部的“选择”，保存凭据配置。  有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。|

在完成时选择“下一步:存储 >>”，转到下一选项卡。

## <a name="storage"></a>存储

> [!WARNING] 
> 从 2020 年 6 月 15 日开始，客户将无法使用 HDInsight 创建新的服务主体。 请参阅[使用 Azure Active Directory 创建服务主体和证书](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

![HDInsight 创建群集存储](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "在 Azure 门户中创建新群集 - 存储")

### <a name="primary-storage"></a>主存储

从”主存储类型”的下拉列表中，选择默认存储类型。 要完成的后续字段将因选择而异。 对于 **Azure 存储**：

1. 至于“选择方法”，请选择“从列表中选择”或“使用访问密钥”。 
    * 接下来，对于“从列表中选择”，请从下拉列表中选择“主存储帐户”，或者选择“新建”。
    * 对于“使用访问密钥”，请输入**存储帐户名称**。 然后，请提供**访问密钥**。

1. 对于“容器”，请接受默认值，或者输入一个新值。

### <a name="additional-azure-storage"></a>其他 Azure 存储

可选：选择“添加 Azure 存储”，获取其他群集存储。 不支持在 HDInsight 群集之外的其他区域使用别的存储帐户。

### <a name="metastore-settings"></a>元存储设置

可选：指定现有的 SQL 数据库，以将 Apache Hive、Apache Oozie、或 Apache Ambari 元数据保存在群集之外。 用于元存储的 Azure SQL 数据库必须允许连接到其他 Azure 服务，包括 Azure HDInsight。 创建元存储时，请勿使用短划线或连字符来命名数据库。 这些字符可能导致群集创建过程失败。

> [!IMPORTANT]
> 对于支持元存储的群集形状，默认元存储提供具有基本层 5 DTU 限制（不可升级）的 Azure SQL 数据库！ 适用于基本测试目的。 对于大型或生产工作负载，我们建议迁移到外部元存储。

在完成时选择“下一步:安全性 + 网络 >>”，转到下一选项卡。

## <a name="security--networking"></a>安全性 + 网络

![HDInsight 创建群集安全网络](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight 创建群集安全网络")

在“安全性 + 网络”选项卡中提供以下信息：

|属性 |说明 |
|---|---|
|企业安全数据包|可选：选中此复选框可使用“企业安全性套餐”。 有关详细信息，请参阅[使用 Azure Active Directory 域服务配置具有企业安全性套餐的 HDInsight 群集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)。|
|TLS|可选：从下拉列表中选择 TLS 版本。 有关详细信息，请参阅[传输层安全性](./transport-layer-security.md)。|
|虚拟网络|可选：从下拉列表中选择现有的虚拟网络和子网。 有关信息，请参阅[为 Azure HDInsight 群集规划虚拟网络部署](hdinsight-plan-virtual-network-deployment.md)。 本文包含虚拟网络的特定配置要求。|
|磁盘加密设置|可选：选中此复选框即可使用加密。 有关详细信息，请参阅[客户管理的密钥磁盘加密](./disk-encryption.md)。|
|Kafka REST 代理|此设置仅适用于群集类型 Kafka。 有关详细信息，请参阅[使用 REST 代理](./kafka/rest-proxy.md)。|
|标识|可选：从下拉列表中选择一个用户分配的现有服务标识。 有关详细信息，请参阅 [Azure HDInsight 中的托管标识](./hdinsight-managed-identities.md)。|

在完成时选择“下一步:配置 + 定价 >>”，转到下一选项卡。

## <a name="configuration--pricing"></a>配置 + 定价

![HDInsight 创建群集配置](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "“配置和定价”选项卡")

在“配置 + 定价”选项卡中提供以下信息：

|属性 |说明 |
|---|---|
|+ 添加应用程序|可选：选择所需的任何应用程序。 Microsoft、独立软件供应商 (ISV) 或你自己都可以开发这些应用程序。 有关详细信息，请参阅[在群集创建期间安装应用程序](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)。|
|节点大小|可选：选择不同大小的节点。|
|节点数|可选：输入指定节点类型的节点数。 如果计划使用 32 个以上的辅助角色节点，则请选择至少具有 8 个核心和 14 GB RAM 的头节点大小。 可以在创建群集时计划节点，也可以在创建群集之后通过缩放群集来计划节点。|
|启用自动缩放|可选：选中相应的复选框以启用该功能。 有关详细信息，请参阅[自动缩放 Azure HDInsight 群集](./hdinsight-autoscale-clusters.md)。|
|+ 添加脚本操作|可选：如果要在创建群集时使用自定义脚本来自定义群集，请使用此选项。 有关脚本操作的详细信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。|

选择“查看 + 创建>>”，验证群集配置并转到最后一个选项卡。

## <a name="review--create"></a>查看 + 创建

![HDInsight 创建群集摘要](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "指定群集节点数")

查看设置。 选择“创建”可创建群集。

创建群集需要一些时间，通常约 20 分钟左右。 监视“通知”以检查预配进程。

## <a name="post-creation"></a>创建帖子

创建进程完成后，选择“部署成功”通知中的“转到资源” 。 群集窗口提供以下信息。

![HDI Azure 门户群集概述](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "群集属性")

窗口中的某些图标解释如下：

|属性 | 说明 |
|---|---|
|概述|提供有关群集的所有基本信息。 例如，名称、其所属的资源组、位置、操作系统、群集仪表板 URL。|
|群集仪表板|将你定向到与群集关联的 Ambari 门户。|
|SSH + 群集登录|提供使用 SSH 访问群集时所需的信息。|
|Delete|删除 HDInsight 群集。|

## <a name="delete-the-cluster"></a>删除群集

请参阅[使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集](./hdinsight-delete-cluster.md)。

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](./hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>后续步骤

你已成功创建 HDInsight 群集。 现在可以了解如何使用群集了。

* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [HDInsight 中的 Apache HBase 入门](hbase/apache-hbase-tutorial-get-started-linux.md)
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)

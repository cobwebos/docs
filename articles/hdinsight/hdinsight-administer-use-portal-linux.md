---
title: 使用 Azure 门户管理 HDInsight 中的 Apache Hadoop 群集
description: 了解如何使用 Azure 门户创建和管理 Azure HDInsight 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/24/2020
ms.openlocfilehash: 05d057be76a1b468f892b3123080e32a948153ae
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598492"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>使用 Azure 门户管理 HDInsight 中的 Apache Hadoop 群集

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

使用 [Azure门户](https://portal.azure.com)可以管理 Azure HDInsight 中的 [Apache Hadoop](https://hadoop.apache.org/) 群集。 使用上述选项卡选择器，可以了解如何使用其他工具在 HDInsight 中管理 Hadoop 群集。

## <a name="prerequisites"></a>先决条件

HDInsight 中的现有 Apache Hadoop 群集。  请参阅[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="getting-started"></a>入门

登录到 [https://portal.azure.com](https://portal.azure.com)。

## <a name="list-and-show-clusters"></a><a name="showClusters"></a> 列出并显示群集

从“HDInsight 群集”页会列出现有的群集  。  在门户中：
1. 在左侧菜单中，选择“所有服务”  。
2. 在“ANALYTICS”下，选择“HDInsight 群集”   。

## <a name="cluster-home-page"></a><a name="homePage"></a> 群集主页

从 " [**HDInsight 群集**](#showClusters)" 页中选择群集名称。  此时会打开“概览”视图，该视图类似于下图： 

![Azure 门户 HDInsight 群集概要](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**顶部菜单：**  

| 项目| 说明 |
|---|---|
|移动|将群集移至其他资源组或其他订阅。|
|Delete|删除群集。 |
|刷新|刷新视图。|

**Left menu:**  

  - **顶部左侧菜单**

    | 项目| 说明 |
    |---|---|
    |概述|提供群集的常规信息。|
    |活动日志|显示和查询活动日志。|
    |访问控制 (IAM)|使用角色分配。  请参阅[使用角色分配管理对 Azure 订阅资源的访问权限](../role-based-access-control/role-assignments-portal.md)。|
    |Tags|可让用户设置键/值对，以定义云服务的自定义分类。 例如，用户可以创建名为 **project**的键，并对与特定项目关联的所有服务使用一个公用值。|
    |诊断并解决问题|显示故障排除信息。|
    |快速入门|显示可帮助你开始使用 HDInsight 的信息。|
    |工具|HDInsight 相关工具的帮助信息。|

  - **“设置”菜单**  

    | 项目| 说明 |
    |---|---|
    |群集大小|检查、增加和减少群集辅助角色节点的数量。 请参阅[缩放群集](hdinsight-administer-use-portal-linux.md#scale-clusters)。|
    |配额限制|显示订阅的已用核心数和可用核心数。|
    |SSH + 群集登录|显示使用安全 Shell (SSH) 连接与群集建立连接的说明。 有关详细信息，请参阅 [将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。|
    |Data Lake Storage Gen1|配置 Data Lake Storage Gen1 访问。  请参阅[快速入门：在 HDInsight 中设置群集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)。|
    |存储帐户|查看存储帐户和密钥。 存储帐户是在群集创建过程中进行配置。|
    |应用程序|添加/删除 HDInsight 应用程序。  请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。|
    |脚本操作|在群集上运行 Bash 脚本。 请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。|
    |外部元存储|查看 [Apache Hive](https://hive.apache.org/) 和 [Apache Oozie](https://oozie.apache.org/) 元存储。 只能在群集创建过程中配置元存储。|
    |HDInsight 合作伙伴|添加/删除当前 HDInsight 合作伙伴。|
    |属性|查看[群集属性](#properties)。|
    |锁|添加锁防止群集遭到修改或删除。|
    |导出模板|显示和导出群集的 Azure 资源管理器模板。 目前，只能导出相关的 Azure 存储帐户。 请参阅[使用 Azure 资源管理器模板在 HDInsight 中创建基于 Linux 的 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。|

  - **监视菜单**

    | 项目| 说明 |
    |---|---|
    |警报|管理警报和操作。|
    |指标|监视 Azure Monitor 日志中的群集指标。|
    |诊断设置|存储诊断指标的位置设置。|
    |Azure Monitor|在 Azure Monitor 中监视群集。|

  - **支持 + 故障排除菜单**

    | 项目| 说明 |
    |---|---|
    |资源运行状况|参阅 [Azure 资源运行状况概述](../service-health/resource-health-overview.md)。|
    |新建支持请求|允许用户通过 Microsoft 支持创建支持票证。|

## <a name="cluster-properties"></a><a name="properties"></a> 群集属性

在“[群集主页](#homePage)”的“设置”下，选择“属性”。  

|项目 | 说明 |
|---|---|
|段|群集名称。|
|群集 URL|Ambari Web 界面的 URL。|
|专用终结点|群集的专用终结点。|
|安全外壳 (SSH)|用于通过 SSH 访问群集的用户名和主机名。|
|状态|下列其中一项：Aborted、Accepted、ClusterStorageProvisioned、AzureVMConfiguration、HDInsightConfiguration、Operational、Running、Error、Deleting、Deleted、Timedout、DeleteQueued、DeleteTimedout、DeleteError、PatchQueued、CertRolloverQueued、ResizeQueued 或 ClusterCustomization。|
|区域|Azure 位置。 有关受支持的 Azure 位置的列表，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)中的“区域”  下拉列表框。|
|创建日期|部署群集的日期。|
|操作系统|“Windows”或“Linux”。  |
|TYPE|Hadoop、HBase、Storm、Spark。|
|版本|请参阅 [HDInsight 版本](hdinsight-component-versioning.md)。|
|最低 TLS 版本|TLS 版本。|
|订阅|订阅名称。|
|默认数据源|默认的群集文件系统。|
|工作器节点大小|工作节点的所选 VM 大小。|
|头节点大小|头节点的所选 VM 大小。|
|虚拟网络|群集将要部署到的虚拟网络的名称（如果在部署时已选择）。|

## <a name="move-clusters"></a>移动群集

可以将 HDInsight 群集移到另一个 Azure 资源组或另一个订阅。

在[群集主页](#homePage)中执行以下操作：

1. 在顶部菜单中选择“移动”。 
2. 选择“移动到另一资源组”或“移动到另一订阅”。  
3. 按新页面中的说明操作。

## <a name="delete-clusters"></a>删除群集

删除群集不会删除默认存储帐户或任何链接的存储帐户。 可以使用相同的存储帐户和相同的元存储来重新创建群集。 建议在重新创建群集时使用新的默认 Blob 容器。

在[群集主页](#homePage)中执行以下操作：

1. 从顶部菜单中选择“删除”  。
2. 按新页面中的说明操作。

另请参阅[暂停/关闭群集](#pauseshut-down-clusters)。

## <a name="add-additional-storage-accounts"></a>添加其他存储帐户

创建群集后，可以添加其他 Azure 存储帐户和 Azure Data Lake Storage 帐户。 有关详细信息，请参阅[将其他存储帐户添加到 HDInsight](./hdinsight-hadoop-add-storage.md)。

## <a name="scale-clusters"></a>缩放群集

使用群集缩放功能可更改 Azure HDInsight 群集使用的辅助角色节点数，而无需重新创建群集。

有关完整信息，请参阅[缩放 HDInsight 群集](./hdinsight-scaling-best-practices.md)。

## <a name="pauseshut-down-clusters"></a>暂停/关闭群集

大多数 Hadoop 作业只是偶尔运行的批处理作业。 对于大多数 Hadoop 群集，群集不会用于处理的时间较长。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。
此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

可以通过许多方式对此过程进行程序性处理：

- 用户 Azure 数据工厂。 有关创建按需 HDInsight 链接服务的信息，请参阅[在 HDInsight 中使用 Azure 数据工厂创建基于 Linux 的按需 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-adf.md)。
- 使用 Azure PowerShell。  请参阅[分析航班延误数据](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)。
- 使用 Azure CLI。 请参阅[使用 Azure CLI 管理 Azure HDInsight 群集](hdinsight-administer-use-command-line.md)。
- 使用 HDInsight .NET SDK。 请参阅[提交 Apache Hadoop 作业](hadoop/submit-apache-hadoop-jobs-programmatically.md)。

有关定价信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。 要从门户中删除群集，请参阅 [删除群集](#delete-clusters)

## <a name="upgrade-clusters"></a>升级群集

请参阅[将 HDInsight 群集升级到更新的版本](./hdinsight-upgrade-cluster.md)。

## <a name="open-the-apache-ambari-web-ui"></a>打开 Apache Ambari Web UI

Ambari 提供由其 RESTful API 提供支持的直观、易用的 Hadoop 管理 Web UI。 Ambari 使系统管理员能够管理和监视 Hadoop 群集。

在[群集主页](#homePage)中执行以下操作：

1. 选择“群集仪表板”。 

    ![HDInsight Apache Hadoop 群集菜单](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. 从新页面中选择“Ambari 主页”。 
1. 输入群集用户名和密码。  默认群集用户名为“admin”  。

有关详细信息，请参阅[使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)。

## <a name="change-passwords"></a>更改密码

HDInsight 群集可以有两个用户帐户。 HDInsight 群集用户帐户（HTTP 用户帐户）和 SSH 用户帐户是在创建过程中创建的。 可以使用门户更改群集用户帐户密码，使用脚本操作更改 SSH 用户帐户。

### <a name="change-the-cluster-user-password"></a>更改群集用户密码

> [!NOTE]  
> 更改群集用户 (admin) 的密码可能会导致针对此群集运行的脚本操作失败。 如果有任何持久性脚本操作以工作节点为目标，则通过重设大小操作在群集中添加节点时，这些脚本可能会失败。 有关脚本操作的详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

在[群集主页](#homePage)中执行以下操作：
1. 在“设置”下选择“SSH + 群集登录”   。
2. 选择“重置凭据”。 
3. 在文本框中输入并确认新密码。
4. 选择“确定”  。

将在群集中的所有节点上更改密码。

### <a name="change-the-ssh-user-password-or-public-key"></a>更改 SSH 用户密码或公钥

1. 使用文本编辑器将以下文本保存为名为**changecredentials.sh**的文件。

    > [!IMPORTANT]  
    > 所用的编辑器必须使用 LF 作为行尾。 如果编辑器使用 CRLF，则脚本将无法正常工作。

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. 将该文件上传到可以使用 HTTP 或 HTTPS 地址从 HDInsight 访问的存储位置。 例如，某个公共文件存储（如 OneDrive 或 Azure Blob 存储）。 将 URI（HTTP 或 HTTPS 地址）保存到文件中，因为下一步需要用到此 URI。
3. 在[群集主页](#homePage)的“设置”下，选择“脚本操作”。  
4. 在“脚本操作”页中，选择“提交新项”   。
5. 在“提交脚本操作”页中，输入以下信息： 

> [!NOTE]
> SSH 密码不能包含以下字符：
> ```
> " ' ` / \ < % ~ | $ & ! 
> ```

   | 字段 | 值 |
   | --- | --- |
   | 脚本类型 | 从下拉列表中选择“- 自定义”。****|
   | 名称 |"更改 ssh 凭据" |
   | Bash 脚本 URI |Changecredentials.sh 文件的 URI |
   | 节点类型：（Head、Worker、Nimbus、监察员或 Zookeeper。） |✓ 适用于所有列出的节点类型 |
   | 参数 |输入 SSH 用户名和新密码。 用户名与密码之间应有一个空格。 |
   | 保留此脚本操作... |将此字段保留未选中状态。 |

6. 选择“创建”**** 以应用脚本。 脚本完成后，便可以使用 SSH 和新凭据连接到群集。

## <a name="find-the-subscription-id"></a>查找订阅 ID

每个群集都绑定到一个 Azure 订阅。  Azure 订阅 ID 在[群集主页](#homePage)中可见。

## <a name="find-the-resource-group"></a>查找资源组

在 Azure 资源管理器模式下，使用 Azure 资源管理器组创建每个 HDInsight 群集。 资源管理器组在[群集主页](#homePage)中可见。

## <a name="find-the-storage-accounts"></a>查找存储帐户

HDInsight 群集使用 Azure 存储帐户或 Azure Data Lake Storage 来存储数据。 每个 HDInsight 群集都可拥有一个默认存储帐户和多个链接的存储帐户。 若要列出存储帐户，请在[群集主页](#homePage)的“设置”下选择“存储帐户”。********

## <a name="monitor-jobs"></a>监视作业

请参阅[使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md#monitoring)。

## <a name="cluster-size"></a>群集大小

[群集主页](#homePage)的 "**群集大小**" 磁贴显示分配给此群集的核心数，以及这些核心是如何分配给此群集中的节点的。

> [!IMPORTANT]  
> 若要监视 HDInsight 群集提供的服务，必须使用 Ambari Web 或 Ambari REST API。 有关如何使用 Ambari 的详细信息，请参阅[使用 Apache Ambari 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>连接到群集

- [将 Apache Hive 和 HDInsight 配合使用](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>后续步骤

本文介绍了一些基本的管理功能。 若要了解详细信息，请参阅以下文章：

- [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
- [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)
- [创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)
- [有关使用 Apache Ambari REST API 的详细信息](hdinsight-hadoop-manage-ambari-rest-api.md)
- [使用 HDInsight 中的 Apache Hive](hadoop/hdinsight-use-hive.md)
- [在 HDInsight 中使用 Apache Sqoop](hadoop/hdinsight-use-sqoop.md)
- [在 HDInsight 中通过 Apache Hive 和 Apache Pig 使用 Python 用户定义函数 (UDF)](hadoop/python-udf-hdinsight.md)
- [Azure HDInsight 包含哪个版本的 Apache Hadoop？](hdinsight-component-versioning.md)

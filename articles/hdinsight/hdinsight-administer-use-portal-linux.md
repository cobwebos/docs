---
title: "使用 Azure 门户管理 HDInsight 中的 Hadoop 群集 | Microsoft Docs"
description: "了解如何使用 Azure 门户创建和管理 HDInsight 群集。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: b0a9b808f2c1a65d10c1c12500325a9b5a34bc5a
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>使用 Azure 门户管理 HDInsight 中的 Hadoop 群集

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

使用 [Azure门户][azure-portal]可以管理 Azure HDInsight 中的 Hadoop 群集。 使用上述选项卡选择器，可以了解如何使用其他工具在 HDInsight 中管理 Hadoop 群集。

**先决条件**

若要按照本文的步骤进行操作，需要 Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## <a name="open-the-azure-portal"></a>打开 Azure 门户
1. 登录到 [https://portal.azure.com](https://portal.azure.com)。
2. 打开门户之后，可以：

   * 单击左侧菜单中的“创建资源”以创建新的群集：

       ![新建 HDInsight 群集按钮](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       在“搜索 Marketplace”中输入 HDInsight ，单击 HDInsight，然后单击“创建”。

   * 在左侧菜单中单击“HDInsight 群集”以列出现有群集：

       ![Azure 门户中的 HDInsight 群集按钮](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       如果看不到“HDInsight 群集”按钮，请单击“智能 + 分析”部分下的“HDInsight 群集”。


## <a name="create-clusters"></a>创建群集
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight 使用各种 Hadoop 组件。 有关已验证和支持的组件的列表，请参阅 [Azure HDInsight 包含哪个版本的 Hadoop？](hdinsight-component-versioning.md) 有关创建群集的一般信息，请参阅[在 HDInsight 中创建 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

### <a name="access-control-requirements"></a>访问控制要求

在创建 HDInsight 群集时，必须指定 Azure 订阅。 可以在新的 Azure 资源组或现有的资源组中创建此群集。 可以使用以下步骤验证是否具有创建 HDInsight 群集的权限：

- 若要创建新的资源组：

    1. 登录到 [Azure 门户](https://portal.azure.com)。
    2. 从左侧菜单中单击“订阅”。 它有一个黄色的密钥图标。 会看到订阅列表。
    3. 单击用于创建群集的订阅。 
    4. 单击“我的权限”。  它显示你在订阅中的[角色](../active-directory/role-based-access-control-what-is.md#built-in-roles)。 至少需要具有“参与者”访问权限才能创建 HDInsight 群集。

- 要使用现有资源组：

    1. 登录到 [Azure 门户](https://portal.azure.com)。
    2. 从左侧菜单中单击“资源组”以列出资源组。
    3. 单击要用于创建 HDInsight 群集的资源组。
    4. 单击“访问控制(IAM)”，并确认你（或你所属的组）至少对该资源组具有“参与者”访问权限。

如果收到 NoRegisteredProviderFound 错误或 MissingSubscriptionRegistration 错误，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](../azure-resource-manager/resource-manager-common-deployment-errors.md)。

## <a name="list-and-show-clusters"></a>列出并显示群集
1. 登录到 [https://portal.azure.com](https://portal.azure.com)。
2. 在左侧菜单中单击“HDInsight 群集”以列出现有群集。 如果看不到“HDInsight 群集”，请先单击“所有服务”。
3. 单击群集名称。 如果群集列表很长，可以使用页面顶部的筛选器。
4. 单击列表中的群集可查看概述页：

    ![Azure 门户 HDInsight 群集概要](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)概述菜单：
    * **仪表板**：打开群集的 Ambari Web UI。
    * **安全外壳**：显示使用安全 Shell (SSH) 连接与群集建立连接的说明。
    * **缩放群集**：可更改此群集的辅助角色节点数。
    * **移动**：将群集移至其他资源组或其他订阅。
    * **删除**：删除群集。

    **左侧菜单：**
    * **活动日志**：显示和查询活动日志。
    * **访问控制 (IAM)**：使用角色分配。  请参阅[使用角色分配管理对 Azure 订阅资源的访问权限](../active-directory/role-based-access-control-configure.md)。
    * **标记**：允许设置键/值对，以定义云服务的自定义分类。 例如，可以创建名为 **project** 的键，并对与特定项目关联的所有服务使用一个公用值。
    * **诊断并解决问题**：显示疑难解答信息。
    * 锁定：添加锁防止群集遭到修改或删除。
    * **自动化脚本**：为群集显示并导出 Azure 资源管理器模板。 当前，只能导出相关的 Azure 存储帐户。 请参阅[使用 Azure 资源管理器模板在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。
    * **快速入门**：显示可帮助你开始使用 HDInsight 的信息。
    * **适用于 HDInsight 的工具**：HDInsight 相关工具的帮助信息。
    * **订阅核心使用情况**：显示订阅的已使用和可用核心。
    * **缩放群集**：增加和减少群集辅助角色节点的数量。 请参阅[缩放群集](hdinsight-administer-use-management-portal.md#scale-clusters)。
    * **SSH + 群集登录**：显示使用安全外壳 (SSH) 连接与群集建立连接的说明。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。
    * **HDInsight 合作伙伴**：添加/删除当前的 HDInsight 合作伙伴。
    * **外部元存储**：查看 Hive 和 Oozie 元存储。 只能在群集创建过程中配置元存储。 请参阅[使用 Hive/Oozie 元存储](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore)。
    * **脚本操作**：在群集上运行 Bash 脚本。 请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。
    * **应用程序**：添加/删除 HDInsight 应用程序。  请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。
    * **监视**：监视 Azure Operations Management Suite 和 Azure Log Analytics 中的群集。
    * **属性**：查看群集属性。
    * **存储帐户**：查看存储帐户和密钥。 存储帐户在群集创建过程中配置。
    * **Data Lake Store 访问**：配置 Data Lake Store 的访问。  请参阅[使用 Azure 门户创建包含 Data Lake Store 的 HDInsight 群集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。
    * **资源运行状况**：请参阅 [Azure 资源运行状况概述](../service-health/resource-health-overview.md)。
    * **新的支持请求**：允许对 Microsoft 支持创建支持票证。
    
6. 单击“属性”：

    属性包括：

   * **主机名**：群集名称。
   * 群集 URL：Ambari Web 界面的 URL。
   * 安全外壳 (SSH)：用于通过 SSH 访问群集的用户名和主机名。
   * 状态：包括 Aborted、Accepted、ClusterStorageProvisioned、AzureVMConfiguration、HDInsightConfiguration、Operational、Running、Error、Deleting、Deleted、Timedout、DeleteQueued、DeleteTimedout、DeleteError、PatchQueued、CertRolloverQueued、ResizeQueued、ClusterCustomization。
   * **区域**：Azure 位置。 有关受支持的 Azure 位置的列表，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)中的“区域”下拉列表框。
   * 创建日期：部署群集的日期。
   * **操作系统**：**Windows** 或 **Linux**。
   * **类型**：Hadoop、HBase、Storm、Spark。
   * **版本**。 请参阅 [HDInsight 版本](hdinsight-component-versioning.md)。
   * **订阅**：订阅名称。
   * **默认数据源**：默认的群集文件系统。
   * 辅助角色节点大小：辅助角色节点的所选 VM 大小。
   * 头节点大小：头节点的所选 VM 大小。
   * 虚拟网络：群集将要部署到的虚拟网络和子网的名称（如果部署时选择）。

## <a name="delete-clusters"></a>删除群集
删除群集不会删除默认存储帐户或任何链接的存储帐户。 可以使用相同的存储帐户和相同的元存储来重新创建群集。 建议在重新创建群集时使用新的默认 Blob 容器。

1. 登录到[门户][azure-portal]。
2. 单击左侧菜单中的“HDInsight 群集”。 如果看不到“HDInsight 群集”，请先单击“所有服务”。
3. 单击要删除的群集。
4. 单击顶部菜单中的“删除”，并按照说明操作。

另请参阅[暂停/关闭群集](#pauseshut-down-clusters)。

## <a name="add-additional-storage-accounts"></a>添加其他存储帐户

创建群集后，可以添加其他 Azure 存储帐户和 Azure Data Lake Store 帐户。 有关详细信息，请参阅[将其他存储帐户添加到 HDInsight](./hdinsight-hadoop-add-storage.md)。

## <a name="scale-clusters"></a>缩放群集
使用群集缩放功能可更改 Azure HDInsight 群集使用的辅助角色节点数，而无需重新创建群集。

> [!NOTE]
> 只支持使用 HDInsight 3.1.3 或更高版本的群集。 如果不确定群集的版本，可以查看“属性”页。  请参阅[列出和显示群集](#list-and-show-clusters)。
>
>

对于 HDInsight 支持的每种类型的群集，更改数据节点数的影响有所不同：

* Hadoop

    可以顺利地增加正在运行的 Hadoop 群集中的辅助节点数，而不会影响任何挂起或运行中的作业。 还可以在操作进行中提交新作业。 系统会正常处理失败的缩放操作，让群集始终保持正常运行状态。

    减少数据节点数目以缩减 Hadoop 群集时，系统会重新启动群集中的某些服务。 此行为会导致所有正在运行和挂起的作业在缩放操作完成时失败。 但是，可以在操作完成后重新提交这些作业。
* HBase

    可以顺利地在 HBase 群集运行时对其添加或删除节点。 在完成缩放操作后的几分钟内，区域服务器就能自动平衡。 不过，也可以手动平衡区域服务器，方法是登录到群集的头节点，并在命令提示符窗口中运行以下命令：

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    有关使用 HBase shell 的详细信息，请参阅 [HDInsight 中的 Apache HBase 示例入门](hbase/apache-hbase-tutorial-get-started-linux.md)。

* Storm

    可以顺利地在 Storm 群集运行时对其添加或删除数据节点。 但是，在缩放操作成功完成后，需要重新平衡拓扑。

    可以使用两种方法来完成重新平衡操作：

  * Storm Web UI
  * 命令行界面 (CLI) 工具

    有关详细信息，请参阅 [Apache Storm 文档](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

    HDInsight 群集上提供了 Storm Web UI：

    ![HDInsight Storm 规模重新平衡](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    以下是用于重新平衡 Storm 拓扑的示例 CLI 命令：

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**缩放群集**

1. 登录到[门户][azure-portal]。
2. 单击左侧菜单中的“HDInsight 群集”。
3. 单击要缩放的群集。
3. 单击“缩放群集”。
4. 输入**辅助角色节点数**。 对群集节点数的限制因 Azure 订阅而异。 若要增大限制，可联系计费支持人员。  成本信息反映对节点数所做的更改。

    ![HDInsight Hadoop HBase Storm Spark 缩放](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>暂停/关闭群集

大多数 Hadoop 作业都为批处理作业，只是偶尔运行。 大多数 Hadoop 群集都存在长时间不进行处理的情况。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。
此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

可以通过许多方式对此过程进行程序性处理：

* 用户 Azure 数据工厂。 有关创建按需 HDInsight 链接服务的信息，请参阅[在 HDInsight 中使用 Azure 数据工厂创建基于 Linux 的按需 Hadoop 群集](hdinsight-hadoop-create-linux-clusters-adf.md)。
* 使用 Azure PowerShell。  请参阅[分析航班延误数据](hdinsight-analyze-flight-delay-data.md)。
* 使用 Azure CLI。 请参阅[使用 Azure CLI 管理 HDInsight 群集](hdinsight-administer-use-command-line.md)。
* 使用 HDInsight .NET SDK。 请参阅[提交 Hadoop 作业](hadoop/submit-apache-hadoop-jobs-programmatically.md)。

有关定价信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。 若要从门户中删除群集，请参阅[删除群集](#delete-clusters)

## <a name="move-cluster"></a>移动群集

可以将 HDInsight 群集移到另一个 Azure 资源组或另一个订阅。  请参阅[列出和显示群集](#list-and-show-clusters)。

## <a name="upgrade-clusters"></a>升级群集

请参阅[将 HDInsight 群集升级到更新的版本](./hdinsight-upgrade-cluster.md)。

## <a name="open-the-ambari-web-ui"></a>打开 Ambari Web UI

Ambari 提供由其 RESTful API 提供支持的直观、易用的 Hadoop 管理 Web UI。 Ambari 使系统管理员能够管理和监视 Hadoop 群集。

1. 从 Azure 门户打开 HDInsight 群集。  请参阅[列出和显示群集](#list-and-show-clusters)。
2. 单击“群集仪表板”。

    ![HDInsight Hadoop 群集菜单](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. 输入群集用户名和密码。  默认群集用户名为“admin”。Ambari Web UI 如下所示：

    ![HDInsight Hadoop Ambari Web UI](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

有关详细信息，请参阅[使用 Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)。

## <a name="change-passwords"></a>更改密码
HDInsight 群集可以有两个用户帐户。 HDInsight 群集用户帐户（即 HTTP 用户帐户）和 SSH 用户帐户是在创建过程中创建的。 可以使用 Ambari Web UI 更改群集用户帐户用户名和密码，使用脚本操作更改 SSH 用户帐户

### <a name="change-the-cluster-user-password"></a>更改群集用户密码
可以使用 Ambari Web UI 更改群集用户密码。 若要登录 Ambari，必须使用现有的群集用户名和密码。

> [!NOTE]
> 更改群集用户 (admin) 的密码可能会导致针对此群集运行的脚本操作失败。 如果有任何持久性脚本操作以工作节点为目标，则通过重设大小操作在群集中添加节点时，这些脚本可能会失败。 有关脚本操作的详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。
>
>

1. 使用 HDInsight 群集用户凭据登录到 Ambari Web UI。 默认的用户名为 **admin**。URL 为 **https://&lt;HDInsight Cluster Name>azurehdinsight.net**。
2. 在顶部菜单中单击“管理”，并单击“管理 Ambari”。
3. 在左侧菜单中，单击“用户”。
4. 单击“管理”。
5. 单击“更改密码”。

然后，Ambari 将更改群集中所有节点上的密码。

### <a name="change-the-ssh-user-password"></a>更改 SSH 用户密码
1. 使用文本编辑器将以下文本保存为名为“changepassword.sh”的文件。

    > [!IMPORTANT]
    > 所用的编辑器必须使用 LF 作为行尾。 如果编辑器使用 CRLF，则脚本将无法正常工作。

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. 将该文件上传到可以使用 HTTP 或 HTTPS 地址从 HDInsight 访问的存储位置。 例如，某个公共文件存储（如 OneDrive 或 Azure Blob 存储）。 将 URI（HTTP 或 HTTPS 地址）保存到文件中，因为下一步需要用到此 URI。
3. 从 Azure 门户中，单击“HDInsight 群集”。
4. 单击 HDInsight 群集。
4. 单击“脚本操作”。
4. 在“脚本操作”边栏选项卡中，选择“提交新项”。 出现“提交脚本操作”边栏选项卡时，请输入以下信息：

   | 字段 | 值 |
   | --- | --- |
   | 名称 |更改 ssh 密码 |
   | Bash 脚本 URI |changepassword.sh 文件的 URI |
   | 节点（头节点、辅助角色节点、Nimbus、监督程序、Zookeeper，等等。） |✓ 适用于所有列出的节点类型 |
   | parameters |输入 SSH 用户名和新密码。 用户名与密码之间应有一个空格。 |
   | 保留此脚本操作... |将此字段保留未选中状态。 |
5. 选择“创建”以应用脚本。 完成脚本后，可以使用新密码通过 SSH 连接到群集。

## <a name="grantrevoke-access"></a>授予/撤消访问权限
HDInsight 群集提供以下 HTTP Web 服务（所有这些服务都有 REST 样式的终结点）：

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

默认情况下，将授权这些服务进行访问。 可以使用 [Azure CLI](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) 和 [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access) 来吊销/授予访问权限。

## <a name="find-the-subscription-id"></a>查找订阅 ID

**查找 Azure 订阅 ID**

1. 登录到[门户][azure-portal]。
2. 单击“订阅”。 每个订阅都有一个名称和 ID。

每个群集都绑定到一个 Azure 订阅。 订阅 ID 显示在群集的“概要”磁贴上。 请参阅[列出和显示群集](#list-and-show-clusters)。

## <a name="find-the-resource-group"></a>查找资源组
在 Azure 资源管理器模式下，使用 Azure 资源管理器组创建每个 HDInsight 群集。 群集所属的 Resource Manager 组显示在以下位置：

* 群集列表包含“资源组”列。
* 群集“概要”磁贴。  

请参阅[列出和显示群集](#list-and-show-clusters)。

## <a name="find-the-storage-accounts"></a>查找存储帐户

HDInsight 群集使用 Azure 存储帐户或 Azure Data Lake Store 来存储数据。 每个 HDInsight 群集都可拥有一个默认存储帐户和多个链接的存储帐户。 若要列出存储帐户，请先从门户中打开群集，然后单击“存储帐户”：

![HDInsight 群集存储帐户](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

在上面的屏幕截图上，有一个“默认”列，指示帐户是否为默认存储帐户。

若要列出 Data Lake Store 帐户，请单击上面屏幕截图中的“Data Lake Store 访问”。

## <a name="run-hive-queries"></a>运行 Hive 查询
无法直接从 Azure 门户运行 Hive 作业，但可以使用 Ambari Web UI 上的 Hive 视图。

**使用 Ambari Hive 视图运行 Hive 查询**

1. 使用 HDInsight 群集用户凭据登录到 Ambari Web UI。 默认的用户名为 **admin**。URL 为 **https://&lt;HDInsight Cluster Name>azurehdinsight.net**。
2. 打开 Hive 视图，如以下屏幕截图中所示：  

    ![HDIinsight Hive 视图](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. 在顶部菜单中单击“查询”。
4. 在“查询编辑器”中输入 Hive 查询，并单击“执行”。

## <a name="monitor-jobs"></a>监视作业
请参阅[使用 Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md#monitoring)。

## <a name="browse-files"></a>浏览文件
使用 Azure 门户可以浏览默认容器的内容。

1. 登录到 [https://portal.azure.com](https://portal.azure.com)。
2. 在左侧菜单中单击“HDInsight 群集”以列出现有群集。
3. 单击群集名称。 如果群集列表很长，可以使用页面顶部的筛选器。
4. 单击群集左侧菜单中的“存储帐户”。
5. 单击存储帐户。
7. 单击“Blob”磁贴。
8. 单击默认容器名称。

## <a name="monitor-cluster-usage"></a>监视群集使用情况
HDInsight 群集边栏选项卡的“使用情况”部分会显示相关信息，方便了解订阅中可以用于 HDInsight 的核心数、分配给此群集的核心数，以及这些核心是如何分配给此群集中的节点的。 请参阅[列出和显示群集](#list-and-show-clusters)。

> [!IMPORTANT]
> 若要监视 HDInsight 群集提供的服务，必须使用 Ambari Web 或 Ambari REST API。 有关如何使用 Ambari 的详细信息，请参阅[使用 Ambari 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>连接到群集

* [将 Hive 与 HDInsight 配合使用](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [将 Pig 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>后续步骤

本文介绍了一些基本管理功能。 若要了解更多信息，请参阅下列文章：

* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)
* [创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)
* [深入了解如何使用 Ambari Web UI](hdinsight-hadoop-manage-ambari.md)
* [有关使用 Ambari REST API 的详细信息](hdinsight-hadoop-manage-ambari-rest-api.md)
* [在 HDInsight 中使用 Hive](hadoop/hdinsight-use-hive.md)
* [在 HDInsight 中使用 Pig](hadoop/hdinsight-use-pig.md)
* [在 HDInsight 中使用 Sqoop](hadoop/hdinsight-use-sqoop.md)
* [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight 包含哪个版本的 Hadoop？](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop 命令行"

---
title: "在 HDInsight 中创建 Hadoop 群集 | Microsoft Docs"
description: "了解如何使用 Azure 门户创建 Azure HDInsight 的群集。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d6680fb1e33b221a74d6c5db4cf5012d9109eac6


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>使用 Azure 门户在 HDInsight 中创建基于 Windows 的 Hadoop 群集
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

了解如何使用 Azure 门户在 HDInsight 中创建 Hadoop 群集。 Microsoft [Azure 门户](../azure-portal-overview.md)是一个中心位置，可在其中预配和管理 Azure 资源。 Azure 门户是可用于在 HDInsight 中创建基于 Linux 的或基于 Windows 的 Hadoop 群集的工具之一。 有关其他群集创建工具和功能的详细信息，请单击本页顶部相应的选项卡，或参阅[群集创建方法](hdinsight-provision-clusters.md#cluster-creation-methods)。

## <a name="prerequisites"></a>先决条件：
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

在开始按照本文中的说明操作之前，你必须具有以下内容：

* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

### <a name="access-control-requirements"></a>访问控制要求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>创建群集
**创建 HDInsight 群集**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 依次单击“新建”、“数据分析”、“HDInsight”。
   
    ![在 Azure 门户中创建新群集](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creating a new cluster in the Azure Portal")
3. 键入或选择以下值：
   
   * **群集名称**：为群集输入名称。 如果该群集名称可用，则名称旁边会出现绿色的复选标记。
   * **群集类型**：选择“Hadoop”。 其他选项包括 **HBase**、**Storm** 和 **Spark**。
     
     > [!IMPORTANT]
     > HDInsight 群集具有各种不同的类型，与该群集进行优化的工作负荷或技术相对应。 不支持在一个群集上创建合并了多个类型（如 Storm 和 HBase）的群集。
     > 
     > 
   * **群集操作系统**：选择“Windows”。 若要创建基于 Linux 的群集，请选择“Linux”。
   * **版本**：请参阅 [HDInsight 版本](hdinsight-component-versioning.md)。
   * **订阅**：选择将用于创建此群集的 Azure 订阅。
   * **资源组**：选择现有资源组或创建新资源组。 如果有可用的资源组，则此条目默认为现有资源组中的一个。
   * **凭据**：配置 Hadoop 用户（HTTP 用户）的用户名和密码。 如果你为该群集启用了远程桌面，则需要配置远程桌面用户的用户名、密码和帐户过期日期。 在底部单击“选择”以保存更改。
     
          ![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")
   * **数据源**：创建新的 Azure 存储帐户，或选择现有的帐户，使其用作群集的默认文件系统。
     
          ![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")
     
         * **选择方法**：将此项设置为“来自所有订阅”，以便能够浏览所有订阅中的存储帐户。 如果想要输入现有存储帐户的“存储名称”和“访问密钥”，请将此项设置为“访问密钥”。
         * **选择存储帐户/新建**：单击“选择存储帐户”，浏览并选择要与群集关联的现有存储帐户。 或者单击“新建”来创建新的存储帐户。 使用出现的字段输入存储帐户名称。 如果该名称可用，将出现绿色复选标记。
         * **选择默认容器**：使用此选项输入要用于该群集的默认容器名称。 尽管你可以输入任何名称，但我们建议使用与群集相同的名称，以方便辨别用于这个特定群集的容器。
         * **位置**：存储帐户所在的地理区域，或要在其中创建存储帐户的地理区域。 这个位置将确定群集位置。  该群集与默认存储帐户必须并存于相同的 Azure 数据中心。
   * **节点定价层**：设置该群集所需的辅助角色节点数。 该群集的预估成本将显示在边栏选项卡内。

        ![节点定价层边栏选项卡](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


    * “可选配置”用于选择群集的版本，以及配置其他可选设置，例如加入**虚拟网络**、设置**外部元存储**用于保存 Hive 和 Oozie 的数据、使用脚本操作来自定义要安装自定义组件的群集，或使用具有该群集的其他存储帐户。

    * **HDInsight 版本**：选择要用于该群集的版本。 有关详细信息，请参阅 [HDInsight 群集版本](hdinsight-component-versioning.md)。
    * **虚拟网络**：如果想要将群集放入虚拟网络，请选择 Azure 虚拟网络和子网。  

        ![虚拟网络边栏选项卡](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

        有关将 HDInsight 与虚拟网络配合使用的信息（包括虚拟网络的特定配置要求），请参阅[使用 Azure 虚拟网络扩展 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。



    * **外部元存储**：指定 Azure SQL 数据库用于存储与该群集关联的 Hive 和 Oozie 元数据。

        > [AZURE.NOTE] 元存储配置不可用于 HBase 群集类型。

    ![自定义元存储边栏选项卡](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")

    对于“为 Hive 使用现有的 SQL DB”元数据，请单击“是”，选择 SQL 数据库，然后提供该数据库的用户名/密码。 如果要“为 Oozie 元数据使用现有的 SQL DB”，请重复这些步骤。 单击“选择”，直到返回“可选配置”边栏选项卡。

    >[AZURE.NOTE] 用于元存储的 Azure SQL 数据库必须允许连接到其他 Azure 服务，包括 Azure HDInsight。 在 Azure SQL 数据库仪表板的右侧单击服务器名称。 这是运行 SQL 数据库实例的服务器。 进入服务器视图后，请单击“配置”，针对“Azure 服务”单击“是”，然后单击“保存”。

            &nbsp;

            > [AZURE.IMPORTANT] 创建元存储时，请勿使用包含短划线或连字符的数据库名称，因为这可能会导致群集创建过程失败。

          * **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.


            ![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


        * **Azure 存储密钥**：指定与该群集关联的其他存储帐户。 在“Azure 存储密钥”边栏选项卡中，单击“添加存储密钥”，然后选择现有的存储帐户或创建新的帐户。


            ![其他存储边栏选项卡](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


1. 单击“创建” 。 选择“固定到启动板”会将群集的磁贴添加到门户的启动板。 该图标指示正在创建群集，完成创建后，将改为显示 HDInsight 图标。
   
    创建群集需要一些时间，通常约 15 分钟左右。 使用启动板上的磁贴或页面左侧的“通知”条目检查预配进程。
2. 创建完成后，在启动板中单击群集磁贴，以启动群集边栏选项卡。 群集边栏选项卡提供有关该群集的基本信息，如名称、其所属的资源组、位置、操作系统、群集仪表板 URL 等。

    ![群集边栏选项卡](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Cluster properties")


    参考以下内容了解此边栏选项卡顶部和“概要”部分中的图标：


    * **设置**和**所有设置**：显示该群集的“设置”边栏选项卡，可让你访问该群集的详细配置信息。
    * **仪表板**、**群集仪表板**和 **URL**：这些是访问群集仪表板（即可在群集上运行作业的 Web 门户）的所有途径。
    * **远程桌面**：用于在群集节点上启用/禁用远程桌面。
    * **缩放群集**：可更改此群集的辅助角色节点数。
    * **删除**：删除 HDInsight 群集。
    * **快速启动**（![云和闪电图标 = 快速启动](./media/hdinsight-provision-clusters/quickstart.png)）：显示可帮助开始使用 HDInsight 的信息。
    * **用户**（![用户图标](./media/hdinsight-provision-clusters/users.png)）：用于设置 Azure 订阅上其他用户对此群集的_门户管理_权限。


        > [AZURE.IMPORTANT] 这_只会_影响在门户中对此群集的访问和权限，对于连接到 HDInsight 群集或将作业提交到其上的用户并没有作用。

    * **标记**（![标记图标](./media/hdinsight-provision-clusters/tags.png)）：标记用于设置键/值对，以定义云服务的自定义分类。 例如，可以创建名为 __project__ 的键，然后对与特定项目关联的所有服务使用一个公用值。

## <a name="customize-clusters"></a>自定义群集
* 请参阅[使用 Bootstrap 自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-bootstrap.md)。
* 请参阅[使用脚本操作自定义基于 Windows 的 HDInsight 群集](hdinsight-hadoop-customize-cluster.md)。

## <a name="next-steps"></a>后续步骤
在本文中，你已经学习了几种创建 HDInsight 群集的方法。 若要了解更多信息，请参阅下列文章：

* [Azure HDInsight 入门](hdinsight-hadoop-linux-tutorial-get-started.md) - 了解如何开始使用 HDInsight 群集
* [以编程方式提交 Hadoop 作业](hdinsight-submit-hadoop-jobs-programmatically.md) - 了解如何以编程方式将作业提交到 HDInsight
* [使用 Azure 门户管理 HDInsight 中的 Hadoop 群集](hdinsight-administer-use-management-portal.md)




<!--HONumber=Nov16_HO3-->



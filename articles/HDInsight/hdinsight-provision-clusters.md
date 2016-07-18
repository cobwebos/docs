<!-- not suitable for Mooncake -->

<properties
   pageTitle="在 HDInsight 中创建基于 Windows 的 Hadoop 群集 | Azure"
   description="了解如何创建 Azure HDInsight 的群集。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.date="06/09/2016"
	wacn.date=""/>

# 在 HDInsight 中创建基于 Windows 的 Hadoop 群集

[AZURE.INCLUDE [选择器](../includes/hdinsight-selector-create-clusters.md)]

Hadoop 群集由用于对群集中的任务进行分布式处理的多个虚拟机（节点）组成。Azure 对各个节点的安装和配置的实现细节进行抽象，因此你只需提供常规配置信息。在本文中，你会了解这些配置设置。

>[AZURE.NOTE] 本文档中的信息特定于基于 Windows 的 HDInsight 群集。有关基于 Linux 的群集的信息，请参阅 [Create Linux-based Hadoop clusters in HDInsight](/documentation/articles/hdinsight-provision-clusters-v1)（在 HDInsight 中创建基于 Linux 的 Hadoop 群集）。

##群集类型

当前，HDInsight 提供 4 种不同类型的群集，每种类型都具有一组用于提供特定功能的组件：

| 群集类型 | 在需要以下功能时使用此类型... |
| ------------ | ----------------------------- |
| Hadoop | 查询和分析（批处理作业） |
| HBase | NoSQL 数据存储 |
| Storm | 实时事件处理 |
| Spark（预览版） | 内存中处理、交互式查询、微批流处理 |

每种群集类型对于群集中的节点都具有自己的术语，以及针对每种节点类型的节点数和默认 VM 大小：

| 类型| 节点（节点数）| 图表|
|-----|------|--------|
|Hadoop| 头节点 (2)，数据节点 (1+)|![HDInsight Hadoop 群集节点](./media/hdinsight-provision-clusters-v1/HDInsight.Hadoop.roles.png)|
|HBase|头服务器 (2)，区域服务器 (1+)，主/Zookeeper 节点 (3)|![HDInsight HBase 群集节点](./media/hdinsight-provision-clusters-v1/HDInsight.HBase.roles.png)|
|Storm|Nimbus 节点 (2)，Supervisor 服务器 (1+)，Zookeeper 节点 (3)|![HDInsight Storm 群集节点](./media/hdinsight-provision-clusters-v1/HDInsight.Storm.roles.png)|
|Spark|头节点 (2)，工作节点 (1+)，Zookeeper 节点 (3)（对于 A1 Zookeeper VM 大小免费）|![HDInsight Spark 群集节点](./media/hdinsight-provision-clusters-v1/HDInsight.Spark.roles.png)|

* 括号中是针对每种节点类型的节点数。

> [AZURE.IMPORTANT] 如果你计划使用 32 个以上的工作节点（在创建群集时或是在创建之后通过扩展群集进行），则必须选择至少具有 8 个核心和 14GB ram 的头节点大小。

可以使用[脚本操作](#customize-clusters-using-script-action)向这些基本类型添加其他组件，如 Hue 或 R。

## 基本配置选项

以下是用于创建 HDInsight 群集的基本配置选项。

- **群集名称**

	群集名称用于标识群集。群集名称必须在全局是唯一的，并且必须遵循以下命名准则：

	- 字段必须是包含 3 到 63 个字符的字符串
	- 字段只能包含字母、数字和连字符。

- **群集类型**

    请参阅[群集类型](#cluster-types)。

- **操作系统**

	你可以在以下两个操作系统之一上创建 HDInsight 群集：
	- **Linux 上的 HDInsight (Ubuntu 12.04 LTS for Linux)**：HDInsight 提供在 Azure 上配置 Linux 群集的选项。如果你熟悉 Linux 或 Unix，要从现有的基于 Linux 的 Hadoop 解决方案进行迁移，或者想要轻松集成针对 Linux 构建的 Hadoop 生态系统组件，请配置 Linux 群集。有关详细信息，请参阅[在 HDInsight 中的 Linux 上开始使用 Hadoop](/documentation/articles/hdinsight-hadoop-tutorial-get-started-windows-v1)。
	- **Windows 上的 HDInsight (Windows Server 2012 R2 Datacenter)**：
    
- **HDInsight 版本**

	用于确定要用于此群集的 HDInsight 版本。有关详细信息，请参阅 [HDInsight 中的 Hadoop 群集版本和组件](/documentation/articles/hdinsight-component-versioning-v1/)。

- **订阅名称**

	每个 HDInsight 群集与一个 Azure 订阅绑定。
    
- **资源组名称**

	你可以使用 [Azure Resource Manager (ARM)](/documentation/articles/resource-group-overview) 以组（称为 Azure 资源组）的方式处理应用程序中的资源。你可以通过一个协调的操作为应用程序部署、更新、监视或删除所有资源。

- **凭据**

	HDInsight 群集允许你在群集创建期间配置两个用户帐户：

	- HTTP 用户。默认用户名是在 Azure 门户上使用基本配置创建的 admin。有时，它称为“群集用户”。
	- RDP 用户（Windows 群集）：用于通过 RDP 连接到群集。在创建帐户时，必须将过期日期设置为从当天算起的 90 天。
	- SSH 用户（Linux 群集）：用于通过 SSH 连接到群集。群集创建后，可以根据[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix)中的步骤创建更多 SSH 用户帐户。

- **数据源**

	原始 HDFS 使用群集上的多个本地磁盘。HDInsight 使用 Azure Blob 存储来存储数据。Azure Blob 存储是一种稳健、通用的存储解决方案，它与 HDInsight 无缝集成。通过 Hadoop 分布式的文件系统 (HDFS) 界面，可以针对 Blob 存储中的结构化或非结构化数据直接运行 HDInsight 中的整套组件。通过将数据存储在 Blob 存储中，你可以安全删除用于计算的 HDInsight 群集而不会丢失用户数据。

	在配置期间，你必须指定 Azure 存储帐户，并在该 Azure 存储帐户中指定 Azure Blob 存储容器。某些创建过程要求事先创建 Azure 存储帐户和 Blob 存储容器。群集使用该 Blob 存储容器作为默认存储位置。你也可以选择指定群集可访问的其他 Azure 存储帐户（链接的存储）。此外，群集还可以访问任何配置有完全公共读取权限或仅限对 blob 的公共读取权限的 Blob 容器。有关限制访问的详细信息，请参阅[管理对 Azure 存储资源的访问](/documentation/articles/storage-manage-access-to-resources)。

	![HDInsight 存储](./media/hdinsight-provision-clusters-v1/HDInsight.storage.png)

	>[AZURE.NOTE] Blob 存储容器提供一组 Blob 集，如图所示：

	![Azure Blob 存储](./media/hdinsight-provision-clusters-v1/Azure.blob.storage.jpg)

    建议不要使用默认 Blob 容器来存储业务数据。最好在每次使用之后删除默认 Blob 容器以降低存储成本。请注意，默认容器包含应用程序和系统日志。请确保在删除该容器之前检索日志。
    
	>[AZURE.WARNING] 不支持对多个群集共享一个 Blob 存储容器。

	有关使用辅助 Blob 存储的详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用](/documentation/articles/hdinsight-hadoop-use-blob-storage)。

    除了 Azure Blob 存储之外，还可以使用 [Azure 数据湖存储](/documentation/articles/data-lake-store-overview)作为 HDInsight 中的 HBase 群集的默认存储帐户以及所有 4 种 HDInsight 群集类型的链接存储。有关说明，请参阅 [Create an HDInsight cluster with Data Lake Store using Azure Portal](/documentation/articles/data-lake-store-hdinsight-hadoop-use-portal)（使用 Azure 门户创建包含数据湖存储的 HDInsight 群集）
    
- **位置（区域）**

	HDInsight 群集与其默认存储帐户必须位于相同的 Azure 位置。
	
	![Azure 区域](./media/hdinsight-provision-clusters-v1/Azure.regions.png)

	有关受支持区域的列表，请单击 [HDInsight 定价](/home/features/hdinsight/#price)中的“区域”下拉列表。

- **节点定价层**

    客户需根据群集的生存期，支付这些节点的使用费。创建群集之后便开始计费，删除群集时便停止计费（无法取消分配或保留群集）。

	不同群集类型具有不同的节点类型、节点数和节点大小。例如，Hadoop 群集类型具有两个头节点和四个数据节点（默认值），而 Storm 群集类型具有两个 nimbus 节点、三个 zookeeper 节点和四个 supervisor 节点（默认值）。HDInsight 群集的成本取决于节点数和节点的虚拟机大小。例如，如果你知道将执行需要大量内存的操作，则可能要选择具有更多内存的计算资源。为了方便学习，建议使用 1 个数据节点。有关 HDInsight 定价的详细信息，请参阅 [HDInsight 定价](/home/features/hdinsight/#price)。

	>[AZURE.NOTE] 群集大小限制因 Azure 订阅而异。若要提高限制的大小，请联系计费支持人员。
	
    >群集使用的节点不视为虚拟机，因为用于节点的虚拟机映像是 HDInsight 服务的实现细节；但是，节点使用的计算核心会针对可供订阅使用的计算核心总数进行计数。在创建 HDInsight 群集时，可以在“节点定价层”边栏选项卡的摘要部分中看到群集将使用的核心数以及可用核心数。

	在使用 Azure 门户配置群集时，节点大小可通过“节点定价层”边栏选项卡获得，还会显示与不同节点大小关联的成本。下面的屏幕截图显示用于基于 Linux 的 Hadoop 群集的选项：

	![hdinsight VM 节点大小](./media/hdinsight-provision-clusters-v1/hdinsight.node.sizes.png)

    下表显示 HDInsight 群集支持的大小和它们提供的容量。

    - 标准层：A 系列

        在经典部署模型中，某些 VM 大小在 PowerShell 和 CLI 中稍有不同。

        * Standard\_A3 是大型
        * Standard\_A4 是超大型

        <br>

        |大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|最大IOPS（每个磁盘 500 次）|
        |---|---|---|---|---|---|---|
        |Standard\_A3\\大型|4|7 GB|2|临时磁盘 = 285 GB |8|8x500|
        |Standard\_A4\\超大型|8|14 GB|4|临时磁盘 = 605 GB |16|16x500|
        |Standard\_A6|4|28 GB|2|临时磁盘 = 285 GB |8|8x500|
        |Standard\_A7|8|56 GB|4|临时磁盘 = 605 GB |16|16x500|


    - 标准层：D 系列

        |大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|最大IOPS（每个磁盘 500 次）|
        |---|---|---|---|---|---|---|
        |Standard\_D3 |4|14 GB|4|临时磁盘 (SSD) = 200 GB |8|8x500|
        |Standard\_D4 |8|28 GB|8|临时磁盘 (SSD) = 400 GB |16|16x500|
        |Standard\_D12 |4|28 GB|4|临时磁盘 (SSD) = 200 GB |8|8x500|
        |Standard\_D13 |8|56 GB|8|临时磁盘 (SSD) = 400 GB |16|16x500|
        |Standard\_D14 |16|112 GB|8|临时磁盘 (SSD) = 800 GB |32|32x500|

    - 标准层：Dv2 系列

        |大小 |CPU 核心数|内存|NIC 数（最大值）|最大磁盘大小|最大数据磁盘（每个 1023 GB）|最大IOPS（每个磁盘 500 次）|
        |---|---|---|---|---|---|---|
        |Standard\_D3\_v2 |4|14 GB|4|临时磁盘 (SSD) = 200 GB |8|8x500|
        |Standard\_D4\_v2 |8|28 GB|8|临时磁盘 (SSD) = 400 GB |16|16x500|
        |Standard\_D12\_v2 |4|28 GB|4|临时磁盘 (SSD) = 200 GB |8|8x500|
        |Standard\_D13\_v2 |8|56 GB|8|临时磁盘 (SSD) = 400 GB |16|16x500|
        |Standard\_D14\_v2 |16|112 GB|8|临时磁盘 (SSD) = 800 GB |32|32x500|    
 
    有关在计划使用这些资源时要考虑的部署注意事项，请参阅 [Sizes for virtual machines](/documentation/articles/virtual-machines-size-specs)（虚拟机的大小）。有关不同大小的定价信息，请参阅 [HDInsight Pricing](/home/features/hdinsight/#price)（HDInsight 定价）
    
	> [AZURE.IMPORTANT] 如果你计划使用 32 个以上的工作节点（在创建群集时或是在创建之后通过扩展群集进行），则必须选择至少具有 8 个核心和 14GB RAM 的头节点大小。计费在创建群集之后便会开始，仅当删除群集时才会停止。有关定价的详细信息，请参阅 [HDInsight pricing details](/home/features/hdinsight/#price)（HDInsight 定价详细信息）。


## 使用其他存储

在某些情况下，可能要向群集添加其他存储。例如，如果你有多个 Azure 存储空间帐户用于不同地理区域或不同服务，但是要使用 HDInsight 对所有这些帐户进行分析。

有关使用辅助 Blob 存储的详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用](/documentation/articles/hdinsight-hadoop-use-blob-storage)。有关使用辅助数据湖存储的详细信息，请参阅 [Create HDInsight clusters with Data Lake Store using Azure Portal](/documentation/articles/data-lake-store-hdinsight-hadoop-use-portal)（使用 Azure 门户创建包含数据湖存储的 HDInsight 群集）


## 使用 Hive/Oozie 元存储

如果希望在删除 HDInsight 群集后保留 Hive 表，我们强烈建议你使用自定义元存储，以用于在以后将该元存储附加到另一个 HDInsight 群集上。

元存储包含 Hive 和 Oozie 元数据，例如 Hive 表、分区、架构和列。使用元存储有助于保留你的 Hive 和 Oozie 元数据，这样，在创建新群集时，你不需要重新创建 Hive 表或 Oozie 作业。默认情况下，Hive 使用嵌入的 Azure SQL 数据库存储此信息。在删除群集时，嵌入的数据库无法保留元数据。例如，你使用 Hive 元存储创建了一个群集。你创建了一些 Hive 表。在删除群集并通过相同的 Hive 元存储重建群集之后，便可以看到在原始群集中创建的 Hive 表。

> [AZURE.NOTE] 元存储配置不可用于 HBase 群集类型。

## 使用 Azure 虚拟网络

[Azure 虚拟网络](/documentation/services/networking/)允许你创建包含需要用于解决方案的资源的安全永久性网络。通过虚拟网络，你可以：

* 在专用网络（仅限云）中将云资源连接在一起。

	![仅限云配置示意图](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-cloud-only.png)

* 通过使用虚拟专用网络 (VPN) 将云资源连接到本地数据中心网络（站点到站点或点到站点）。

    | 站点到站点配置 | 点到站点配置 |
    | -------------------------- | --------------------------- |
    | 利用站点到站点配置，你可以通过使用硬件 VPN 或路由和远程访问服务将多个资源从数据中心连接到 Azure 虚拟网络。<br />![站点到站点配置示意图](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-site-to-site.png) | 利用点到站点配置，你可以通过使用软件 VPN 将特定资源连接到 Azure 虚拟网络。<br />![点到站点配置示意图](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-point-to-site.png) |

基于 Windows 的群集需要 v1（经典）虚拟网络，而基于 Linux 的群集需要 v2 (Azure Resource Manager) 虚拟网络。如果没有正确的网络类型，创建群集时它将不能使用。

有关将 HDInsight 与虚拟网络配合使用的详细信息（包括虚拟网络的特定配置要求），请参阅 [Extend HDInsight capabilities by using an Azure Virtual Network](/documentation/articles/hdinsight-extend-hadoop-virtual-network)（使用 Azure 虚拟网络扩展 HDInsight 功能）。

## 使用 HDInsight 群集自定义功能来自定义群集 (bootstrap)

有时，你可能需要配置配置文件：

- clusterIdentity.xml
- core-site.xml
- gateway.xml
- hbase-env.xml
- hbase-site.xml
- hdfs-site.xml
- hive-env.xml
- hive-site.xml
- mapred-site
- oozie-site.xml
- oozie-env.xml
- storm-site.xml
- tez-site.xml
- webhcat-site.xml
- yarn-site.xml

若要在群集生存期保留更改，你可以在创建过程中使用 HDInsight 群集自定义，或者可以放心地在基于 Linux 的群集中使用 Ambari。有关详细信息，请参阅 [Customize HDInsight clusters using Bootstrap](/documentation/articles/hdinsight-hadoop-customize-cluster-bootstrap)（使用 Bootstrap 自定义 HDInsight 群集）。

>[AZURE.NOTE] 基于 Windows 的群集无法保留重新制作映像所造成的更改。有关详细信息，请参阅[重新启动角色实例进行 OS 升级](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)。若要在群集生存期保留更改，必须在创建过程中使用 HDInsight 群集自定义。

## 使用脚本操作自定义群集

你可以在创建期间通过使用脚本安装其他组件或自定义群集配置。此类脚本可通过**脚本操作**调用，脚本操作是一种配置选项，可通过门户、HDInsight Windows PowerShell cmdlet 或 HDInsight .NET SDK 使用。有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](/documentation/articles/hdinsight-hadoop-customize-cluster-v1)。



## 群集创建方法

在本文中，你了解了有关创建基于 Windows 的 HDInsight 群集的基本信息。使用下表可查找有关如何使用最适合你需求的方法来创建群集的特定信息：

| 使用此方法创建群集... | 使用 Web 浏览器... | 使用命令行 | 使用 REST API | 使用 SDK | 通过 Linux、Mac OS X 或 Unix | 通过 Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure 门户](/documentation/articles/hdinsight-hadoop-create-windows-clusters-portal) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure CLI](/documentation/articles/hdinsight-hadoop-create-windows-clusters-cli) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](/documentation/articles/hdinsight-hadoop-create-windows-clusters-powershell) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [cURL](/documentation/articles/hdinsight-hadoop-create-linux-clusters-curl-rest) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](/documentation/articles/hdinsight-hadoop-create-windows-clusters-dotnet-sdk) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [ARM 模板](/documentation/articles/hdinsight-hadoop-create-windows-clusters-arm-templates) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |



<!---HONumber=Mooncake_0711_2016-->
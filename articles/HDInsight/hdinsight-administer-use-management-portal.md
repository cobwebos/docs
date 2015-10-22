<properties
	pageTitle="使用 Azure 预览门户管理 HDInsight 中的 Hadoop 群集 | Azure"
	description="了解如何管理 HDInsight 服务。创建 HDInsight 群集，打开交互式 JavaScript 控制台，然后打开 Hadoop 命令控制台。"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015"
	wacn.date="" 
	ms.author="jgao"/>

#使用 Azure 门户管理 HDInsight 中的 Hadoop 群集

##概述
使用 Azure 门户，可以设置 Azure HDInsight 中的 Hadoop 集群、更改 Hadoop 用户密码，以及启用远程桌面协议 (RDP) 以访问群集上的 Hadoop 命令控制台。除了 Azure 门户外，还有其他可用于管理 HDInsight 的工具。


* [使用 Azure 门户管理 HDInsight 中的 Hadoop 群集](/documentation/articles/hdinsight-administer-use-management-portal-v1)

> [AZURE.NOTE]本文档中的步骤特定于使用基于 Windows 的 Hadoop 群集。有关使用基于 Linux 的群集的信息，请参阅[使用 Azure 预览门户管理 HDInsight 中的 Hadoop 群集](/documentation/articles/hdinsight-administer-use-portal-linux)



> [AZURE.NOTE]除了预览门户外，还有其他可用于管理 HDInsight 的工具。
>
> - [使用 Azure PowerShell 管理 HDInsight](/documentation/articles/hdinsight-administer-use-powershell)
> - [使用 Azure CLI 管理 HDInsight](/documentation/articles/hdinsight-administer-use-command-line)

**先决条件**

在开始阅读本文前，你必须具有：

- **Azure 订阅** - Azure 是基于订阅的平台。有关获取订阅的信息，请参阅[购买选项](/pricing/purchase-options/)和[试用](/pricing/1rmb-trial/)。
- **Azure 存储帐户** - HDInsight 群集使用 Azure Blob 存储容器作为默认文件系统。有关 Azure Blob 存储如何提供与 HDInsight 群集的无缝体验的详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用](/documentation/articles/hdinsight-use-blob-storage)。有关创建 Azure 存储帐户的详细信息，请参阅[如何创建存储帐户](/documentation/articles/storage-create-storage-account)。

##设置 HDInsight 群集

有关使用预览门户的预配说明，请参阅[预配 HDInsight 群集](/documentation/articles/hdinsight-provision-clusters#portal)。

##自定义 HDInsight 群集预配

HDInsight 使用各种 Hadoop 组件。有关已获得验证和支持的组件的列表，请参阅 [Azure HDInsight 包含哪个版本的 Hadoop？](/documentation/articles/hdinsight-component-versioning)。可使用以下选项之一自定义 HDInsight：

- 使用脚本操作来运行可以自定义群集的自定义脚本，以更改群集配置或安装 Giraph 或 Solr 等自定义组件。有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](/documentation/articles/hdinsight-hadoop-customize-cluster)。
- 在群集设置期间使用 HDInsight .NET SDK 或 Azure PowerShell 中的群集自定义参数。这样，这些配置更改将在群集的整个生存期内保留，并且不受 Azure 平台在维护时定期执行的群集节点重置映像影响。有关使用群集自定义参数的详细信息，请参阅[预配 HDInsight 群集](/documentation/articles/hdinsight-provision-clusters)。
- 一些本机 Java 组件（如 Mahout 和 Cascading）可以在群集上作为 JAR 文件运行。可以通过 Hadoop 作业提交机制将这些 JAR 文件分发到 Azure Blob 存储，并提交到 HDInsight 群集。有关详细信息，请参阅[以编程方式提交 Hadoop 作业](/documentation/articles/hdinsight-submit-hadoop-jobs-programmatically)。 


	>[AZURE.NOTE]如果你在将 JAR 文件部署到 HDInsight 群集或调用 HDInsight 群集上的 JAR 文件时遇到问题，请联系 [Microsoft 技术支持](/support/contact/)。

	> Cascading 不受 HDInsight 支持，因此不符合 Microsoft 技术支持的条件。有关支持的组件的列表，请参阅 [HDInsight 提供的群集版本有哪些新功能？](/documentation/articles/hdinsight-component-versioning)。


不支持使用远程桌面连接在群集上安装自定义软件。你应该避免在头节点的驱动器上存储任何文件，因为如果你需要重新创建群集，这些文件会丢失。建议你在 Azure Blob 存储中存储文件。Blob 存储是持久性的。

##更改 HDInsight 群集用户名和密码
HDInsight 群集可以有两个用户帐户。HDInsight 群集用户帐户是在设置过程中创建的。你还可以创建通过 RDP 访问群集的 RDP 用户帐户。请参阅[启用远程桌面](#connect-to-hdinsight-clusters-by-using-rdp)。

**更改 HDInsight 群集用户名和密码**

1. 登录到 [Azure 门户](https://manage.windowsazure.cn/)。
2. 单击左窗格中的“HDINSIGHT”。这将显示已部署的 HDInsight 群集的列表。
3. 单击要重设用户名和密码的 HDInsight 群集。
4. 在页面顶部，单击“配置”。
5. 单击“HADOOP 服务”旁边的“关闭”。
6. 单击页面底部的“保存”，然后等待禁用操作完成。
7. 在该服务已禁用后，单击“HADOOP 服务”旁边的“启用”。
8. 对于“用户名”和“新密码”，分别输入群集的新用户名和密码。
8. 单击“保存”。


##使用 RDP 连接到 HDInsight 群集

通过你在创建群集时提供的凭据，可以访问群集上的服务，但无法通过远程桌面访问群集本身。远程桌面访问默认情况下处于关闭状态，因此，使用它来直接访问群集时，要求一些其他的创建后配置。

**启用远程桌面**

1. 登录到 [Azure 门户](https://manage.windowsazure.cn/)。
2. 单击左窗格中的“HDINSIGHT”。这将显示已部署的 HDInsight 群集的列表。
3. 单击你要连接到的 HDInsight 群集。
4. 在页面顶部，单击“配置”。
5. 在页面底部，单击“启用远程”。
6. 在“配置远程桌面”向导中，输入远程桌面的用户名和密码。注意，此用户名必须不同于用来创建群集的用户名（默认情况下，指使用“快速创建”选项时的“admin”）。在“到期日期”框中输入到期日期。注意，到期日期必须为将来日期，但不超过从现在开始的一周。此日期的到期时间默认为指定日期的午夜。然后，单击复选图标。

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]


> [AZURE.NOTE]也可以使用 HDInsight .NET SDK 在群集上启用远程桌面。按以下方式使用 HDInsight 客户端对象上的 **EnableRdp** 方法：**client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**。同样，若要在群集上禁用远程桌面，可以使用 **client.DisableRdp(clustername, location)**。有关这些方法的详细信息，请参阅 [HDInsight .NET SDK 参考](https://msdn.microsoft.com/zh-cn/library/azure/dn469975.aspx)。这仅适用于在 Windows 上运行的 HDInsight 群集。
 


> [AZURE.NOTE]为群集启用 RDP 后，必须刷新页面，然后才能连接到群集。
 
**使用 RDP 连接到群集**

1. 登录到 [Azure 门户](https://manage.windowsazure.cn/)。
2. 单击左窗格中的“HDINSIGHT”。这将显示已部署的 HDInsight 群集的列表。
3. 单击你要连接到的 HDInsight 群集。
4. 在页面顶部，单击“配置”。
5. 单击“连接”，然后按照说明进行操作。

##创建自签名证书

如果你要使用 .NET SDK 在群集上执行任何操作，必须在工作站上创建自签名证书，并将该证书上载到你的 Azure 订阅。这是一次性的任务。只要证书有效，你就可以将它安装在其他计算机上。

**创建自签名证书**

1. 创建用来对请求进行身份验证的自签名证书。可以使用 Internet Information Services (IIS) 或 [makecert](http://go.microsoft.com/fwlink/?LinkId=534000) 创建该证书。
 
2. 浏览到证书所在的位置，右键单击该证书，单击“安装证书”，然后将证书安装到计算机的个人存储。编辑证书属性，以便为证书指定一个友好名称。

3. 将证书导入 Azure 门户。在门户中，单击页面左下角的“设置”，然后单击“管理证书”。在页面底部，单击“上载”，然后根据说明上载你在前一步骤中创建的 .cer 文件。

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##授予/撤消 HTTP 服务访问权限

HDInsight 群集提供以下 HTTP Web 服务（所有这些服务都有 REST 样式的终结点）：

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

默认情况下，将授权这些服务进行访问。你可以从 Azure 门户撤消/授予访问权限。

>[AZURE.NOTE]授予/撤消访问权限时，你将重设群集用户的用户名和密码。

**授予/撤消 HTTP Web 服务访问权限**

1. 登录到 [Azure 门户](https://manage.windowsazure.cn/)。
2. 单击左窗格中的“HDINSIGHT”。这将显示已部署的 HDInsight 群集的列表。
3. 单击要配置的 HDInsight 群集。
4. 在页面顶部，单击“配置”。
5. 单击“HADOOP 服务”旁边的“启用”或“关闭”。  
6. 对于“用户名”和“新密码”，分别输入群集的新用户名和密码。
7. 单击“保存”。

也可以通过 Azure PowerShell cmdlet 来完成此操作：

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

请参阅[使用 Azure PowerShell 管理 HDInsight](/documentation/articles/hdinsight-administer-use-powershell)。

##更改 HDInsight 群集用户名和密码
HDInsight 群集可以有两个用户帐户。HDInsight 群集用户帐户是在设置过程中创建的。你还可以创建通过 RDP 访问群集的 RDP 用户帐户。请参阅[启用远程桌面](#connect-to-hdinsight-clusters-by-using-rdp)。

**更改 HDInsight 群集用户名和密码**

1. 登录到 [预览门户][azure-portal]。
2. 从左侧菜单中依次单击“浏览全部”、“HDInsight 群集”和你的群集名称。
3. 从顶部菜单中单击“设置”，然后单击“群集登录”。
4. 更改**群集登录名**和/或**群集登录密码**，然后单击“保存”。

	![hdinsight 更改群集用户的用户名密码 http 用户](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##缩放群集
群集缩放功能可让你更改 Azure HDInsight 中运行的群集使用的辅助节点数，而无需重新创建群集。

>[AZURE.NOTE]只支持使用 HDInsight 3.1.3 或更高版本的群集。如果你不确定群集的版本，可以查看“属性”页。请参阅[熟悉群集门户界面](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface)。

更改 HDInsight 支持的每种类型的群集所用数据节点数的影响：

- Hadoop

	你可以顺利地增加正在运行的 Hadoop 群集中的辅助节点数，而不会影响任何挂起或运行中的作业。你还可以在操作进行中提交新作业。系统会正常处理失败的缩放操作，让群集始终保持正常运行状态。

	减少数据节点数目以缩减 Hadoop 群集时，系统会重新启动群集中的某些服务。这会导致所有正在运行和挂起的作业在缩放操作完成时失败。但是，你可以在操作完成后重新提交这些作业。

- HBase

	你可以顺利地在 HBase 群集运行时对其添加或删除节点。在完成缩放操作后的几分钟内，区域服务器就能自动平衡。不过，你也可以手动平衡区域服务器，方法是登录到群集的头节点，然后在命令提示符窗口中运行以下命令：

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	有关使用 HBase shell 的详细信息，请参阅
- Storm

	你可以顺利地在 Storm 群集运行时对其添加或删除数据节点。但是，在缩放操作成功完成后，你需要重新平衡拓扑。

	可以使用两种方法来完成重新平衡操作：

	* Storm Web UI
	* 命令行界面 (CLI) 工具

	有关详细信息，请参阅 [Apache Storm 文档](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

	HDInsight 群集上提供了 Storm Web UI：

	![hdinsight storm 缩放重新平衡](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	以下是有关如何使用 CLI 命令重新平衡 Storm 拓扑的示例：

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**缩放群集**

1. 登录到 [预览门户][azure-portal]。
2. 从左侧菜单中依次单击“浏览全部”、“HDInsight 群集”和你的群集名称。
3. 从顶部菜单中单击“设置”，然后单击“缩放群集”。
4. 输入**辅助节点数**。对群集节点数的限制不会因 Azure 订阅而有所不同。若要增大限制，可联系计费支持人员。成本信息将反映对节点数所做的更改。


	![hdinsight hadoop hbase storm spark 缩放](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)



##使用 RDP 连接到 HDInsight 群集

通过你在创建群集时提供的凭据，可以访问群集上的服务，但无法通过远程桌面访问群集本身。在预配群集时或在预配群集后，你可以启用“远程桌面访问”。有关在预配时启用远程桌面的说明，请参阅[预配 HDInsight 群集](/documentation/articles/hdinsight-provision-clusters)。

**启用远程桌面**

1. 登录到 [预览门户][azure-portal]。
2. 从左侧菜单中依次单击“浏览全部”、“HDInsight 群集”和你的群集名称。
3. 从顶部菜单中单击“设置”，然后单击“远程桌面”。
4. 输入**到期日期**、**远程桌面用户名**和**远程桌面密码**，然后单击“启用”。

	![hdinsight 启用禁用配置远程桌面](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	“到期日期”的默认值是“一周”。
> [AZURE.NOTE]也可以使用 HDInsight .NET SDK 在群集上启用远程桌面。按以下方式使用 HDInsight 客户端对象上的 **EnableRdp** 方法：**client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**。同样，若要在群集上禁用远程桌面，可以使用 **client.DisableRdp(clustername, location)**。有关这些方法的详细信息，请参阅 [HDInsight .NET SDK 参考](http://go.microsoft.com/fwlink/?LinkId=529017)。这仅适用于在 Windows 上运行的 HDInsight 群集。

**使用 RDP 连接到群集**

1. 登录到 [预览门户][azure-portal]。
2. 从左侧菜单中依次单击“浏览全部”、“HDInsight 群集”和你的群集名称。
3. 从顶部菜单中单击“设置”，然后单击“远程桌面”。
4. 单击“连接”，然后按照说明进行操作。如果“连接”为禁用，必须首先启用它。请确保使用远程桌面用户的用户名和密码。不能使用群集用户凭据。

##打开 Hadoop 命令行

若要使用远程桌面连接到群集并使用 Hadoop 命令行，首先必须对群集启用远程桌面访问，如上一部分中所述。

**打开 Hadoop 命令行**

1. 使用“远程桌面”连接到群集。
8. 从桌面上双击“Hadoop 命令行”。
		
	![HDI.HadoopCommandLine][image-hadoopcommandline]


	有关 Hadoop 命令的详细信息，请参阅 [Hadoop 命令参考](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html)。

在上面的屏幕快照中，文件夹名称嵌入了 Hadoop 版本号。版本号可以根据群集上安装的 Hadoop 组件的版本而更改。可以使用 Hadoop 环境变量来引用这些文件夹。例如：

	cd %hadoop_home%
	cd %hive_home%
	cd %hbase_home%
	cd %pig_home%
	cd %sqoop_home%   
	cd %hcatalog_home%


##后续步骤
在本文中，你学习了如何使用 Azure 门户创建 HDInsight 群集以及如何打开 Hadoop 命令行工具。若要了解更多信息，请参阅下列文章：

* [使用 Azure PowerShell 管理 HDInsight](/documentation/articles/hdinsight-administer-use-powershell)
* [使用跨平台命令行界面管理 HDInsight](/documentation/articles/hdinsight-administer-use-command-line)
* [设置 HDInsight 群集](/documentation/articles/hdinsight-provision-clusters)
* [以编程方式提交 Hadoop 作业](/documentation/articles/hdinsight-submit-hadoop-jobs-programmatically)
* [Azure HDInsight 入门](/documentation/articles/hdinsight-get-started)
* [Azure HDInsight 包含哪个版本的 Hadoop？](/documentation/articles/hdinsight-component-versioning)

[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "群集登录页"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop 命令行"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!---HONumber=74-->
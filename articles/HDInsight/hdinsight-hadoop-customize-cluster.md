<properties
	pageTitle="使用脚本操作自定义 HDInsight 群集 | Microsoft Azure"
	description="了解如何使用脚本操作自定义 HDInsight 群集。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.date="10/02/2015"
	wacn.date=""/>

# 使用脚本操作自定义 HDInsight 群集 (Windows)

HDInsight 提供了一个称为“脚本操作”的配置选项，该脚本操作可调用自定义脚本，以定义创建过程中要在群集上执行的自定义。这些脚本可用于在群集上安装额外的软件或更改群集上的应用程序配置。

<!-- deleted by customization

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../includes/hdinsight-azure-preview-portal.md)]
-->

* [使用脚本操作自定义 HDInsight 群集](/documentation/articles/hdinsight-hadoop-customize-cluster-v1)

<!-- deleted by customization

> [AZURE.NOTE] The information in this article is specific to Windows-based HDInsight clusters. For a version of this article that is specific to Linux-based clusters, see [Customize HDInsight clusters using Script Action (Linux)](/documentation/articles/hdinsight-hadoop-customize-cluster)
-->

你也可以使用多种其他方法来自定义 HDInsight 群集，例如包含其他存储帐户、更改 hadoop 配置文件（core-site.xml、hive-site.xml 等），或者将共享库（例如 Hive、Oozie）添加到群集中的共同位置。这些自定义可以通过使用 Azure PowerShell、Azure HDInsight .NET SDK 或 Azure 管理门户来完成。有关详细信息，请参阅[在 HDInsight 中创建 Hadoop 群集][hdinsight-provision-cluster]。
<!-- deleted by customization Preview portal -->
<!-- keep by customization: begin -->
<!-- keep by customization: end -->

## 群集创建过程中的脚本操作

只能在创建群集过程中使用脚本操作。下图演示了在创建过程中执行脚本操作的时间：

![群集创建过程中的 HDInsight 群集自定义和阶段][img-hdi-cluster-states]

当脚本运行时，群集进入 **ClusterCustomization** 阶段。在此阶段，脚本在系统管理员帐户下，以并行方式在群集中所有指定的节点上运行，而在节点上提供完全的系统管理员权限。

> [AZURE.NOTE]因为你在 **ClusterCustomization** 阶段中于群集节点上拥有系统管理员权限，所以你可以使用脚本来运行作业，例如停止和启动服务，包括 Hadoop 相关服务。因此，在脚本中，你必须在脚本完成运行之前，确定 Ambari 服务及其他 Hadoop 相关服务已启动并且正在运行。这些服务必须在群集创建时，成功地确定群集的运行状况和状态。如果你更改群集上的任何影响这些服务的配置，必须使用所提供的帮助器函数。有关帮助器函数的详细信息，请参阅[为 HDInsight 开发脚本操作脚本][hdinsight-write-script]。

脚本的输出以及错误日志文件存储在你为群集指定的默认存储帐户中。这些日志存储在名为 **u<\\cluster-name-fragment><\\time-stamp>setuplog** 的表中。这是从群集中所有节点上（头节点和辅助节点）运行的脚本聚合的日志文件。

每个群集可接受多个脚本操作，这些脚本依其指定顺序被调用。脚本可在头节点和/或工作线程节点上运行。

HDInsight 提供了多个脚本用于在 HDInsight 群集上安装以下组件：

Name | 脚本
----- | -----
**安装 R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1。 请参阅[在 HDInsight 群集上安装并使用 R][hdinsight-install-r]。
**安装 Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1。 请参阅[在 HDInsight 群集上安装并使用 Solr](/documentation/articles/hdinsight-hadoop-solr-install)。
**安装 Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1。 请参阅[在 HDInsight 群集上安装并使用 Giraph](/documentation/articles/hdinsight-hadoop-giraph-install)。

<!-- deleted by customization
**Install Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. See [Install and use Spark on HDInsight clusters][hdinsight-install-spark].
-->


<!-- deleted by customization
## Call scripts using the Azure Preview Portal

**From the Azure Preview portal**

1. Start creating a cluster as described at [Create Hadoop clusters in HDInsight](/documentation/articles/hdinsight-provision-clusters#portal).
2. Under Optional Configuration, for the **Script Actions** blade, click **add script action** to provide details about the script action, as shown below:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Use Script Action to customize a cluster")

-->
<!-- keep by customization: begin -->
## 使用 Azure 管理门户调用脚本

**通过 Azure 管理门户** 
1.根据[使用自定义选项设置群集](/documentation/articles/hdinsight-provision-clusters#portal)中的说明，使用“自定义创建”选项开始设置群集。
2.在向导的“脚本操作”页上，单击“添加脚本操作”，以提供有关脚本操作的详细信息，如下所示：

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
<!-- keep by customization: end -->
	<table border='1'>
		<tr><th>Property</th><th>Value</th></tr>
		<tr><td>Name</td>
			<td>Specify a name for the script action.</td></tr>
		<tr><td>Script URI</td>
			<td>Specify the URI to the script that is invoked to customize the cluster. s</td></tr>
		<tr><td>Head/Worker</td>
			<td>Specify the nodes (**Head** or **Worker**) on which the customization script is run.</b>.
		<tr><td>Parameters</td>
			<td>Specify the parameters, if required by the script.</td></tr>
	</table>

	Press ENTER to add more than one script action to install multiple components on the cluster.

3. 单击“选择”可保存脚本操作配置并继续执行群集创建。

<!-- deleted by customization

## Call scripts using Azure PowerShell

This following PowerShell script demonstrates how to install Spark on Windows based HDInsight cluster.  To install other software, you will need to replace the script file in the script:

In this section, we use the **<a href = "http://msdn.microsoft.com/zh-cn/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet to invoke scripts by using Script Action to customize a cluster. Before proceeding, make sure you have installed and configured Azure PowerShell. For information on configuring a workstation to run Azure PowerShell cmdlets for HDInsight, see [Install and configure Azure PowerShell][powershell-install-configure].

Perform the following steps:

1. Open an Azure PowerShell window and declare the following variables:

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Specify the configuration values such as nodes in the cluster and the default storage to be used.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.chinacloudapi.cn"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. Use the **Add-AzureHDInsightScriptAction** cmdlet to add a script action to cluster configuration. Later, when the cluster is being created, the script action gets executed.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	**Add-AzureHDInsightScriptAction** cmdlet takes the following parameters:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parameter</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definition</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">The configuration object to which script action information is added.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name of the script action.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Specifies the nodes on which the customization script is run. The valid values are HeadNode (to install on the head node) or DataNode (to install on all the data nodes). You can use either or both values.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Specifies the URI to the script that is executed.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parameters</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parameters required by the script. The sample script used in this topic does not require any parameters, and hence you do not see this parameter in the snippet above.
	</td></tr>
	</table>

4. Finally, start creating a customized cluster with Spark installed.  

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

When prompted, enter the credentials for the cluster. It can take several minutes before the cluster is created.

## Call scripts using .NET SDK 

The following sample demonstrates how to install Spark on Windows based HDInsight cluster. To install other software, you will need to replace the script file in the code.

**To create a HDInsight cluster with Spark** 

1. Create a C# console application in Visual Studio.
2. From the Nuget Package Manager Console, run the following command.

		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Common.Authentication -Pre

2. Use the following using statements in the Program.cs file:

		using System;
		using System.Security;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;

3. Place the code in the class with the following:

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<YourAzureSubscriptionID>");
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumNodes = <NumberOfClusterNodes>;
        private const string NewClusterLocation = "China East";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();

        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }


        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }

4. Press **F5** to run the application.


-->
<!-- keep by customization: begin -->
**从 Azure PowerShell cmdlet**

使用 Azure PowerShell 命令来运行单个脚本操作或多个脚本操作。你可以使用 **<a href = "http://msdn.microsoft.com/zh-cn/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet 调用自定义脚本。若要使用这些 cmdlet，你必须已安装并设置 Azure PowerShell。有关配置工作站以运行适用于 HDInsight 的 Azure Powershell cmdlet 的信息，请参阅[安装和配置 Azure PowerShell][powershell-install-configure]。

使用以下 Azure PowerShell 命令可以在部署 HDInsight 群集时运行单个脚本操作：

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName –Uri http://uri.to/scriptaction.ps1 –Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

使用以下 Azure PowerShell 命令可以在部署 HDInsight 群集时运行多个脚本操作：

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName1 –Uri http://uri.to/scriptaction1.ps1 –Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName2 –Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**从 HDInsight .NET SDK**

HDInsight .NET SDK 提供了 <a href="http://msdn.microsoft.com/zh-cn/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> 类用于调用自定义脚本。使用 HDInsight .NET SDK：

1. 创建一个 Visual Studio 应用程序，然后从 Nuget 安装 SDK。在“工具”菜单中，单击“Nuget Package Manager”，然后单击“Package Manager Console”。在控制台中运行下列命令以安装程序包：

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. 使用 SDK 创建群集。有关说明，请参阅[使用 .NET SDK 设置 HDInsight 群集](/documentation/articles/hdinsight-provision-clusters#sdk)。

3. 使用 **ScriptAction** 类调用自定义脚本，如下所示：

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// Provide the cluster information, like
			// name, Storage account, credentials,
			// cluster size, and version		    
			...
			...
		};
<!-- keep by customization: end -->
## 支持 HDInsight 群集上使用的开放源代码软件
Microsoft Azure HDInsight 服务是一个弹性平台，可让你使用围绕着 Hadoop 形成的开放源代码技术生态系统，在云中生成大数据应用程序。Microsoft Azure 为开放源代码技术提供一般级别的支持，如 <a href="/support/faq/" target="_blank">Azure 支持常见问题网站</a>上的“支持范围”部分中所述。HDInsight 服务为如下所述的某些组件提供附加的支持级别。

HDInsight 服务中有两种类型的开放源代码组件：

- **内置组件** - 这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。例如，Yarn ResourceManager、Hive 查询语言 (HiveQL) 及 Mahout 库均属于此类别。<a href="/documentation/articles/hdinsight-component-versioning/" target="_blank">HDInsight 提供的 Hadoop 群集版本有哪些新功能</a>中提供了群集组件的完整列表。
- **自定义组件** - 作为群集用户，你可以安装，或者在工作负荷中使用由社区提供的或你自己创建的任何组件。

完全支持内置组件，Microsoft 支持部门将帮助你找出并解决与这些组件相关的问题。

> [AZURE.WARNING]完全支持通过 HDInsight 群集提供的组件，Microsoft 支持部门将帮助你找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助你进一步排查问题。这可能导致问题解决，或要求你参与可用的开放源代码技术渠道，在该处可找到该技术的深入专业知识。有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](http://social.msdn.microsoft.com/Forums/azure/zh-cn/home?forum=hdinsight)、[http://stackoverflow.com](http://stackoverflow.com)。此外，Apache 项目在 [http://apache.org](http://apache.org) 上提供了项目站点，例如 [Hadoop](http://hadoop.apache.org/)。
<!-- deleted by customization , [Spark](http://spark.apache.org/) -->

HDInsight 服务提供多种方式来使用自定义组件。不论在群集上使用组件或安装组件的方式为何，均适用相同级别的支持。以下是可以在 HDInsight 群集上使用的自定义组件最常见方式的列表：

1. 作业提交 - Hadoop 或其他类型的作业可以提交到执行或使用自定义组件的群集。
2. 群集自定义 - 在群集创建期间，你可以指定将安装在群集节点的其他设置和自定义组件。
3. 示例 - 对于常见的自定义组件，Microsoft 和其他人可能会提供演示如何在 HDInsight 群集上使用这些组件的示例。我们不针对这些示例提供支持。

## 开发脚本操作脚本

请参阅[为 HDInsight 开发脚本操作脚本][hdinsight-write-script]。


## 另请参阅

- [在 HDInsight 中创建 Hadoop 群集][hdinsight-provision-cluster]说明了如何使用其他自定义选项来创建 HDInsight 群集。
- [为 HDInsight 开发脚本操作脚本][hdinsight-write-script]
- [在 HDInsight 群集上安装并使用 R][hdinsight-install-r]
- [在 HDInsight 群集上安装并使用 Solr](/documentation/articles/hdinsight-hadoop-solr-install)
- [在 HDInsight 群集上安装并使用 Giraph](/documentation/articles/hdinsight-hadoop-giraph-install)

<!-- deleted by customization
- [Install and use Spark on HDInsight clusters][hdinsight-install-spark]
-->

<!-- deleted by customization
[hdinsight-install-spark]: /documentation/articles/hdinsight-hadoop-spark-install
-->
[hdinsight-install-r]: /documentation/articles/hdinsight-hadoop-r-scripts
[hdinsight-write-script]: /documentation/articles/hdinsight-hadoop-script-actions
[hdinsight-provision-cluster]: /documentation/articles/hdinsight-provision-clusters
[powershell-install-configure]: /documentation/articles/install-configure-powershell
[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "群集创建过程中的阶段"

<!---HONumber=79-->
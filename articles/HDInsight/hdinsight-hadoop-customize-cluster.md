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
	ms.date="11/13/2015"
	wacn.date=""/>

# 使用脚本操作自定义 HDInsight 群集 (Windows)

[AZURE.INCLUDE [usescriptaction-selector](../includes/hdinsight-selector-use-script-action.md)]

在创建群集的过程中，可以使用**脚本操作**来调用[自定义脚本](/documentation/articles/hdinsight-hadoop-script-actions)，以便在群集上安装其他软件。

你也可以使用多种其他方法来自定义 HDInsight 群集，例如包含其他存储帐户、更改 hadoop 配置文件（core-site.xml、hive-site.xml 等），或者将共享库（例如 Hive、Oozie）添加到群集中的共同位置。这些自定义可以通过使用 Azure PowerShell、Azure HDInsight .NET SDK 或 Azure 管理门户来完成。有关详细信息，请参阅[在 HDInsight 中创建 Hadoop 群集][hdinsight-provision-cluster]。

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
**安装 R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1。请参阅 [在 HDInsight 群集上安装并使用 R][hdinsight-install-r]。
**安装 Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1。请参阅 [在 HDInsight 群集上安装并使用 Solr](/documentation/articles/hdinsight-hadoop-solr-install)。
- **安装 Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1。请参阅 [在 HDInsight 群集上安装并使用 Giraph](/documentation/articles/hdinsight-hadoop-giraph-install)。



## 使用 Azure 管理门户调用脚本

**通过 Azure 管理门户**

1. 根据[使用自定义选项设置群集](/documentation/articles/hdinsight-provision-clusters#portal)中的说明，使用“自定义创建”选项开始设置群集。 
2. 在向导的“脚本操作”页上，单击“添加脚本操作”，以提供有关脚本操作的详细信息，如下所示：

	![使用脚本操作自定义群集](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "使用脚本操作自定义群集")

	<table border='1'>
		<tr><th>属性</th><th>值</th></tr>
		<tr><td>Name</td>
			<td>指定脚本操作的名称。</td></tr>
		<tr><td>脚本 URI</td>
			<td>指定要调用以自定义群集的脚本的 URI。</td></tr>
		<tr><td>头节点/辅助节点</td>
			<td>指定在其上运行自定义脚本的节点（**Head** 或 **Worker**）。</b>
		<tr><td>Parameters</td>
			<td>根据脚本的需要，指定参数。</td></tr>
	</table>

	按 ENTER 可添加多个脚本操作，以在群集上安装多个组件。

3. 单击“选择”可保存脚本操作配置并继续执行群集创建。

##<a name="call_scripts_using_powershell"></a>使用 Azure PowerShell 调用脚本

以下 PowerShell 脚本演示如何在基于 Windows 的 HDInsight 群集上安装 Spark。

	# Provide values for these variables
	$subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

	$nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
	$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
	
	$resourceGroupName = $namePrefix + "rg"
	$location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
	
	$hdinsightClusterName = $namePrefix + "spark"
	$httpUserName = "admin"
	$httpPassword = "Pass@word111"
	
	$defaultStorageAccountName = "$namePrefix" + "store"
	$defaultBlobContainerName = $hdinsightClusterName
	
	#############################################################
	# Connect to Azure
	#############################################################
	
	Try{
		Get-AzureRmSubscription
	}
	Catch{
		Login-AzureRmAccount
	}
	Select-AzureRmSubscription -SubscriptionId $subscriptionID
	
	#############################################################
	# Prepare the dependent components
	#############################################################
	
	# Create resource group
	New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
	
	# Create storage account
	New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $storageAccountKey  
	New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext
	
	#############################################################
	# Create cluster with Spark
	#############################################################
	
	# Specify the configuration options
	$config = New-AzureRmHDInsightClusterConfig `
				-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.chinacloudapi.cn" `
				-DefaultStorageAccountKey $defaultStorageAccountKey 
				
	
	# Add a script action to the cluster configuration
	$config = Add-AzureRmHDInsightScriptAction `
				-Config $config `
				-Name "Install Spark" `
				-NodeType HeadNode `
				-Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
	
	# Start creating a cluster with Spark installed
	New-AzureRmHDInsightCluster `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $hdinsightClusterName `
			-Location $location `
			-ClusterSizeInNodes 2 `
			-ClusterType Hadoop `
			-OSType Windows `
			-DefaultStorageContainer $defaultBlobContainerName `
			-Config $config


若要安装其他软件，需要替换脚本中的脚本文件：


出现提示时，请输入群集的凭据。创建群集可能需要几分钟时间。

## 使用 .NET SDK 调用脚本 

以下示例演示如何在基于 Windows 的 HDInsight 群集上安装 Spark。若要安装其他软件，需要替换代码中的脚本文件。

**创建包含 Spark 的 HDInsight 群集**

1. 在 Visual Studio 中创建 C# 控制台应用程序。
2. 通过 Nuget 包管理器控制台运行以下命令。

		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Common.Authentication -Pre

2. 在 Program.cs 文件中使用以下 using 语句：

		using System;
		using System.Security;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;

3. 将类中的代码替换为以下内容：

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

4. 按 **F5** 运行应用程序。


## 支持 HDInsight 群集上使用的开放源代码软件
Microsoft Azure HDInsight 服务是一个弹性平台，可让你使用围绕着 Hadoop 形成的开放源代码技术生态系统，在云中生成大数据应用程序。Microsoft Azure 为开放源代码技术提供一般级别的支持，如 <a href="/support/faq/" target="_blank">Azure 支持常见问题网站</a>上的**支持范围**部分中所述。HDInsight 服务为如下所述的某些组件提供附加的支持级别。

HDInsight 服务中有两种类型的开放源代码组件：

- **内置组件** - 这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。例如，Yarn ResourceManager、Hive 查询语言 (HiveQL) 及 Mahout 库均属于此类别。[HDInsight 提供的 Hadoop 群集版本有哪些新增功能](/documentation/articles/hdinsight-component-versioning)</a>中提供了群集组件的完整列表。
- **自定义组件** - 作为群集用户，你可以安装，或者在工作负荷中使用由社区提供的或你自己创建的任何组件。

完全支持内置组件，Microsoft 支持部门将帮助你找出并解决与这些组件相关的问题。

> [AZURE.WARNING]完全支持通过 HDInsight 群集提供的组件，Microsoft 支持部门将帮助你找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助你进一步排查问题。这可能导致问题解决，或要求你参与可用的开放源代码技术渠道，在该处可找到该技术的深入专业知识。有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/zh-cn/home?forum=hdinsight)、[http://stackoverflow.com](http://stackoverflow.com)。此外，Apache 项目在 [http://apache.org](http://apache.org) 上提供了项目站点，例如 [Hadoop](http://hadoop.apache.org/)。

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

[hdinsight-install-r]: /documentation/articles/hdinsight-hadoop-r-scripts
[hdinsight-write-script]: /documentation/articles/hdinsight-hadoop-script-actions
[hdinsight-provision-cluster]: /documentation/articles/hdinsight-provision-clusters
[powershell-install-configure]: /documentation/articles/powershell-install-configure


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "群集创建过程中的阶段"

<!---HONumber=Mooncake_0104_2016-->
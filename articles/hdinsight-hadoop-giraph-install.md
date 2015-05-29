<properties 
	pageTitle="使用 HDInsight 中的操作脚本在 Hadoop 群集上安装 Giraph | Azure" 
	description="了解如何自定义 HDInsight 群集以安装 Giraph。你将使用"脚本操作"配置选项，通过一个脚本来安装 Giraph" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	wacn.date="" 
	ms.author="nitinme"/>

# 在 HDInsight Hadoop 群集上安装和使用 Giraph

你可以使用**脚本操作**群集自定义，在 Azure HDInsight 上 Hadoop 中的任何一种群集上安装 Giraph。脚本操作可让你运行脚本，以便只在创建群集时才自定义群集。有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集][hdinsight-cluster-customize]。

在本主题中，你将学习如何使用脚本操作来安装 Giraph。一旦你已安装 Giraph，你还将了解如何将 Giraph 用于大多数典型应用程序，也就是处理大型图形。


## <a name="whatis"></a>什么是 Giraph？

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> 可让你使用 Hadoop 执行图形处理，并可以在 Azure HDInsight 上使用。图形可为对象之间的关系建模，例如，为大型网络（例如 Internet）上的路由器之间的连接建模，或者为社交网络上的人物之间的关系建模（有时称为社交图形）。通过图形处理，你可以推理图形中对象之间的关系，例如：

- 根据当前的关系识别潜在的朋友。
- 识别网络中两台计算机之间的最短路由。
- 计算网页的排名。

   
## <a name="install"></a>如何安装 Giraph？

你可以从一个只读的 Azure 存储 Blob 获取在 HDInsight 群集上安装 Giraph 的示例脚本，网址为 [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1)。本部分提供有关如何在使用 Azure 门户设置群集时使用示例脚本的说明。 

> [AZURE.NOTE] 该示例脚本只能与 HDInsight 群集版本 3.1 配合使用。有关 HDInsight 群集版本的详细信息，请参阅 [HDInsight 群集版本](hdinsight-component-versioning)。

1. 根据[使用自定义选项设置群集](/documentation/articles/hdinsight-provision-clusters/#portal)中的说明，使用"自定义创建"选项开始设置群集。 
2. 在向导的"脚本操作"页中，单击"添加脚本操作"以提供有关脚本操作的详细信息，如下所示：

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>属性</th><th>值</th></tr>
		<tr><td>名称</td>
			<td>指定脚本操作的名称。例如 <b>Install Giraph</b>。</td></tr>
		<tr><td>脚本 URI</td>
			<td>指定要调用来自定义群集的脚本的统一资源标识符 (URI)。例如 <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
		<tr><td>节点类型</td>
			<td>指定运行自定义脚本的节点。你可以选择"所有节点"、"仅头节点"或"仅辅助节点"<b></b><b></b><b></b>。
		<tr><td>Parameters</td>
			<td>根据脚本的要求指定参数。用于安装 Giraph 的脚本不需要任何参数，因此你可以将此字段留空。</td></tr>
	</table>	

	你可以添加多个脚本操作，以在群集上安装多个组件。添加脚本之后，单击复选标记开始设置群集。

你也可以使用 Azure PowerShell 或 HDInsight .NET SDK 在 HDInsight 上使用脚本安装 Giraph。本主题稍后将提供这些过程的说明。

## <a name="usegiraph"></a>如何在 HDInsight 中使用 Giraph？

我们将使用 SimpleShortestPathsComputation 示例来演示有关查找图形中对象之间最短路径的基本 <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> 实现。请执行以下步骤，以上载示例数据和示例 jar，使用 SimpleShortestPathsComputation 示例运行作业，然后查看结果。

1. 将示例数据文件上载到 Azure Blob 存储。在本地工作站上，创建名为 **tiny_graph.txt** 的新文件。该文件应该包含以下几行：

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	将 tiny_graph.txt 文件上载到 HDInsight 群集的主存储。有关如何上载数据的说明，请参阅[在 HDInsight 中上载 Hadoop 作业的数据](hdinsight-upload-data)。

	此数据使用 [source\_id, source\_value,[[dest\_id], [edge\_value],...]] 格式，描述定向图形中对象之间的关系。每一行代表 **source\_id** 对象与一个或多个 **dest\_id** 对象之间的关系。**edge\_value**（或权重）可被视为 **source_id** 和 **dest\_id** 之间的连接强度或距离。

	使用表示对象间距离的值（或权重）绘制图形后，上述数据可能与下面类似。

	![tiny_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)

	

4. 运行 SimpleShortestPathsComputation 示例。使用 tiny_graph.txt 文件作为输入，通过以下 Azure PowerShell cmdlet 来运行该示例。这要求你事先安装并配置 [Azure PowerShell][powershell-install-configure]。

		$clusterName = "clustername"
		# Giraph examples jar
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments
		
		# Run the job, write output to the Azure PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	在上面的示例中，请将 **clustername** 替换为已装有 Giraph 的 HDInsight 群集的名称。

5. 查看结果。完成该作业后，结果将存储在 __wasb:///example/out/shotestpaths 文件夹中的两个输出文件中。这些文件名为 __part-m-00001__ 和 __part-m-00002__。执行以下步骤以下载和查看输出：

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure Storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Create the Storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	这在工作站上的当前目录中创建 __example/output/shortestpaths__ 目录结构，并将两个输出文件下载到该位置。

	使用 __Cat__ cmdlet 显示文件的内容： 

		Cat example/output/shortestpaths/part*

	输出应如下所示：


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	SimpleShortestPathComputation 示例硬编码为从对象 ID 1 开始查找与其他对象间的最短路径。因此，输出应显示为 `destination_id distance`，其中，distance 为对象 ID 1 与目标 ID 的边缘之间的行程值（或权重）。
	
	在可视化此数据的情况下，你可以通过体验 ID 1 与所有其他对象之间的最短路径来验证结果。请注意，ID 1 和 ID 4 之间的最短路径为 5。这是从 ID 1 到 ID 3， <span style="color:orange">然后再从 ID 3 到 ID 4</span>的总 <span style="color:red">距离。</span>。

	![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-hadoop-giraph-install\giraph-graph-out.png) 


## <a name="usingPS"></a>使用 Azure PowerShell 在 HDInsight Hadoop 群集上安装 Giraph

在本部分中，我们将通过使用脚本操作自定义群集，使用 **<a href = "http://msdn.microsoft.com/zh-cn/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet 来调用脚本。在继续之前，请确保已安装并配置 Azure PowerShell。有关配置工作站以运行适用于 HDInsight 的 Azure Powershell cmdlet 的信息，请参阅[安装和配置 Azure PowerShell][powershell-install-configure]。

请执行以下步骤：

1. 打开 Azure PowerShell 窗口并声明以下变量：

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. 指定配置值，例如要使用的群集中节点和默认存储：

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.chinacloudapi.cn"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. 使用 **Add-AzureHDInsightScriptAction** cmdlet 将脚本操作添加到群集配置中。稍后在创建群集时，将要执行该脚本操作。 

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

	**Add-AzureHDInsightScriptAction** cmdlet 使用以下参数：

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">参数</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定义</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">要在其中添加脚本操作信息的配置对象。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名称</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">脚本操作的名称。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">运行自定义脚本的节点。有效值为 HeadNode（在头节点上安装）或 DataNode（在所有数据节点上安装）。你可以使用其中一个或两个值。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">要执行的脚本的 URI。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parameters</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">脚本所需的参数。本主题中使用的示例脚本不需要任何参数，因此你在上述代码段中看不到此参数。
	</td></tr>
	</table>
	
4. 最后，开始设置已安装 Giraph 的自定义群集：  
	
		# Start provisioning a cluster with Giraph installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

出现提示时，请输入群集的凭据。创建群集可能需要几分钟时间。


## <a name="usingSDK"></a>使用 .NET SDK 在 HDInsight Hadoop 群集上安装 Giraph

HDInsight .NET SDK 提供 .NET 客户端库，可简化从 .NET Framework 应用程序中使用 HDInsight 的操作。本部分说明如何使用 SDK 中的脚本操作来设置已安装 Giraph 的群集。必须执行以下过程：

- 安装 HDInsight .NET SDK
- 创建自签名证书
- 创建控制台应用程序
- 运行应用程序


**安装 HDInsight .NET SDK**

可以从 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安装该 SDK 的最新发行版。下一过程中将显示说明。

**创建自签名证书**

创建自签名证书，将其安装到工作站上，然后将其上传到你的 Azure 订阅。有关说明，请参阅[创建自签名证书](/zh-cn/documentation/articles/hdinsight-administer-use-management-portal/#cert)。 


**创建 Visual Studio 应用程序**

1. 打开 Visual Studio 2013。

2. 在"文件"菜单中，单击"新建"，然后单击"项目"。

3. 在"新建项目"中，键入或选择以下值：
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">属性</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">值</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">类别</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">模板/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">模板</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">控制台应用程序</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名称</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateGiraphCluster</td></tr>
	</table>

4. 单击"确定"以创建该项目。

5. 在"工具"菜单中，单击"Nuget Package Manager"，然后单击"Package Manager Console"。

6. 在控制台中运行以下命令以安装该包：

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	此命令将从当前 Visual Studio 项目添加 .NET 库以及对这些库的引用。

7. 在解决方案资源管理器中，双击 **Program.cs** 将其打开。

8. 将下列 using 语句添加到文件顶部：

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. 在 main () 函数中，复制并粘贴以下代码，并为变量提供值：
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.chinacloudapi.cn";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. 将以下代码附加到 Main() 函数中，以使用 [ScriptAction](http://msdn.microsoft.com/zh-cn/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) 类来调用自定义脚本安装 Giraph：

		// Add the script action to install Giraph
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //Because the script used does not require any parameters
        ));

11. 最后，请创建群集：

		client.CreateCluster(clusterInfo);

12. 保存对应用程序所做的更改，然后生成解决方案。 

**运行应用程序**

打开 Azure PowerShell 控制台，浏览到存储 Visual Studio 项目的位置，浏览到项目中的 \bin\debug 目录，然后运行以下命令：

	.\CreateGiraphCluster <cluster-name>

提供群集名称，然后按 ENTER 设置已安装 Giraph 的群集。


## 另请参阅##
- [在 HDinsight Hadoop 群集上安装和使用 Spark][hdinsight-install-spark]：提供了有关如何使用群集自定义在 HDInsight Hadoop 群集上安装和使用 Spark 的说明。Spark 是一个开放源代码并行处理框架，它支持内存中处理以提升大数据分析应用程序的性能。
- [在 HDinsight Hadoop 群集上安装 R][hdinsight-install-r] 提供了有关如何使用群集自定义在 HDInsight Hadoop 群集上安装和使用 R 的说明。R 是一个用于统计计算的开放源代码语言和环境。它提供数百个内置统计函数及其自身的编程语言，该语言结合了函数编程和对象导向编程的方面。它还提供广泛的图形功能。
- [在 HDInsight 群集上安装 Solr](hdinsight-hadoop-solr-install)。在 HDInsight Hadoop 群集上使用群集自定义安装 Solr。Solr 可让你对存储的数据执行强大的搜索操作。



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: /documentation/articles/install-configure-powershell/

[hdinsight-provision]: /documentation/articles/hdinsight-provision-clusters/
[hdinsight-install-r]: /documentation/articles/hdinsight-hadoop-r-scripts/
[hdinsight-install-spark]: /documentation/articles/hdinsight-hadoop-spark-install/
[hdinsight-cluster-customize]: /documentation/articles/hdinsight-hadoop-customize-cluster

<!---HONumber=56-->
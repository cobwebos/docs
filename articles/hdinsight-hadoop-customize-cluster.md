<properties 
	pageTitle="使用脚本操作自定义 HDInsight 群集 | Azure" 
	description="了解如何使用脚本操作自定义 HDInsight 群集。" 
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

# 使用脚本操作自定义 HDInsight 群集

你可以自定义 Azure HDInsight 群集，以在群集上安装额外的软件或更改群集上的应用程序配置。HDInsight 提供了一个称为**脚本操作**的配置选项，该脚本操作可调用自定义脚本，以定义要在群集上执行自定义。在 *部署群集时*，可以使用这些脚本自定义该群集。  

你也可以使用多种其他方法来自定义 HDInsight 群集，例如包含其他存储帐户、更改 hadoop 配置文件（core-site.xml、hive-site.xml 等），或者将共享库（例如 Hive、Oozie）添加到群集中的共同位置。这些自定义可以通过使用 Azure PowerShell、Azure HDInsight .NET SDK 或 Azure 门户来完成。有关详细信息，请参阅[使用自定义选项在 HDInsight 中设置 Hadoop 群集][hdinsight-provision-cluster]。



> [AZURE.NOTE] 只有在 HDInsight 群集版本 3.1 上才支持使用"脚本操作"来自定义群集。有关 HDInsight 群集版本的详细信息，请参阅 [HDInsight 群集版本](hdinsight-component-versioning)。


## <a name="lifecycle"></a>创建群集期间如何使用脚本？

使用"脚本操作"时，只有在创建 HDInsight 群集的过程中可以自定义群集。创建 HDInsight 群集时，经历以下阶段：

![HDInsight cluster customization and stages during cluster provisioning][img-hdi-cluster-states] 

在群集创建作业完成 **HDInsightConfiguration** 阶段后并在 **ClusterOperational** 阶段之前，调用脚本。每个群集可接受多个脚本操作，这些脚本依其指定顺序被调用。

> [AZURE.NOTE] 自定义 HDInsight 群集的选项是标准 Azure HDInsight 订阅免费提供的功能之一。

### 脚本的工作原理

你可以选择在头节点和/或辅助节点上运行脚本。当脚本运行时，群集进入 **ClusterCustomization** 阶段。在此阶段，脚本在系统管理员帐户下，以并行方式在群集中所有指定的节点上运行，而在节点上提供完全的系统管理员权限。 

> [AZURE.NOTE] 因为你在 **Cluster customization** 阶段中于群集节点上拥有系统管理员权限，所以你可以使用脚本来运行作业，例如停止和启动服务，包括 Hadoop 相关服务。因此，在脚本中，你必须在脚本完成运行之前，确定 Ambari 服务及其他 Hadoop 相关服务已启动并且正在运行。这些服务必须在群集创建时，成功地确定群集的运行状况和状态。如果你更改群集上的任何影响这些服务的配置，必须使用所提供的帮助器函数。有关帮助器函数的详细信息，请参阅[使用 HDInsight 进行脚本操作开发][hdinsight-write-script]。

脚本的输出以及错误日志文件存储在你为群集指定的默认存储帐户中。日志以 **u<\cluster-name-fragment><\time-stamp>setuplog** 的名称存储在表中。这是从群集中所有节点上（头节点和辅助节点）运行的脚本聚合的日志文件。

## <a name="writescript"></a>如何编写一个脚本来进行群集自定义？

有关如何编写群集自定义脚本的信息，请参阅[使用 HDInsight 进行脚本操作开发][hdinsight-write-script]。 

## <a name="howto"></a>如何使用脚本操作来自定义群集？

你可以从 Azure 门户、Azure PowerShell Cmdlet 或 HDInsight .NET SDK 使用脚本操作来自定义群集。 

**使用 Azure 门户**

1. 根据[使用自定义选项设置群集](/documentation/articles/hdinsight-provision-clusters/#portal)中的说明，使用"自定义创建"选项开始设置群集。 
2. 在向导的"脚本操作"页中，单击"添加脚本操作"以提供有关脚本操作的详细信息，如下所示：

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>属性</th><th>值</th></tr>
		<tr><td>名称</td>
			<td>指定脚本操作的名称。</td></tr>
		<tr><td>脚本 URI</td>
			<td>指定要调用来自定义群集的脚本的 URI。</td></tr>
		<tr><td>节点类型</td>
			<td>指定运行自定义脚本的节点。你可以选择"所有节点"、"仅头节点"或"仅辅助节点"<b></b><b></b><b></b>。
		<tr><td>参数</td>
			<td>根据脚本的要求指定参数。</td></tr>
	</table>

	你可以添加多个脚本操作，以在群集上安装多个组件。添加脚本之后，单击复选标记开始设置群集。 
  
**使用 Azure PowerShell cmdlet**

使用 Azure PowerShell 命令来运行单个脚本操作或多个脚本操作。你可以使用 **<a href = "http://msdn.microsoft.com/zh-cn/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet 调用自定义脚本。若要使用这些 cmdlet，你必须已安装并设置 Azure PowerShell。有关配置工作站以运行适用于 HDInsight 的 Azure Powershell cmdlet 的信息，请参阅[安装和配置 Azure PowerShell][powershell-install-configure]。

使用以下 Azure PowerShell 命令可以在部署 HDInsight 群集时运行单个脚本操作：

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName -Uri http://uri.to/scriptaction.ps1 -Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

使用以下 Azure PowerShell 命令可以在部署 HDInsight 群集时运行多个脚本操作：

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName1 -Uri http://uri.to/scriptaction1.ps1 -Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName2 -Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**使用 HDInsight .NET SDK**

HDInsight .NET SDK 提供了 <a href="http://msdn.microsoft.com/zh-cn/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> 类用于调用自定义脚本。使用 HDInsight .NET SDK：

1. 创建一个 Visual Studio 应用程序，然后从 Nuget 安装 SDK。在"工具"菜单中，单击"Nuget Package Manager"，然后单击"Package Manager Console"。在控制台中运行以下命令以安装该包：

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. 使用 SDK 创建群集。有关说明，请参阅[使用 .NET SDK 设置 HDInsight 群集](/documentation/articles/hdinsight-provision-clusters/#sdk)。

3. 使用 **ScriptAction** 类调用自定义脚本，如下所示：

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// Provide the cluster information, like
			// name, Storage account, credentials,
			// cluster size, and version		    
			...
			...
		};

		// Add the script action to install Spark
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install the component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script
		));


## <a name="example"></a>群集自定义示例

为了帮助你入门，HDInsight 提供了可在 HDInsight 群集上安装以下组件的示例脚本：

- **安装 Spark** - 请参阅[在 HDInsight 群集上安装和使用 Spark][hdinsight-install-spark]。
- **安装 R** - 请参阅[在 HDInsight 群集上安装和使用 R][hdinsight-install-r]。
- **安装 Solr** - 请参阅[在 HDInsight 群集上安装和使用 Solr](hdinsight-hadoop-solr-install)。
- **安装 Giraph** - 请参阅[在 HDInsight 群集上安装和使用 Giraph](hdinsight-hadoop-giraph-install)。

## <a name="support"></a>支持 HDInsight 群集上使用的开放源代码软件
Microsoft Azure HDInsight 服务是弹性平台，可让你使用围绕着 Hadoop 形成的开放源代码技术生态系统，在云生成大数据应用程序。Microsoft Azure 为开放源代码技术提供一般级别的支持，如 <a href="/support/faq/" target="_blank">Azure 支持常见问题网站</a>上的**支持范围**部分中所述。HDInsight 服务为如下所述的某些组件提供附加的支持级别。

HDInsight 服务中有两种类型的开放源代码组件：

- **内置组件** - 这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。例如，Yarn ResourceManager、Hive 查询语言 (HiveQL) 及 Mahout 库均属于此类别。<a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">HDInsight 提供的 Hadoop 群集版本有哪些新功能？</a>中提供了群集组件的完整列表。
- **自定义组件** - 作为群集用户，你可以安装，或者在工作负载中使用由社区提供的或你自己创建的任何组件。

完全支持内置组件，Microsoft 支持部门将帮助你找出并解决与这些组件相关的问题。

自定义组件可获得合理范围的支持，以帮助你进一步排查问题。这可能导致问题解决，或要求你参与可用的开放源代码技术渠道，在该处可找到该技术的深入专业知识。例如，有许多可以使用的社区站点，例如：<a href ="https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight" target="_blank">HDInsight 的 MSDN 论坛</a>和<a href="http://stackoverflow.com" target="_blank">堆栈溢出</a>。此外，Apache 项目在 <a href="http://apache.org" target="_blank">Apache.org</a> 上提供了项目站点，例如 <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a> 和 <a href="http://spark.apache.org/" target="_blank">Spark</a>。

HDInsight 服务提供多种方式来使用自定义组件。不论在群集上使用组件或安装组件的方式为何，均适用相同级别的支持。以下是可以在 HDInsight 群集上使用的自定义组件最常见方式的列表：

1. 作业提交 - Hadoop 或其他类型的作业可以提交到执行或使用自定义组件的群集。
2. 群集自定义 - 在群集创建期间，你可以指定将安装在群集节点的其他设置和自定义组件。
3. 示例 - 对于常见的自定义组件，Microsoft 和其他人可能会提供演示如何在 HDInsight 群集上使用这些组件的示例。我们不针对这些示例提供支持。


## 另请参阅##
[使用自定义选项在 HDInsight 中设置 Hadoop 群集][hdinsight-provision-cluster]说明了如何使用其他自定义选项来设置 HDInsight 群集。

[hdinsight-install-spark]: /documentation/articles/hdinsight-hadoop-spark-install/
[hdinsight-install-r]: /documentation/articles/hdinsight-hadoop-r-scripts/
[hdinsight-write-script]: /documentation/articles/hdinsight-hadoop-script-actions/
[hdinsight-provision-cluster]: /documentation/articles/hdinsight-provision-clusters/
[powershell-install-configure]: /documentation/articles/install-configure-powershell/


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster provisioning"

<!---HONumber=56-->
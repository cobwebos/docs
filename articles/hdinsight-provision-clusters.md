<properties 
   pageTitle="在 HDInsight 中设置 Hadoop 群集 | Azure" 
   description="了解如何通过使用 Azure 门户、Azure PowerShell、命令行或 HDInsight .NET SDK 设置 Azure HDInsight 群集" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/21/2015"
   wacn.date="" 
   ms.author="nitinme"/>

# 通过使用自定义选项在 HDInsight 中设置 Hadoop 群集

了解在 Azure HDInsight 上自定义设置 Hadoop 群集的不同方法 - 使用 Azure 门户、Azure PowerShell、命令行工具或 HDInsight .NET SDK。有关设置 HBase 群集和 Storm 群集的说明，请参阅[在 HDInsight 中设置 HBase 群集](hdinsight-hbase-get-started)和[开始在 HDInsight 上使用 Storm](hdinsight-storm-getting-started.md)。请参阅 <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Hadoop 与 HBase 之间的区别</a>，以了解你为何可以选择一个，而不选择另一个。

## 什么是 HDInsight 群集？

你是否曾经疑惑过，为何我们只要谈论 Hadoop 或 BigData 就会提到群集？这是因为，Hadoop 能够针对分散在不同群集节点上的大型数据实现分布式处理。群集采用主/从体系结构，其中包括一个主节点（又称头节点或命名节点）和若干个从节点（又称数据节点）。有关详细信息，请参阅 [Apache Hadoop][apache-hadoop]。

![HDInsight Cluster][img-hdi-cluster]

HDInsight 群集会抽象化 Hadoop 实现详细信息，因此你不必担心如何与不同的群集节点通信。当你设置 HDInsight 群集时，便设置了包含 Hadoop 和相关应用程序的 Azure 计算资源。有关详细信息，请参阅 [HDInsight 中的 Hadoop 简介](hdinsight-hadoop-introduction)。要改动的数据存储在 Azure Blob 存储中。有关详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用](hdinsight-use-blob-storage)。

本文提供有关群集的不同设置方法的说明。如果你正在寻求通过一种快捷的方法设置群集，请参阅 [Azure HDInsight 入门](hdinsight-get-started)。

**先决条件：**

在开始按照本文中的说明操作之前，你必须具有以下内容：

- Azure 订阅。Azure 是基于订阅的平台。HDInsight Windows PowerShell cmdlet 使用你的订阅来执行任务。有关获得订阅的详细信息，请参阅[购买选项][azure-purchase-options][试用版][azure-trial]。

## <a id="configuration"></a>配置选项

### Linux 上的群集

HDInsight 提供在 Azure 上配置 Linux 群集的选项。如果你熟悉 Linux 或 Unix，要从现有的基于 Linux 的 Hadoop 解决方案进行迁移，或者想要轻松集成针对 Linux 构建的 Hadoop 生态系统组件，请配置 Linux 群集。有关详细信息，请参阅[在 HDInsight 中的 Linux 上开始使用 Hadoop](hdinsight-hadoop-linux-get-started)。 

### 其他存储

在配置期间，你必须指定 Azure Blob 存储帐户和默认容器。该容器被集群用作默认存储位置。你可以选择指定还要与群集关联的其他 Azure 存储帐户。   

>[AZURE.NOTE] 不要对多个群集共享一个 Blob 存储容器。此操作不受支持。 

有关使用辅助 Blob 存储的详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用](hdinsight-use-blob-storage)。

### 元存储

元存储包含 Hive 和 Oozie 元数据，例如 Hive 表、分区、架构和列。使用元存储有助于保留你的 Hive 和 Oozie 元数据，这样，在设置新群集时，你不需要重新创建 Hive 表或 Oozie 作业。默认情况下，Hive 使用嵌入的数据库存储该信息。在删除群集时，嵌入的数据库无法保留元数据。

### 群集自定义

你可以在设置期间通过使用脚本安装其他组件或自定义群集配置。此类脚本可通过**脚本操作**调用，脚本操作是一种配置选项，可通过 Azure 门户、HDInsight Windows PowerShell cmdlet 或 HDInsight .NET SDK 使用。有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster)。


### 虚拟网络

[Azure 虚拟网络](/documentation/services/networking/)允许你创建包含需要用于解决方案的资源的安全永久性网络。通过虚拟网络，你可以：

* 在专用网络（仅限云）中将云资源连接在一起。

	![diagram of cloud-only configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* 通过使用虚拟专用网络 (VPN) 将云资源连接到本地数据中心网络（站点到站点或点到站点）。

	利用站点到站点配置，你可以通过使用硬件 VPN 或路由和远程访问服务将多个资源从数据中心连接到 Azure 虚拟网络。

	![diagram of site-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	利用点到站点配置，你可以通过使用软件 VPN 将特定资源连接到 Azure 虚拟网络。

	![diagram of point-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

有关虚拟网络特性、优势和功能的详细信息，请参阅 [Azure 虚拟网络概述](http://msdn.microsoft.com/zh-cn/library/azure/jj156007.aspx)。

> [AZURE.NOTE] 你必须先创建 Azure 虚拟网络，然后才能设置 HDInsight 群集。有关详细信息，请参阅[虚拟网络配置任务](http://msdn.microsoft.com/zh-cn/library/azure/jj156206.aspx)。
>
> Azure HDInsight 仅支持基于位置的虚拟网络，并且当前不适用于基于地缘组的虚拟网络。
>
> 强烈建议你为一个群集指定一个子网。

## <a id="portal"></a> 使用 Azure 门户

HDInsight 群集使用 Azure Blob 存储容器作为默认文件系统。创建 HDInsight 群集前，需要具有位于同一数据中心的 Azure 存储帐户。有关详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用](hdinsight-use-blob-storage)。有关创建 Azure 存储帐户的详细信息，请参阅[如何创建存储帐户](storage-create-storage-account)。


> [AZURE.NOTE] 目前，只有**东亚**、**东南亚**、**北欧**、**西欧**、**美国东部**、**美国西部**、**美国中北部**和**美国中南部**地区才能托管 HDInsight 群集。

**通过使用"自定义创建"选项创建 HDInsight 群集**

1. 登录到 [Azure 门户][azure-management-portal]。
2. 单击页面底部的"+ 新建"，然后依次单击"数据服务"、"HDINSIGHT"和"自定义创建"。
3. 在"群集详细信息"页上，键入或选择以下值：

	![Provide Hadoop HDInsight cluster details][image-customprovision-page1]

    <table border='1'>
		<tr><th>属性</th><th>值</th></tr>
		<tr><td>群集名称</td>
			<td><p>命名群集。 </p>
				<ul>
				<li>域名系统 (DNS) 名称必须以字母数字字符开头和结尾，并且可以包含短划线。</li>
				<li>字段必须是介于 3 到 63 个字符之间的字符串。</li>
				</ul></td></tr>
		<tr><td>群集类型</td>
			<td>对于群集类型，请选择"Hadoop"<strong></strong>。</td></tr>
		<tr><td>操作系统</td>
			<td>选择 <b>Windows Server 2012 R2 Data Center</b> 以设置 Windows 群集。若要设置 Linux 群集，请参阅<a href="/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">在 HDInsight 中设置 Hadoop Linux 群集</a>。</td></tr>
		<tr><td>HDInsight 版本</td>
			<td>选择版本。对于 Hadoop，默认值为 HDInsight 版本 3.1，该版本使用 Hadoop 2.4。</td></tr>
		</table>

	输入或选择表中所示的值，然后单击右箭头。

4. 在"配置群集"页上，输入或选择以下值：

	![Provide Hadoop HDInsight cluster details](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
	<tr><th>名称</th><th>值</th></tr>
	<tr><td>数据节点</td><td>要部署的数据节点的数目。出于测试目的，创建一个单节点群集。<br />群集大小限制因 Azure 订阅而异。若要提高限制的大小，请联系 Azure 计费支持。</td></tr>
	<tr><td>区域/虚拟网络</td><td><p>选择与上一个过程中创建的存储帐户相同的区域。HDInsight 要求存储帐户位于同一区域中。稍后，在配置中，你只能选择位于你在此处指定的区域中的存储帐户。</p><p> 可以选择的区域为：<strong>中国东部</strong>、<strong>中国北部</strong>。 <br/>如果你创建了 Azure 虚拟网络，则可以选择 HDInsight 群集要配置使用的网络。</p><p>有关创建 Azure 虚拟网络的详细信息，请参阅[虚拟网络配置任务](http://msdn.microsoft.com/zh-cn/library/azure/jj156206.aspx)。</p></td></tr>
	<tr><td>头节点大小</td><td><p>为头节点选择虚拟机 (VM) 大小。</p></td></tr>
	<tr><td>数据节点大小</td><td><p>为数据节点选择 VM 大小。</p></td></tr>
	</table>

	>[AZURE.NOTE] 根据所选的 VM，你的成本可能会有所不同。HDInsight 对群集节点使用所有标准层 VM。有关 VM 大小如何影响价格的信息，请参阅 <a href="/home/features/hdinsight/#price" target="_blank">HDInsight 价格</a>。	


5. 在"配置群集用户"页上提供以下值：

    ![Provide Hadoop HDInsight cluster user and metastore details](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>属性</th><th>值</th></tr>
		<tr><td>HTTP 用户名</td>
			<td>指定 HDInsight 群集用户名。</td></tr>
		<tr><td>HTTP 密码/确认密码</td>
			<td>指定 HDInsight 群集用户密码。</td></tr>
		<tr><td>为群集启用远程桌面</td>
			<td>在设置后，选中此复选框，以为可远程连接到群集节点的远程桌面用户指定用户名、密码和到期日期。稍后，你还可以在设置了群集后启用远程桌面。有关说明，请参阅<a href="/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">使用 RDP 连接到 HDInsight 群集</a>。</td></tr>
		<tr><td>输入 Hive/Oozie 元存储</td>
			<td>选中此复选框可指定要用作 Hive/Oozie 元存储的群集所在数据中心上的 SQL 数据库。如果你选中此复选框，则必须在向导的后续页中指定有关 Azure SQL 数据库的详细信息。如果你希望即使在删除群集后也会保留有关 Hive/Oozie 作业的元数据，则此选项将十分有用。</td></tr>
		</td></tr>		
	</table>

	单击右箭头。

6. 在**"配置 Hive/Oozie 元存储"**页上提供以下值：

    ![Provide Hadoop HDInsight cluster user](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

	指定要用作 Hive/Oozie 元存储的 Azure SQL 数据库。你可以为 Hive 和 Oozie 元存储指定相同的数据库。此 SQL 数据库必须与 HDInsight 群集位于同一数据中心。该列表框只列出你在<strong>"群集详细信息"</strong>页中指定的同一数据中心的 SQL 数据库。另请指定用于连接到你选择的 Azure SQL 数据库的用户名和密码。

    >[AZURE.NOTE] 用于元存储的 Azure SQL Database 必须允许连接到其他 Azure 服务，包括 Azure HDInsight。在 Azure SQL 数据库仪表板的右侧单击服务器名称。这是运行 SQL 数据库实例的服务器。进入服务器视图后，请单击"配置"，单击"Azure 服务"对应的"是"，然后单击"保存"。

    单击右箭头。


7. 在"存储帐户"页上提供以下值：

    ![Provide storage account for Hadoop HDInsight cluster](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png)

	<table border='1'>
		<tr><th>属性</th><th>值</th></tr>
		<tr><td>存储帐户</td>
			<td>为 HDInsight 群集指定将用作默认文件系统的 Azure 存储帐户。可以选择以下三个选项之一：
			<ul>
				<li><strong>使用现有存储</strong></li>
				<li><strong>创建新存储</strong></li>
				<li><strong>使用其他订阅中的存储</strong></li>
			</ul>
			</td></tr>
		<tr><td>帐户名</td>
			<td><ul>
				<li>如果选择了使用现有存储，请为"帐户名"选择现有的存储帐户<strong></strong>。下拉列表仅列出你选择设置群集的相同数据中心内的存储帐户。</li>
				<li>如果选择了"创建新存储"或"使用其他订阅中的存储"选项，则必须提供存储帐户名<strong></strong><strong></strong>。</li>
			</ul></td></tr>
		<tr><td>帐户密钥</td>
			<td>如果选择了"使用其他订阅中的存储"选项，请指定该存储帐户的帐户密钥<strong></strong>。</td></tr>
		<tr><td>默认容器</td>
			<td><p>指定存储帐户上用作 HDInsight 群集默认文件系统的默认容器。如果为"存储帐户"字段选择了"使用现有存储"，并且该帐户中不存在现有容器，则默认情况下，将创建与群集同名的容器<strong></strong><strong></strong>。如果已存在与群集同名的容器，则将在容器名称后追加一个序列号。例如，mycontainer1、mycontainer2，等等。但是，如果现有存储帐户的容器名称与你指定的群集名称不同，则你也可以使用该容器。</p>
            <p>如果选择了创建新存储或使用其他 Azure 订阅中的存储，则必须指定默认容器名称。</p>
        </td></tr>
		<tr><td>其他存储帐户</td>
			<td>HDInsight 支持多个存储帐户。一个群集可以使用的其他存储帐户数没有限制。但是，如果你通过使用 Azure 门户创建群集，则由于 UI 限制，你最多只能创建七个存储帐户。指定的每个其他存储帐户将在向导中添加一个额外的"存储帐户"页，以便你在此指定帐户信息。例如，在上面的屏幕截图中，选择了 1 个附加的存储帐户，因此第 5 页添加到了对话框。</td></tr>
	</table>

	单击右箭头。

7. 如果你选择了为群集配置其他存储，请在"存储帐户"页上，输入其他存储帐户的帐户信息：

	![Provide additional storage details for HDInsight cluster](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png)

    同样，你可以选择从现有存储创建新存储，或者使用其他 Azure 订阅中的存储。提供值的过程类似于前面的步骤。

    > [AZURE.NOTE] 一旦为 HDInsight 群集选择了 Azure 存储帐户，就不能再删除该帐户，也不能将它更改为另一帐户。

8. 在**"脚本操作"**页上，单击**"添加脚本操作"**，以在创建群集时提供有关要运行以自定义群集的自定义脚本的详细信息。有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster)。 
	
	![Configure Script Action to customize an HDInsight cluster](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page7.png)

	<table border='1'>
		<tr><th>属性</th><th>值</th></tr>
		<tr><td>名称</td>
			<td>指定脚本操作的名称。</td></tr>
		<tr><td>脚本 URI</td>
			<td>指定调用以自定义群集的脚本的统一资源标识符 (URI)。</td></tr>
		<tr><td>节点类型</td>
			<td>指定在其上运行自定义脚本的节点。你可以选择<b>"所有节点"</b>、<b>"仅限头节点"</b>或<b>"仅限数据节点"</b>。
		<tr><td>参数</td>
			<td>根据脚本的需要，指定参数。</td></tr>
	</table>

	你可以添加多个脚本操作，以在群集上安装多个组件。添加脚本后，单击复选标记以开始设置群集。 

## <a id="powershell"></a> 使用 Azure PowerShell
Azure PowerShell 是一个功能强大的脚本编写环境，可用于在 Azure 中控制和自动执行工作负荷的部署和管理。本部分提供有关如何通过使用 Azure PowerShell 设置 HDInsight 群集的说明。有关配置工作站以运行 HDInsight Windows Powershell cmdlet 的信息，请参阅[安装和配置 Azure PowerShell](install-configure-powershell)。有关将 Azure PowerShell 与 HDInsight 配合使用的详细信息，请参阅[使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell)。有关 HDInsight Windows PowerShell cmdlet 的列表，请参阅 [HDInsight cmdlet 参考][hdinsight-powershell-reference]。

> [AZURE.NOTE] 虽然本部分中的脚本可用于在 Azure 虚拟网络上配置 HDInsight 群集，但它们不能用于创建 Azure 虚拟网络。有关创建 Azure 虚拟网络的信息，请参阅[虚拟网络配置任务](http://msdn.microsoft.com/zh-cn/library/azure/jj156206.aspx)。

通过使用 Azure PowerShell 设置 HDInsight 群集需要执行以下过程：

- 创建 Azure 存储帐户
- 创建 Azure Blob 容器
- 创建 HDInsight 群集

你可以使用 Windows PowerShell 控制台或 Windows PowerShell 集成脚本环境 (ISE) 来运行脚本。

HDInsight 使用 Azure Blob 存储容器作为默认文件系统。你需要先拥有 Azure 存储帐户和存储容器，然后才能创建 HDInsight 群集。存储帐户必须与 HDInsight 群集位于同一数据中心。

**连接到 Azure 帐户**

		Add-AzureAccount 

系统将提示你输入 Azure 帐户凭据。

**创建 Azure 存储帐户**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

如果你已有存储帐户但是不知道帐户名称和帐户密钥，则可以使用以下 Windows PowerShell 命令来检索该信息：

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**创建 Azure Blob 存储容器**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

准备好存储帐户和 Blob 容器后，你就可以创建群集了。

**设置 HDInsight 群集**

> [AZURE.NOTE] Azure PowerShell cmdlet 是唯一推荐用于更改 HDInsight 群集中的配置变量的方法。如果对群集进行修补，可能会覆盖通过远程桌面连接到群集时对 Hadoop 配置文件所做的更改。如果对群集进行修补，将保留通过 Azure PowerShell 设置的配置值。

- 在 Azure PowerShell 控制台窗口中运行以下命令：

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
        $hadoopUserPassword = "<HadoopUserPassword>"    

        $secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)            

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.chinacloudapi.cn" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE] $hadoopUserName 和 $hadoopUserPassword 命令用于群集创建的 Hadoop 用户帐户。此帐户将用于连接到群集并运行作业。如果你在 Azure 门户中使用"快速创建"选项来设置群集，则默认 Hadoop 用户名为"admin"。不要将此帐户与远程桌面协议 (RDP) 用户帐户相混淆。RDP 用户帐户不能与 Hadoop 用户帐户相同。有关详细信息，请参阅[在 HDInsight 中使用 Azure 管理门户管理 Hadoop 群集][hdinsight-admin-portal]。

	设置群集可能需要几分钟时间。

	![HDI.CLI.Provision][image-hdi-ps-provision]



**通过使用自定义配置选项设置 HDInsight 群集**

设置群集时，你可以使用其他配置选项，例如，连接到多个 Azure Blob 存储，使用虚拟网络，或者对 Hive 和 Oozie 元存储使用 Azure SQL 数据库。这样，你便可以将数据和元数据的生存期与群集的生存期分开。

> [AZURE.NOTE] Windows PowerShell cmdlet 是唯一推荐用于更改 HDInsight 群集中的配置变量的方法。如果对群集进行修补，可能会覆盖通过远程桌面连接到群集时对 Hadoop 配置文件所做的更改。如果对群集进行修补，将保留通过 PowerShell 设置的配置值。

- 从 Windows PowerShell 窗口运行以下命令：

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"
		
		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>" 

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.chinacloudapi.cn" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.chinacloudapi.cn" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.chinacloudapi.cn" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.chinacloudapi.cn" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE] 用于元存储的 Azure SQL Database 必须允许连接到其他 Azure 服务，包括 Azure HDInsight。在 Azure SQL 数据库仪表板的右侧单击服务器名称。这是运行 SQL 数据库实例的服务器。进入服务器视图后，请单击"配置"，单击"Azure 服务"对应的"是"，然后单击"保存"。

**列出 HDInsight 群集**

- 在 Azure PowerShell 控制台窗口中运行以下命令，以列出 HDInsight 群集并验证是否已成功设置群集：

		Get-AzureHDInsightCluster -Name <ClusterName>


## <a id="cli"></a> 使用跨平台命令行

> [AZURE.NOTE] 自 2014 年 8 月 29 日起，跨平台命令行界面无法用于将群集与 Azure 虚拟网络相关联。

设置 HDInsight 群集的另一方法是使用跨平台命令行界面。该命令行工具在 Node.js 中实现。可以在支持 Node.js 的任意平台（包括 Windows、Mac 和 Linux）上使用它。可从以下位置安装 CLI：

- **Node.js SDK** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **跨平台 CLI** - <a href="https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

有关如何使用命令行界面的一般指南，请参阅[适用于 Mac 和 Linux 的 Azure 命令行工具](xplat-cli)。

以下说明将指导你在 Linux 和 Windows 上安装跨平台命令行，然后使用该命令行来设置群集。

- [设置适用于 Linux 的 Azure 跨平台命令行](#clilin)
- [设置适用于 Windows 的 Azure 跨平台命令行](#cliwin)
- [通过使用 Azure 跨平台命令行设置 HDInsight 群集](#cliprovision)

#### <a id="clilin"></a>设置适用于 Linux 的跨平台命令行

执行以下过程，将你的 Linux 计算机设置为使用 Azure 命令行工具：

- 通过使用 Node.js 程序包管理器 (NPM) 安装跨平台命令行
- 连接到你的 Azure 订阅

**通过使用 NPM 安装命令行界面**

1.	在 Linux 计算机上打开终端窗口，然后运行以下命令：

		sudo npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	运行以下命令以验证安装：

		azure hdinsight -h

	可以在不同级别使用 *-h* 开关以显示帮助信息。例如：

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**连接到 Azure 订阅**

在使用命令行界面前，你必须配置工作站和 Azure 之间的连接。命令行界面使用你的 Azure 订阅信息连接到你的帐户。可从 Azure 的发布设置文件中获取此信息。稍后可以导入发布设置文件作为永久性本地配置设置，命令行界面会将此设置用于后续操作。你只需导入你的发布设置一次。

> [AZURE.NOTE] 发布设置文件包含敏感信息。Microsoft 建议你删除该文件或采取其他措施来加密包含该文件的用户文件夹。在 Windows 上，修改文件夹属性或使用 BitLocker 驱动程序加密。 


1.	打开终端窗口。
2.	运行以下命令以登录到你的 Azure 订阅：

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	该命令将启动要从中下载发布设置文件的网页。如果网页未打开，请单击终端窗口中的链接以打开浏览器页并登录到该门户。 

3.	将发布设置文件下载到计算机。
5.	从命令提示符窗口，运行以下命令以导入发布设置文件：

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>设置适用于 Windows 的跨平台命令行

执行以下过程，将你的 Windows 计算机设置为使用 Azure 命令行工具：

- 安装跨平台命令行（通过使用 NPM 或 Windows 安装程序）
- 下载并导入 Azure 帐户发布设置


命令行界面可通过 NPM 或 Windows 安装程序来安装。Microsoft 建议你只使用这两个选项中的一个来进行安装。

**通过使用 NPM 安装命令行界面**

1.	浏览到 **www.nodejs.org**。
2.	单击**"安装"**，然后使用默认设置按照说明操作。
3.	从工作站打开**"命令提示符"**（或  *Azure Command Prompt*、 *Developer Command Prompt for VS2012*）。
4.	在命令提示符窗口中运行以下命令：

		npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE] 如果收到"未找到 NPM 命令"的错误消息，请验证以下路径位于 PATH 环境变量中：<i>C:\Program Files (x86)\nodejs; C:\Users\[username]\AppData\Roaming\npm</i> 或 <i>C:\Program Files\nodejs; C:\Users\[username]\AppData\Roaming\npm</i>
	
5.	运行以下命令以验证安装：

		azure hdinsight -h

	可以在不同级别使用 *-h* 开关以显示帮助信息。例如：
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**通过使用 Windows 安装程序安装命令行界面**

1.	浏览到 **http://www.windowsazure.cn/downloads/**。
2.	向下滚动到"命令行工具"部分，然后单击"跨平台命令行界面"，按 Web 平台安装程序向导的要求操作。

**下载和导入发布设置**

在使用命令行界面前，你必须配置工作站和 Azure 之间的连接。命令行界面使用你的 Azure 订阅信息连接到你的帐户。可从 Azure 的发布设置文件中获取此信息。稍后可以导入发布设置文件作为永久性本地配置设置，命令行界面会将此设置用于后续操作。你只需导入你的发布设置一次。

> [AZURE.NOTE] 发布设置文件包含敏感信息。Microsoft 建议你删除该文件或采取其他措施来加密包含该文件的用户文件夹。在 Windows 上，修改文件夹属性或使用 BitLocker。


1.	打开"命令提示符"。
2.	运行以下命令来下载发布设置文件：

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	该命令将启动要从中下载发布设置文件的网页。

3.	出现保存文件的提示时，请单击"保存"并提供文件的保存位置。
5.	从命令提示符窗口，运行以下命令以导入发布设置文件：

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>通过使用 Azure 跨平台命令行设置 HDInsight 群集

通过使用跨平台命令行设置 HDInsight 群集的步骤如下：

- 创建 Azure 存储帐户
- 设置群集

**创建 Azure 存储帐户**

HDInsight 使用 Azure Blob 存储容器作为默认文件系统。你需要先拥有 Azure 存储帐户，然后才能创建 HDInsight 群集。存储帐户必须位于同一数据中心内。

- 在命令提示符窗口中运行以下命令，以创建 Azure 存储帐户：

		azure storage account create [options] <StorageAccountName>

	出现指定位置的提示时，请选择 HDInsight 群集可以设置到的位置。该存储位置必须与 HDInsight 群集所在的位置相同。目前，只有**中国东部**和**中国北部**地区才能托管 HDInsight 群集。  

有关通过使用 Azure 门户创建 Azure 存储帐户的信息，请参阅[创建、管理或删除存储帐户](storage-create-storage-account)。

如果你已有存储帐户但是不知道帐户名称和帐户密钥，则可以使用以下命令来检索该信息：

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

有关通过使用 Azure 门户获取信息的详细信息，请参阅 *如何：查看、复制和重新生成存储访问密钥*部分（位于[创建、管理或删除存储帐户](storage-create-storage-account)中）。

HDInsight 群集还需要在存储帐户中提供一个容器。如果你提供的存储帐户尚不包含容器， *azure hdinsight cluster create* 命令将提示你输入容器名称，然后会创建该容器。但是，如果你想要预先创建容器，则可以使用以下命令：

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

准备好存储帐户和 Blob 容器后，你就可以创建群集了。

**设置 HDInsight 群集**

- 从命令提示符窗口，运行以下命令：

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.chinacloudapi.cn" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**通过使用配置文件设置 HDInsight 群集**

通常，你要设置 HDInsight 群集，运行作业，然后删除该群集以降低成本。在命令行界面上，你可以选择将配置保存到文件，以便在每次设置群集时重用这些配置。

- 在命令提示符窗口中运行以下命令：


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.chinacloudapi.cn" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.chinacloudapi.cn"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.chinacloudapi.cn"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.chinacloudapi.cn"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
		 
		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>
		 
	>[AZURE.NOTE] 用于元存储的 Azure SQL Database 必须允许连接到其他 Azure 服务，包括 Azure HDInsight。在 Azure SQL 数据库仪表板的右侧单击服务器名称。这是运行 SQL 数据库实例的服务器。进入服务器视图后，请单击"配置"，单击"Azure 服务"对应的"是"，然后单击"保存"。


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**列出和显示群集详细信息**

- 使用以下命令来列出和显示群集详细信息：

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>
	
	![HDI.CLIListCluster][image-cli-clusterlisting]


**删除群集**

- 使用以下命令来删除群集：

		azure hdinsight cluster delete <ClusterName>



## <a id="sdk"></a> 使用 HDInsight .NET SDK
HDInsight .NET SDK 提供 .NET 客户端库，可简化从 .NET Framework 应用程序使用 HDInsight 的操作。

通过使用 SDK 设置 HDInsight 群集时必须执行以下过程：

- 安装 HDInsight .NET SDK
- 创建自签名证书
- 创建控制台应用程序
- 运行应用程序


**安装 HDInsight .NET SDK**

你可以从 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安装该 SDK 的最新发行版。下一过程中将显示说明。

**创建自签名证书**

创建自签名证书，将其安装到工作站上，然后将其上传到你的 Azure 订阅。有关说明，请参阅[创建自签名证书](/documentation/articles/hdinsight-administer-use-management-portal/#cert)。 


**创建 Visual Studio 控制台应用程序**

1. 打开 Visual Studio 2013。

2. 在**"文件"**菜单中，单击**"新建"**，然后单击<b>"项目"</b>。

3. 在**"新建项目"**中，键入或选择以下值：

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. 单击"确定"以创建该项目。

5. 在"工具"菜单中，单击"Nuget Package Manager"，然后单击"Package Manager Console"。

6. 在控制台中运行下列命令以安装程序包：

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	此命令将 .NET 库以及对这些库的引用添加到当前 Visual Studio 项目中。

7. 在解决方案资源管理器中，双击 **Program.cs** 将其打开。

8. 将下列 using 语句添加到文件顶部。

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	
9. 在 Main() 函数中，复制并粘贴以下代码：
		
        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate you created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.chinacloudapi.cn";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. 替换 Main() 函数开头的变量。

**运行应用程序**

该应用程序在 Visual Studio 中打开时，按 **F5** 运行该应用程序。控制台窗口应打开并显示应用程序的状态。设置一个 HDInsight 群集可能需要几分钟时间。



## <a id="nextsteps"></a> 后续步骤
在本文中，你了解了几种设置 HDInsight 群集的方法。若要了解更多信息，请参阅以下文章：

* [开始使用 Azure HDInsight](hdinsight-get-started)  - 了解如何开始使用你的 HDInsight 群集
* [将 Sqoop 与 HDInsight 配合使用](hdinsight-use-sqoop) - 了解如何在 HDInsight 和 SQL Database 或 SQL Server 之间复制数据
* [使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell) - 了解如何通过 Azure PowerShell 使用 HDInsight
* [以编程方式提交 Hadoop 作业](hdinsight-submit-hadoop-jobs-programmatically) - 了解如何以编程方式将作业提交到 HDInsight
* [Azure HDInsight SDK 文档][hdinsight-sdk-documentation] - 探索 HDInsight SDK


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/zh-cn/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: /documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster
[hdinsight-get-started]: hdinsight-get-started
[hdinsight-storage]: hdinsight-use-blob-storage
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically
[hdinsight-powershell-reference]: http://msdn.microsoft.com/zh-cn/library/azure/dn479228.aspx
[hdinsight-storm-get-started]: hdinsight-storm-getting-started.md

[azure-management-portal]: https://manage.windowsazure.cn/

[azure-command-line-tools]: xplat-cli

[azure-create-storageaccount]: storage-create-storage-account

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: /pricing/purchase-options/
[azure-trial]: /pricing/1rmb-trial/
[hdi-remote]: /documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: install-configure-powershell

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png "Provide Hadoop HDInsight cluster details"
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png "Provide Hadoop HDInsight cluster users"
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png "Provide storage account details for Hadoop HDInsight cluster"
[image-customprovision-page5]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png "Provide additional storage account details for Hadoop HDInsight cluster"
[image-customprovision-page6]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png "Configure Script Action to customize an HDInsight cluster"


[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "将 Sqoop 与 HDInsight 配合使用"
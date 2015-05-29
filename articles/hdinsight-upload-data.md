<properties 
	pageTitle="在 HDInsight 中上载 Hadoop 作业的数据 | Azure" 
	description="了解如何使用 Azure Storage Explorer、Azure PowerShell、Hadoop 命令行或 Sqoop 在 HDInsight 中上载和访问数据。" 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	wacn.date="" 
	ms.author="jgao"/>



# 在 HDInsight 中上载 Hadoop 作业的数据

Azure HDInsight 在 Azure Blob 存储之上提供了一个功能完备的 Hadoop 分布式文件系统 (HDFS)。该系统设计为一个 HDFS 扩展，为客户提供无缝体验。它通过启用 Hadoop 生态系统中的整套组件以直接操作其管理的数据。Azure Blob 存储和 HDFS 是独立的文件系统，并且已针对数据的存储和计算进行了优化。有关使用 Azure Blob 存储的益处，请参阅[将 Azure Blob 存储与 HDInsight 配合使用][hdinsight-storage]。 

通常，为了执行 MapReduce 作业，会部署 Azure HDInsight 群集，一旦这些作业完成，就删除这些群集。在完成计算后将数据保存在 HDFS 群集中是一种成本很高的数据存储方法。对于将使用 HDInsight 处理的数据而言，Azure Blob 存储是一个高度可用的、可高度缩放的、大容量、低成本且可共享的存储选项。在 Blob 中存储数据，可以安全地释放用于计算的 HDInsight 群集而不丢失数据。 

可以通过 [AzCopy][azure-azcopy]、[Azure PowerShell][azure-powershell]、[用于 .NET 的 Azure 存储客户端库][azure-storage-client-library]、[Azure 跨平台命令行界面][xplatcli]或资源管理器工具访问 Azure Blob 存储。下面是一些可用的工具：

* [Azure 存储空间资源管理器](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure 资源管理器](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure 资源管理器专业版](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

## 先决条件

在开始下一步之前，请注意以下要求：

* 一个 Azure HDInsight 群集。有关说明，请参阅 [Azure HDInsight 入门][hdinsight-get-started]或[设置 HDInsight 群集][hdinsight-provision]。


## <a id="azcopy"></a>使用 AzCopy 将数据上载到 Azure Blob 存储##

AzCopy 是一个命令行工具，用于简化将数据传入和传出 Azure 存储帐户的任务。你可以将它用作独立的工具，也可以将此工具融入到现有应用程序中。[下载 AzCopy][azure-azcopy-download]。

AzCopy 语法为：

	AzCopy <Source> <Destination> [filePattern [filePattern...]][选项]

有关详细信息，请参阅 [AzCopy - 上载/下载 Azure Blob 的文件][azure-azcopy]。

## <a id="powershell"></a>使用 Azure PowerShell 将数据上载到 Azure Blob 存储##

Azure PowerShell 是一个功能强大的脚本编写环境，可用于在 Azure 中控制和自动执行工作负荷的部署和管理。你可以使用 Azure PowerShell 将数据上载到 Azure Blob 存储，以便 MapReduce 作业可以处理数据。有关配置工作站以运行 Azure PowerShell 的信息，请参阅[安装和配置 Azure PowerShell][powershell-install-configure]。

**将本地文件上载到 Azure Blob 存储**

1. 根据[安装和配置 Azure PowerShell][powershell-install-configure] 中的说明打开 Azure PowerShell 控制台。
2. 设置以下脚本中前五个变量的值：

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"
		
		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"
		
		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		# Copy the file from local workstation to the Blob container		
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
		
3. 将脚本粘贴到 Azure PowerShell 控制台中以运行此脚本。

Azure Blob 存储容器将数据存储为键值对，没有目录层次结构。不过，可在键名称中使用"/"字符，使其看起来像存储在目录结构中的文件。例如，Blob 的键可以是 *input/log1.txt*。不存在实际的"input"目录，但由于键名称中包含"/"字符，因此使其看起来像一个文件路径。在前一个脚本中，你可以通过设置 **$blobname** 变量为此文件提供一个文件夹结构，例如 *$blobname="myfolder/myfile.txt"*。

如果使用 Azure 资源管理器工具，你可以看到一些 0 字节的文件。这些文件有两个作用：

- 在有空文件夹的情况下，它们充当文件夹存在的标记。Azure Blob 存储足够聪明，可以了解如果存在名为 foo/bar 的 blob，就存在一个名为 **foo** 的文件夹。但如果你希望具有一个名为 **foo** 的空文件夹，则指明这一点的唯一方法是放入这个特殊的 0 字节文件。
- 它们具有 Hadoop 文件系统所需的一些特别的元数据，最明显的是文件夹的权限和所有者。


## <a id="xplatcli"></a>使用 Azure 跨平台命令行界面将数据上载到 Azure Blob 存储

Azure 跨平台命令行界面 (xplat-cli) 是一个跨平台工具，可用于管理 Azure 服务。使用以下步骤将数据上载到 Azure Blob 存储：

1. <a href="/documentation/articles/xplat-cli" target="_blank">安装并配置 xplat-cli</a>。

2. 打开命令提示符、bash 或其他 shell，然后使用以下方法对 Azure 订阅进行身份验证。

		azure login

	出现提示时，输入订阅的用户名和密码。

3. 使用以下命令，列出订阅的存储帐户：

		azure storage account list

4. 选择包含你要使用的 Blob 的存储帐户，然后使用以下命令检索此帐户的密钥：

		azure storage account keys list <storage-account-name>

	这应会返回**主**密钥和**辅助**密钥。复制**主**密钥值，因为后面的步骤要用到它。

5. 使用以下命令检索存储帐户中的 Blob 容器列表：

		azure storage container list -a <storage-account-name> -k <primary-key>

6. 使用以下命令可将文件上载和下载到 Blob：

	* 上载文件：

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* 下载文件：

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] 如果你始终使用同一个存储帐户，可以设置以下环境变量，而无需为每条命令指定帐户和密钥：
> 
> * **AZURE\_STORAGE\_ACCOUNT**：存储帐户名称
> 
> * **AZURE\_STORAGE\_ACCESS\_KEY**：存储帐户密钥

## <a id="storageexplorer"></a>使用 Azure 存储空间资源管理器将数据上载到 Blob 存储

*Azure 存储资源管理器* 是用于在 Azure 存储空间中检查和更改数据的有用工具。它是免费工具，可从 CodePlex：[Azure 存储空间资源管理器](http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer")下载。

使用该工具之前，你必须知道你的 Azure 存储帐户名和帐户密钥。有关如何获取此信息的说明，请参阅[创建、管理或删除存储帐户][azure-create-storageaccount]的"查看、复制和重新生成存储访问密钥"部分。  

1. 运行 Azure 存储空间资源管理器。

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. 单击"添加帐户"。在将帐户添加到 Azure 存储资源管理器后，你无需再执行该步骤。 

	![HDI.ASEAddAccount][image-ase-addaccount]

3. 输入"存储帐户名称"和"存储帐户密钥"，然后单击"添加存储帐户"。可以添加多个存储帐户，每个帐户均将显示在一个选项卡上。 
4. 在"存储类型"下，单击"Blob"。

	![HDI.ASEBlob][image-ase-blob]

5. 在"容器"下，单击与你的 HDInsight 群集关联的容器名称。创建 HDInsight 群集时，你必须指定一个容器。否则，群集创建过程将为你创建一个容器。
6. 在"Blob"下，单击"上载"。
7. 指定要上载的文件，然后单击"打开"。








































































## <a id="commandline"></a>使用 Hadoop 命令行将数据上载到 Azure Blob 存储空间

在使用 Hadoop 命令行之前，你必须使用远程桌面连接到群集： 



1. 登录到 [Azure 门户][azure-management-portal]。
2. 单击"HDInsight"。这将显示已部署的 Hadoop 群集的列表。
3. 单击要将数据上载到的 HDInsight 群集。
4. 单击页面顶部的"配置"。
5. 如果你尚未启用远程桌面，则单击"启用远程"，并按说明操作。否则，跳至下一步。
4. 单击页面底部的"连接"。
7. 单击"打开"。
8. 输入你的凭据，然后单击"确定"。
9. 单击"是"。
10. 在桌面上单击"Hadoop 命令行"。
12. 以下代码示例演示如何将 davinci.txt 文件从 HDInsight 头节点上的本地文件系统复制到 /example/data 目录。

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	由于默认文件系统在 Azure Blob 存储中，/example/datadavinci.txt 实际是在 Azure Blob 存储中。你也可以将该文件表示为：

		wasb:///example/data/davinci.txt 

	或

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.chinacloudapi.cn/example/data/davinci.txt

	在使用 *wasbs* 时，完全限定的域名是必需的。

13. 使用以下命令列出已上载的文件：

		hadoop dfs -lsr /example/data


## <a id="sqoop"></a>使用 Sqoop 将数据从 Azure SQL Database 或 SQL Server 导入到 HDFS

Sqoop 是一种为在 Hadoop 和关系数据库之间传输数据而设计的工具。可以使用此工具将数据从关系数据库管理系统 (RDBMS)（如 SQL Server、MySQL 或 Oracle）中导入到 Hadoop 分布式文件系统 (HDFS)，在 Hadoop 中使用 MapReduce 或 Hive 转换数据，然后回过来将数据导出到 RDBMS。有关详细信息，请参阅 [Sqoop 用户指南][apache-sqoop-guide]。

在导入数据前，你必须知道托管 Azure SQL 数据库的服务器名称、数据库帐户名称、帐户密码和数据库名称。 

默认情况下，可以从 Azure HDInsight 这样的 Azure 服务连接 Azure SQL 数据库。如果禁用了此防火墙设置，则必须从 Azure 门户启用它。有关创建 Azure SQL Database 和配置防火墙规则的说明，请参阅[创建和配置 SQL Database][sqldatabase-create-configure]。 

以下过程使用 Azure PowerShell 提交 Sqoop 作业。 

**使用 Sqoop 和 Azure PowerShell 将数据导入到 HDInsight**

1. 根据[安装和配置 Azure PowerShell][powershell-install-configure] 中的说明打开 Azure PowerShell 控制台。
2. 在以下脚本中设置前八个变量的值：

		$subscriptionName = "<AzureSubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
		$tableName = "<SQLDatabaseTableName>"
		
		$hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".
		
		Select-AzureSubscription $subscriptionName		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.chinacloudapi.cn;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1"

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3. 将脚本粘贴到 Azure PowerShell 控制台中以运行此脚本。请参阅 [HDInsight 入门][hdinsight-get-started]，以获取用于从 Azure Blob 存储检索数据文件的示例。

有关使用 Sqoop 的详细信息，请参阅[将 Sqoop 与 HDInsight 配合使用][hdinsight-use-sqoop]。

## 后续步骤
现在，你已了解如何将数据导入 HDInsight，请阅读以下文章了解如何执行分析：

* [Azure HDInsight 入门][hdinsight-get-started]
* [以编程方式提交 Hadoop 作业][hdinsight-submit-jobs]
* [将 Hive 与 HDInsight 配合使用][hdinsight-use-hive]
* [将 Pig 与 HDInsight 配合使用][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.cn
[azure-powershell]: http://msdn.microsoft.com/zh-cn/library/azure/jj152841.aspx

[azure-storage-client-library]: /documentation/articles/storage-dotnet-how-to-use-blobs
[azure-create-storage-account]: /documentation/articles/storage-create-storage-account/
[azure-azcopy-download]: storage-use-azcopy
[azure-azcopy]: storage-use-azcopy

[hdinsight-use-sqoop]: /documentation/articles/hdinsight-use-sqoop/

[hdinsight-storage]: /documentation/articles/hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: /documentation/articles/hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-get-started]: /documentation/articles/hdinsight-get-started/

[hdinsight-use-hive]: /documentation/articles/hdinsight-use-hive/
[hdinsight-use-pig]: /documentation/articles/hdinsight-use-pig
[hdinsight-provision]: /documentation/articles/hdinsight-provision-clusters/

[sqldatabase-create-configue]: /documentation/articles/sql-database-create-configure/

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /documentation/articles/install-configure-powershell/

[xplatcli]: xplat-cli


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

<!---HONumber=56-->
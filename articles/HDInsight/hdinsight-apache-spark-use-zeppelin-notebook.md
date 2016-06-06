<!-- not suitable for Mooncake -->

<properties 
	pageTitle="在 HDInsight Linux 上的 Spark 群集中使用 Zeppelin 笔记本 | Azure" 
	description="逐步说明如何在 HDInsight Linux 上的 Spark 群集中使用 Zeppelin 笔记本。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.date="05/16/2016"
	wacn.date=""/>


# 在 HDInsight Linux 上的 Spark 群集中使用 Zeppelin 笔记本（预览版）

了解如何在 Spark 群集上安装 Zeppelin 笔记本，以及如何使用 Zeppelin 笔记本。

> [AZURE.IMPORTANT] 适用于 HDInsight Spark 群集的 Zeppelin 笔记本产品只是为了展示如何在 Azure HDInsight Spark 环境中使用 Zeppelin。如果你要使用笔记本来操作 HDInsight Spark，我们建议改用 Jupyter 笔记本。Jupyter 笔记本还提供不同的内核选项（例如 Scala），并且功能会不断改善。有关如何使用 Jupyter 笔记本和 HDInsight Spark 的说明，请参阅 [Run Spark SQL queries using a Jupyter notebook（使用 Jupyter 笔记本运行 Spark SQL 查询）](/documentation/articles/hdinsight-apache-spark-jupyter-spark-sql#jupyter)。

**先决条件：**

* 在开始学习本教程之前，你必须有一个 Azure 订阅。请参阅[获取 Azure 试用版](/pricing/1rmb-trial/)。
* Apache Spark 群集。有关说明，请参阅 [Create Apache Spark clusters in Azure HDInsight（在 Azure HDInsight 中创建 Apache Spark 群集）](/documentation/articles/hdinsight-provision-clusters-v1)。
* SSH 客户端。对于 Linux 和 Unix 分发版或 Macintosh OS X，操作系统已随附 `ssh` 命令。对于 Windows，我们建议使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

	> [AZURE.NOTE] 如果想要使用 `ssh` 或 PuTTY 以外的 SSH 客户端，请参阅客户端的文档，以了解如何建立 SSH 隧道。

* 可配置为使用 SOCKS 代理的 Web 浏览器

* __（可选）__：类似于 [FoxyProxy](http://getfoxyproxy.org/,) 的插件，它可以应用只会通过隧道路由特定请求的规则。

	> [AZURE.WARNING] 如果不使用类似于 FoxyProxy 的插件，则可以通过隧道路由所有通过浏览器发出的请求。这可能导致浏览器中的网页加载速度变慢。

## 在创建群集过程中安装 Zeppelin

可以使用脚本操作在 Spark 群集上安装 Zeppelin。脚本操作使用自定义脚本在群集上安装默认情况下未提供的组件。可以使用自定义脚本，通过 Azure 门户、HDInsight .NET SDK 或 Azure PowerShell 安装 Zeppelin。

### 使用 Azure 门户

有关如何使用 HDInsight .NET SDK 运行脚本操作以安装 Zeppelin 的说明，请参阅 [Customize HDInsight clusters using Script Action（使用脚本操作自定义 HDInsight 群集）](/documentation/articles/hdinsight-hadoop-customize-cluster-v1#use-a-script-action-from-the-azure-portal)。必须对该文章中的说明做出一些更改。

* 必须使用脚本来安装 Zeppelin。用于在 HDInsight 中的 Spark 群集上安装 Zeppelin 的自定义脚本可从以下链接获取：
	* 对于 Spark 1.6.0 群集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
	* 对于 Spark 1.5.2 群集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* 只能在头节点上运行脚本操作。

* 该脚本不需要任何参数。

### 使用 HDInsight .NET SDK

有关如何使用 HDInsight .NET SDK 运行脚本操作以安装 Zeppelin 的说明，请参阅 [Customize HDInsight clusters using Script Action（使用脚本操作自定义 HDInsight 群集）](/documentation/articles/hdinsight-hadoop-customize-cluster-v1#use-a-script-action-from-the-hdinsight-net-sdk)。必须对该文章中的说明做出一些更改。

* 必须使用脚本来安装 Zeppelin。用于在 HDInsight 中的 Spark 群集上安装 Zeppelin 的自定义脚本可从以下链接获取：
	* 对于 Spark 1.6.0 群集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
	* 对于 Spark 1.5.2 群集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* 该脚本不需要任何参数。

* 将你要创建的群集类型设置为 Spark。

### 使用 Azure PowerShell

使用以下 PowerShell 代码段在已安装 Zeppelin 的 HDInsight Linux 上创建 Spark 群集。根据所用的 Spark 群集版本，必须更新以下 PowerShell 代码段，以包含相应自定义脚本的链接。

* 对于 Spark 1.6.0 群集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* 对于 Spark 1.5.2 群集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[AZURE.INCLUDE [upgrade-powershell](../includes/hdinsight-use-latest-powershell.md)]


	Login-AzureRMAccount
	
	# PROVIDE VALUES FOR THE VARIABLES
	$clusterAdminUsername="admin"
	$clusterAdminPassword="<<password>>"
	$clusterSshUsername="adminssh"
	$clusterSshPassword="<<password>>"
	$clusterName="<<clustername>>"
	$clusterContainerName=$clusterName
	$resourceGroupName="<<resourceGroupName>>"
	$location="<<region>>"
	$storage1Name="<<storagename>>"
	$storage1Key="<<storagekey>>"
	$subscriptionId="<<subscriptionId>>"
	
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	$passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
	$clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
	$passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
	$clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
	
	$azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
	$azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
	$azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.chinacloudapi.cn"
	
	Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
	
	New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## 设置 SSH 隧道以访问 Zeppelin 笔记本

你将使用 SSH 隧道来访问 HDInsight Linux 中 Spark 群集上运行的 Zeppelin 笔记本。以下步骤演示如何使用 ssh 命令行 (Linux) 和 PuTTY (Windows) 来创建 SSH 隧道。

### 使用 SSH 命令创建隧道 (Linux)

使用以下 `ssh` 命令创建 SSH 隧道。将 __USERNAME__ 替换为 HDInsight 群集的 SSH 用户，并将 __CLUSTERNAME__ 替换为 HDInsight 群集的名称

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.cn

这会创建一个通过 SSH 将流量路由到群集本地端口 9876 的连接。选项包括：

* **D 9876** - 通过隧道路由流量的本地端口。

* **C** - 压缩所有数据，因为 Web 流量大多为文本。

* **2** - 强制 SSH 仅尝试协议版本 2。

* **q** - 静默模式。

* **T** - 禁用 pseudo-tty 分配，因为我们只要转发端口。

* **n** - 防止读取 STDIN，因为我们将仅转发端口。

* **N** - 不执行远程命令，因为我们只要转发端口。

* **f** - 在后台运行。

如果使用 SSH 密钥配置了群集，则可能需要使用 `-i` 参数，并指定 SSH 私钥的路径。

在命令完成后，发送到本地计算机上的端口 9876 的流量将通过安全套接字层 (SSL) 路由到群集头节点，并显示为源于此处。

### 使用 PuTTY 创建隧道 (Windows)

执行以下步骤使用 PuTTY 创建 SSH 隧道。

1. 打开 PuTTY 并输入你的连接信息。如果你不熟悉 PuTTY，请参阅 [Use SSH with Linux-based Hadoop on HDInsight from Windows（在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用）](/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows)，以了解有关如何配合 HDInsight 使用 PuTTY 的信息。

2. 在对话框左侧的“类别”部分中，依次展开“连接”和“SSH”，然后选择“隧道”。

3. 提供以下有关“用于控制 SSH 端口转发的选项”窗体的信息：

	* **源端口** - 客户端上要转发的端口。例如 **9876**。

	* **目标** - 基于 Linux 的 HDInsight 群集的 SSH 地址。例如 **mycluster-ssh.azurehdinsight.cn**。

	* **动态** - 启用动态 SOCKS 代理路由。

	![隧道选项图像](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. 单击“添加”以添加设置，然后单击“打开”以打开 SSH 连接。

5. 出现提示时，登录到服务器。这将会建立 SSH 会话并启用隧道。

### 从浏览器使用隧道

> [AZURE.NOTE] 本部分中的步骤使用 FireFox 浏览器，因为它在 Linux、Unix、Macintosh OS X 和 Windows 系统上均可任意使用。其他现代浏览器（如 Google Chrome、Microsoft Edge 或 Apple Safari）应该也可以正常运行；但是，某些步骤中使用的 FoxyProxy 插件不一定适用于所有浏览器。

1. 将浏览器配置为使用 **localhost:9876** 作为 **SOCKS v5** 代理。Firefox 中的设置如下所示。如果使用的端口不是 9876，请将端口更改为所用的端口：

	![Firefox 设置图像](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

	> [AZURE.NOTE] 选择“远程 DNS”将通过使用 HDInsight 群集解析域名系统 (DNS) 请求。如果未将其选中，则将在本地解析 DNS。

2. 在 Firefox 中启用和禁用代理设置的情况下访问某个站点（例如 [http://www.whatismyip.com/](http://www.whatismyip.com/)），以验证是否能够通过隧道路由流量。启用这些设置时，IP 地址将是 Azure 数据中心内某台计算机的地址。

### 浏览器扩展

虽然你可以将浏览器配置为使用隧道，但是，你通常不想要通过隧道路由所有流量。浏览器扩展，例如 [FoxyProxy](http://getfoxyproxy.org/)，支持 URL 请求的模式匹配（仅限 FoxyProxy Standard 或 Plus 版），以便只有特定 URL 的请求才通过隧道发送。

如果你已安装 FoxyProxy Standard，请使用以下步骤将其配置为仅通过隧道转发 HDInsight 的流量。

1. 在浏览器中打开 FoxyProxy 扩展。例如，在 Firefox 中，选择地址字段旁边的 FoxyProxy 图标。

	![foxyproxy 图标](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. 选择“添加新代理”，再选择“常规”选项卡，然后输入代理名称 **HDInsightProxy**。

	![foxyproxy 常规](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. 选择“代理详细信息”选项卡并填充以下字段。

	* **主机或 IP 地址** - 这是 localhost，因为我们要在本地计算机上使用 SSH 隧道。

	* **端口** - 这是用于 SSH 隧道的端口。

	* **SOCKS 代理** - 选择此项可让浏览器使用隧道作为代理。

	* **SOCKS v5** - 选择此项可设置代理所需的版本。

	![foxyproxy 代理](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. 选择“URL 模式”选项卡，然后选择“添加新模式”。使用以下设置来定义模式，然后单击“确定”：

	* **模式名称** - **zeppelinnotebook** - 模式的友好名称。

	* **URL 模式** - ***hn0*** - 定义与 Zeppelin 笔记本所在终结点的内部完全限定域名匹配的模式。由于 Zeppelin 笔记本仅适用于群集的 headnode0，并且终结点通常是 `http://hn0-<string>.internal.chinacloudapp.cn`，因此使用模式 **hn0** 可确保将请求重定向到 Zeppelin 终结点。

		![foxyproxy 模式](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. 单击“确定”以添加代理，并关闭“代理设置”。

5. 在“FoxyProxy”对话框的顶部，将“选择模式”更改为“根据其预定义模式和优先级使用代理”，然后单击“关闭”。

	![foxyproxy 选择模式](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

执行这些步骤后，只会通过 SSL 隧道路由包含字符串 __internal.chinacloudapp.cn__ 的 URL 的请求。

## 访问 Zeppelin 笔记本

设置 SSH 隧道后，可以遵循以下步骤，在 Spark 群集上访问 Zeppelin 笔记本。

1. 从 Web 浏览器打开以下终结点：

		http://hn0-myspar:9995

	* **hn0** 表示 headnode0
	* **myspar** 是 Spark 群集名称的前六个字母。
	* **9995** 是可用于访问 Zeppelin 笔记本的端口。

2. 创建新的笔记本。在标题窗格中单击“笔记本”，然后单击“创建新笔记”。

	![创建新的 Zeppelin 笔记本](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "创建新的 Zeppelin 笔记本")

	在同一页面上的“笔记本”标题下面，你应会看到名称以“笔记 XXXXXXXXX”开头的新笔记本。单击该新笔记本。

3. 在新笔记本的网页上单击标题，并根据需要更改笔记本的名称。按 ENTER 保存名称更改。此外，请确保笔记本标题在右上角显示“已连接”状态。

	![Zeppelin 笔记本状态](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Zeppelin 笔记本状态")

4. 将示例数据载入临时表。当你在 HDInsight 中创建 Spark 群集时，系统会将示例数据文件 **hvac.csv** 复制到 **\\HdiSamples\\SensorSampleData\\hvac** 下的关联存储帐户。

	将以下代码段粘贴到新笔记本中默认创建的空白段落处。

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	按 **SHIFT + ENTER** 或单击“播放”按钮，使段落运行代码段。段落右上角的状态应从“就绪”逐渐变成“挂起”、“正在运行”和“已完成”。输出将显示在同一段落的底部。屏幕截图如下所示：

	![基于原始数据创建临时表](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "基于原始数据创建临时表")

	你也可以为每个段落提供标题。在右下角单击“设置”图标，然后单击“显示标题”。

5. 现在可以针对 **hvac** 表运行 Spark SQL 语句。将以下查询粘贴到新段落中。该查询将检索建筑物 ID，以及在给定日期当天每栋建筑物的目标温度与实际温度之间的差异。按 **SHIFT + ENTER**。

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	开头的 **%Sql** 语句告诉笔记本要使用Spark SQL 解释程序。你可以从笔记本标题中的“解释程序”选项卡查看已定义的解释程序。

	以下屏幕快照显示了输出。

	![使用笔记本运行 Spark SQL 语句](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "使用笔记本运行 Spark SQL 语句")

	 单击显示选项（以矩形突出显示）以针对相同输出切换不同的表示形式。单击“设置”以选择构成输出中的密钥和值的项。以上屏幕截图使用 **buildingID** 作为密钥，使用 **temp\_diff** 平均值作为值。

	
6. 你还可以在查询中使用变量来运行 Spark SQL 语句。下一个代码段演示如何在查询中使用你可以用来查询的值定义 **Temp** 变量。当你首次运行查询时，下拉列表中会自动填充你指定的变量值。

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	将此代码段粘贴到新段落，然后按 **SHIFT + ENTER**。以下屏幕快照显示了输出。

	![使用笔记本运行 Spark SQL 语句](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "使用笔记本运行 Spark SQL 语句")

	对于后续查询，可以从下拉列表中选择新的值，然后再次运行查询。单击“设置”以选择构成输出中的密钥和值的项。以上屏幕截图使用 **buildingID** 作为密钥，使用 **temp\_diff** 平均值作为值，使用 **targettemp** 作为组。

7. 重新启动 Spark SQL 解释程序以退出应用程序。单击顶部的“解释程序”选项卡，然后针对 Spark 解释程序单击“重新启动”。

	![重新启动 Zeppelin 解释程序](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "重新启动 Zeppelin 解释程序")


## <a name="seealso"></a>另请参阅


* [概述：Azure HDInsight 上的 Apache Spark](/documentation/articles/hdinsight-apache-spark-overview)

### 方案

* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](/documentation/articles/hdinsight-apache-spark-use-bi-tools)

* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](/documentation/articles/hdinsight-apache-spark-ipython-notebook-machine-learning)

* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](/documentation/articles/hdinsight-apache-spark-machine-learning-mllib-ipython)

* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](/documentation/articles/hdinsight-apache-spark-eventhub-streaming)

* [使用 HDInsight 中的 Spark 分析网站日志](/documentation/articles/hdinsight-apache-spark-custom-library-website-log-analysis)

### 创建和运行应用程序

* [使用 Scala 创建独立的应用程序](/documentation/articles/hdinsight-apache-spark-create-standalone-application)

* [使用 Livy 在 Spark 群集中远程运行作业](/documentation/articles/hdinsight-apache-spark-livy-rest-interface)

### 工具和扩展

* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](/documentation/articles/hdinsight-apache-spark-intellij-tool-plugin)

* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](/documentation/articles/hdinsight-apache-spark-jupyter-notebook-kernels)

### 管理资源

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](/documentation/articles/hdinsight-apache-spark-resource-manager)


[hdinsight-versions]: /documentation/articles/hdinsight-component-versioning-v1
[hdinsight-upload-data]: /documentation/articles/hdinsight-upload-data
[hdinsight-storage]: /documentation/articles/hdinsight-hadoop-use-blob-storage

[azure-purchase-options]: /pricing/overview/
[azure-member-offers]: /pricing/member-offers/
[azure-trial]: /pricing/1rmb-trial/
[azure-management-portal]: https://manage.windowsazure.cn/
[azure-create-storageaccount]: /documentation/articles/storage-create-storage-account








<!---HONumber=Mooncake_0530_2016-->
<properties 
   pageTitle="在 HDInsight 中开始将 Hadoop 与 Hive 配合使用 | Azure" 
   description="在 HDInsight 中开始使用 Hadoop - 云中的大数据解决方案。了解如何设置群集、使用 Hive 查询数据，以及将数据输出到 Excel 以进行分析。" 
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
   ms.date="03/16/2015" 
   wacn.date="" 
   ms.author="nitinme"/>


# 在 Windows 上的 HDInsight 中开始将 Hadoop 与 Hive 配合使用

> [AZURE.SELECTOR]
- [Windows](hdinsight-get-started)
- [Linux](hdinsight-hadoop-linux-get-started)

为了帮助你快速开始使用 HDInsight，本教程说明了如何运行 Hive 查询以从 Hadoop 群集的非结构化数据中提取有关手机使用情况的有用信息。然后，你将在 Microsoft Excel 中分析结果。


> [AZURE.NOTE] 如果你是 Hadoop 和大数据的新手，可以先阅读有关术语 [Apache Hadoop][apache-hadoop]、[MapReduce][apache-mapreduce]、[HDFS][apache-hdfs] 和 [Hive][apache-hive] 的详细信息。若要了解 HDInsight 如何在 Azure 中启用 Hadoop，请参阅 [HDInsight 中的 Hadoop 简介][hadoop-hdinsight-intro]

Microsoft 还提供了 HDInsight Emulator for Azure（以前称作 *Microsoft HDInsight 开发者预览版*），与 Azure HDInsight 的通用版本结合使用。Emulator 针对开发人员方案，仅支持单节点部署。有关如何使用 HDInsight Emulator 的信息，请参阅 [HDInsight Emulator 入门][hdinsight-emulator]。

> [AZURE.NOTE] 有关如何设置 HBase 群集的说明，请参阅[在 HDInsight 中设置 HBase 群集][hdinsight-hbase-custom-provision]。请参阅 <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Hadoop 与 HBase 之间的差别</a>，以了解为何要选择其中的某一种数据库。   

## 本教程的目标是什么？ ##

假设你具有一个大型非结构化数据集，想要对其运行查询以提取一些有意义的信息。这正是我们在本教程中将要实现的目标。下面说明了如何实现此目标：

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Excel.][image-hdi-getstarted-flow]

你也可以观看本教程的演示视频：



<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target="_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->



**先决条件：**

在开始阅读本教程前，你必须具有：


- Azure 订阅。有关获取订阅的详细信息，请参阅[购买选项][azure-purchase-options]和[免费试用][azure-trial]。
- 装有 Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone 或 Office 2010 Professional Plus 的计算机。

**本教程预计完成时间：**30 分钟

## 本教程的内容

* [创建 Azure 存储帐户](#storage)
* [设置 HDInsight 群集](#provision)
* [从门户运行示例](#sample)
* [运行 HIVE 作业](#hivequery)
* [后续步骤](#nextsteps)

## <a name="storage"></a>创建 Azure 存储帐户

HDInsight 使用 Azure Blob 存储来存储数据。有关详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用][hdinsight-storage]。

在设置 HDInsight 群集时，你已指定了一个 Azure 存储帐户。将该帐户的一个特定 Blob 容器指定为默认文件系统，如同在 Hadoop 分布式文件系统 (HDFS) 中一样。默认情况下，HDInsight 群集与你指定的存储帐户设置在同一数据中心内。

>[AZURE.NOTE] 请不要多个 HDInsight 群集之间共享默认的 Blob 存储容器。 

除此存储帐户外，你在自定义配置 HDInsight 群集时还可以添加其他存储帐户。附加的此存储帐户可以来自同一 Azure 订阅，也可以来自不同的 Azure 订阅。有关说明，请参阅[使用自定义选项设置 HDInsight 群集][hdinsight-provision]。 

为简化本教程，仅使用了默认 Blob 和默认存储帐户。而在实践中，数据文件通常存储在指定的存储帐户中。

**创建 Azure 存储帐户**

1. 登录到 [Azure 门户][azure-management-portal]。
2. 单击左下角的"新建"，指向"数据服务"，指向"存储"，然后单击"快速创建"。

	![Azure portal where you can use Quick Create to set up a new storage account.][image-hdi-storageaccount-quickcreate]

3. 输入"URL"、"位置"和"复制"信息，然后单击"创建存储帐户"。（不支持地缘组。）你将在存储列表中看到新的存储帐户。

	>[AZURE.NOTE]  用于设置 HDInsight 群集的"快速创建"选项（与我们在本教程中使用的一样）在设置群集时并不要求提供位置。默认情况下，它将群集与存储帐户一起放在同一数据中心。因此，确保在群集支持的位置中创建存储帐户。这些位置包括：**中国东部**、**中国北部**。

4. 等到新存储帐户的"状态"更改为"联机"。
5. 从列表中选择新存储帐户，然后单击页面底部的"管理访问密钥"。
7. 记下"存储帐户名称"和"主访问密钥"（或"辅助访问密钥"- 任一密钥都有效）。本教程后面的步骤中将会用到它们。


有关详细信息，请参阅
[如何创建存储帐户][azure-create-storageaccount]和[将 Azure Blob 存储与 HDInsight 配合使用][hdinsight-storage]。
	
## <a name="provision"></a>设置 HDInsight 群集

当你设置 HDInsight 群集时，便设置了包含 Hadoop 和相关应用程序的 Azure 计算资源。在此部分中，设置基于 Hadoop 版本 2.4 的 HDInsight 版本 3.1 群集。你还可以使用 Azure 门户、HDInsight PowerShell cmdlet 或 HDInsight .NET SDK 为其他版本创建 Hadoop 群集。有关说明，请参阅[使用自定义选项设置 HDInsight 群集][hdinsight-provision]。有关 HDInsight 版本及其 SLA 的信息，请参阅 [HDInsight 组件版本](hdinsight-component-versioning)。

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**设置 HDInsight 群集**

1. 登录到 [Azure 门户][azure-management-portal]。 

2. 单击左窗格中的"HDInsight"以便列出你的帐户中群集的状态。在下面的屏幕快照中，没有现有的 HDInsight 群集。

	![Status of HDInsight clusters in the Azure portal.][image-hdi-clusterstatus]

3. 单击左下角的"新建"，然后依次单击"数据服务"、"HDInsight"和"Hadoop"。

	![Creation of a Hadoop cluster in HDInsight.][image-hdi-quickcreatecluster]

4. 输入或选择以下值：

	<table border="1">
	<tr><th>名称</th><th>值</th></tr>
	<tr><td>群集名称</td><td>群集的名称。</td></tr>
	<tr><td>群集大小</td><td>要部署的数据节点的数目。默认值为 4。但是，下拉列表中还提供了使用 1 或 2 个数据节点的选项。可以使用"自定义创建"选项指定任意数量的群集节点<strong></strong>。提供了有关各种群集大小的计帐费率的定价详细信息。单击下拉框正上方的 <strong>?</strong> 符号并访问弹出框上的链接。</td></tr>
	<tr><td>密码</td><td><i>admin</i> 帐户的密码。如果使用的不是"自定义创建"<strong></strong>选项，则指定群集用户名"admin"。请注意，这不是设置群集所在的 VM 的 Windows Administrator 帐户。帐户名称可以通过使用"自定义创建"<strong></strong>向导来更改。</td></tr>
	<tr><td>存储帐户</td><td>从下拉框中选择你所创建的存储帐户。 <br/>

	选择存储帐户后，就不能更改它。如果删除了存储帐户，则群集将不再可用。

	HDInsight 群集与存储帐户位于同一数据中心中。 
	</td></tr>
	</table>

	记下群集名称。本教程后面的步骤中将会用到它。

	
5. 单击"创建 HDInsight 群集"。完成设置后，状态列将显示"正在运行"。

	>[AZURE.NOTE] 上述过程通过 HDInsight 版本 3.1 来创建群集。若要使用其他版本创建群集，请使用门户中的"自定义"创建方法，或使用 Azure PowerShell。有关各版本之间的差异的信息，请参阅 [HDInsight 提供的群集版本有哪些新功能？][hdinsight-versions]。有关使用"自定义创建"选项的信息，请参阅[使用自定义选项设置 HDInsight 群集][hdinsight-provision]。


## <a name="sample"></a>从门户运行示例

成功设置的 HDInsight 群集提供包括入门库的查询控制台以直接从门户运行示例。通过浏览一些基本方案，你可以使用示例了解如何使用 HDInsight。这些示例提供所有必要组件，比如要分析的数据和要对数据运行的查询。若要了解有关入门库中的示例的详细信息，请参阅[使用 HDInsight 入门库了解 HDInsight 中的 Hadoop](hdinsight-learn-hadoop-use-sample-gallery)。

**若要运行示例**，请从 Azure 门户中单击你想要运行示例的群集名称，然后单击页面底部的"查询控制台"。从打开的网页中，单击"入门库"选项卡，然后在"示例"类别下，单击要运行的示例。按照网页上的说明完成示例。下表列出了几个示例，并提供了有关每个示例的作用的详细信息。

示例 | 示例的作用
------ | ---------------
[传感器数据分析][hdinsight-sensor-data-sample] | 了解如何使用 HDInsight 处理加热、通风和空调 (HVAC) 系统产生的历史数据，以识别无法可靠地保持设定温度的系统
[网站日志分析][hdinsight-weblogs-sample] | 了解如何使用 HDInsight 分析网站日志文件，以了解一天中从外部网站对该网站的访问次数，以及用户遇到的网站错误汇总


## <a name="hivequery"></a>从门户运行 HIVE 查询
现在，你的 HDInsight 群集已设置完毕，下一步是运行 Hive 作业以查询示例 Hive 表。我们将使用 HDInsight 群集随附的 *hivesampletable*。该表包含有关移动设备制造商、平台和型号的数据。我们查询该表以按特定制造商检索移动设备的数据。

> [AZURE.NOTE] HDInsight Tools for Visual Studio 随附了 Azure SDK for .NET 2.5 或更高版本。使用 Visual Studio 中的工具可以连接到 HDInsight 群集、创建 Hive 表和运行 Hive 查询。有关详细信息，请参阅 [HDInsight Hadoop Tools for Visual Studio 入门][1]。

**从群集仪表板运行 Hive 作业**

1. 登录到 [Azure 门户][azure-management-portal]。 
2. 单击左窗格中的"HDINSIGHT"。你将会看到所创建的群集的列表，包括你刚刚在上一部分中创建的群集。
3. 单击要用于运行 Hive 作业的群集名称，然后单击页面底部的"查询控制台"。 
4. 这会在另一个浏览器选项卡中打开一个网页。输入 Hadoop 用户帐户和密码。默认用户名为 **admin**；密码是你在设置群集时所输入的密码。仪表板类似于：

	![Hive Editor tab in the HDInsight cluster dashboard.][img-hdi-dashboard]

	页面顶部有多个选项卡。默认选项卡为"Hive 编辑器"，而其他选项卡为"作业历史记录"和"文件浏览器"。使用仪表板可以提交 Hive 查询、检查 Hadoop 作业日志，以及浏览 WASB 文件。

	> [AZURE.NOTE] 请注意，网页的 URL 为 *&lt;ClusterName&gt;.azurehdinsight.net*。因此，如果不从门户打开仪表板，也可以在 Web 浏览器中使用 URL 打开仪表板。

6. 在"Hive 编辑器"选项卡上，为"查询名称"输入 **HTC20**。查询名称为作业标题。

7. 在查询窗格中输入以下查询： 

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![Query entered in the query pane of the Hive Editor.][img-hdi-dashboard-query-select]

4. 单击"提交"。片刻之后即可返回结果。屏幕将每隔 30 秒刷新一次。你也可以单击"刷新"来刷新屏幕。

    完成后，屏幕将如下所示：

	![Results from a Hive query in listed at the bottom of the cluster dashboard.][img-hdi-dashboard-query-select-result]

5. 单击屏幕上的查询名称以查看输出。记下"作业开始时间(UTC)"。稍后需要用到此值。 

    ![Job Start Time listed in the Job History tab of the HDInsight cluster dashboard.][img-hdi-dashboard-query-select-result-output]

    该页面还显示"作业输出"和"作业日志"。你也可以选择下载输出文件 (\_stdout) 和日志文件 \(_stderr)。


	> [AZURE.NOTE] 只要停留在"Hive 编辑器"选项卡上，该选项卡上的"作业会话"表便会列出已完成或正在运行的作业。如果导航离开页面，该表就不列出任何作业。"作业历史记录"选项卡保留所有作业（已完成或正在运行）的列表。
 

**浏览到输出文件**

1. 在群集仪表板中，单击"文件浏览器"。 
2. 单击你的存储帐户名称，单击容器名称（与群集名称相同），然后单击"用户"。
3. 单击 **admin**，然后单击其上次修改时间比你前面记下的作业开始时间稍晚的 GUID。复制此 GUID。在后一个部分将要用到它。


   	![The output file GUID listed in the File Browser tab.][img-hdi-dashboard-query-browse-output]


### <a name="powerquery"></a>连接到 Microsoft 商业智能工具 

你可以使用 Microsoft Excel 的 Power Query 附加组件将作业输出从 HDInsight 导入到 Excel 中，从中可以使用 Microsoft 商业智能工具进一步分析结果。 

要完成本部分教程，必须已安装 Excel 2013 或 2010。 

**下载 Microsoft Power Query for Excel**

- 从 [Microsoft 下载中心](http://www.microsoft.com/zh-cn/download/details.aspx?id=39379)下载 Microsoft Power Query for Microsoft Excel 并将其安装。

**导入 HDInsight 数据**

1. 打开 Excel，然后创建一个新的工作簿。
3. 依次单击"Power Query"菜单、"从其他源"和"从 Azure HDInsight"。

	![Excel PowerQuery Import menu open for Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. 输入与你的群集关联的 Azure Blob 存储帐户的"帐户名称"，然后单击"确定"。（这是先前在教程中创建的存储帐户。）
4. 输入 Azure Blob 存储帐户的"帐户密钥"，然后单击"保存"。 
5. 在右窗格中，双击 Blob 名称。默认情况下，该 Blob 名称与群集名称相同。 

6. 在"名称"列中找到 **stdout**。确认对应"文件夹路径"列中的 GUID 是否与你前面记下的 GUID 匹配。若匹配，则表明输出数据与所提交的作业相对应。单击 **stdout** 列左侧的"二进制"。

	![Finding the data output by GUID in the list of content.][image-hdi-gettingstarted-powerquery-importdata2]

9. 单击左上角的"关闭并加载"以将 Hive 作业输出导入到 Excel 中。


## <a name="nextsteps"></a>后续步骤
在本教程中，你已了解了如何使用 HDInsight 设置群集、如何对其运行 MapReduce 作业，以及如何将结果导入到 Excel 中，在 Excel 中，可以使用商业智能工具进一步处理结果以及以图形方式显示结果。若要了解更多信息，请参阅以下文章：

- [HDInsight Hadoop Tools for Visual Studio 入门][1]
- [HDInsight Emulator 入门][hdinsight-emulator]
- [将 Azure Blob 存储与 HDInsight 配合使用][hdinsight-storage]
- [使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell]
- [将数据上载到 HDInsight][hdinsight-upload-data]
- [将 MapReduce 与 HDInsight 配合使用][hdinsight-use-mapreduce]
- [将 Hive 与 HDInsight 配合使用][hdinsight-use-hive]
- [将 Pig 与 HDInsight 配合使用][hdinsight-use-pig]
- [将 Oozie 与 HDInsight 配合使用][hdinsight-use-oozie]
- [为 HDInsight 开发 C# Hadoop 流式处理程序][hdinsight-develop-streaming]
- [为 HDInsight 开发 Java MapReduce 程序][hdinsight-develop-mapreduce]


[1]: /documentation/articles/hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-versions]: /documentation/articles/hdinsight-component-versioning/

[hdinsight-get-started-30]: /documentation/articles/hdinsight-get-started-30/
[hdinsight-provision]: /documentation/articles/hdinsight-provision-clusters/
[hdinsight-admin-powershell]: /documentation/articles/hdinsight-administer-use-powershell/
[hdinsight-upload-data]: /documentation/articles/hdinsight-upload-data/
[hdinsight-use-mapreduce]: /documentation/articles/hdinsight-use-mapreduce
[hdinsight-use-hive]: /documentation/articles/hdinsight-use-hive/
[hdinsight-use-pig]: /documentation/articles/hdinsight-use-pig/
[hdinsight-use-oozie]: /documentation/articles/hdinsight-use-oozie/
[hdinsight-storage]: /documentation/articles/hdinsight-use-blob-storage/
[hdinsight-emulator]: /documentation/articles/hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: /documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: /documentation/articles/hdinsight-develop-deploy-java-mapreduce/
[hadoop-hdinsight-intro]: /documentation/articles/hdinsight-hadoop-introduction/
[hdinsight-weblogs-sample]: /documentation/articles/hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: /documentation/articles/hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: /pricing/purchase-options/

[azure-trial]: /pricing/1rmb-trial/
[azure-management-portal]: https://manage.windowsazure.cn/
[azure-create-storageaccount]: /documentation/articles/storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: /documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: /documentation/articles/install-configure-powershell/
[powershell-open]: /documentation/articles/install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=56-->
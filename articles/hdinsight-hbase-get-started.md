<properties
	pageTitle="在 HDInsight 中使用 Hive 设置和查询 HBase 表 | Azure"
	description="开始在 HDInsight 中将 HBase 与 Hadoop 配合使用。了解如何使用 Hive 创建和查询 HBase 表。"
	services="hdinsight"
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
	ms.date="03/09/2015" 
	wacn.date="" 
	ms.author="jgao"/>



# 开始在 HDInsight 中使用 Apache HBase

了解如何通过在 HDInsight 中使用 Hive 创建 HBase 表和查询 HBase 表。

HBase 是一种低延迟的 NoSQL 数据库，可用于对大数据执行联机事务处理。HBase 以集成到 Azure 环境中的托管群集形式提供。这些群集配置为在 Azure Blob 存储中直接存储数据，这样就减少了延迟，并提高了选择性能和价格的灵活性。这样，客户便可构建用于处理大型数据集的交互式网站，构建用于存储数百万个终结点的传感器数据与遥测数据的服务，并通过 Hadoop 作业来分析这些数据。有关 HBase 及其适用的方案的详细信息，请参阅 [HDInsight HBase 概述][hdinsight-hbase-overview]。

> [AZURE.NOTE] HBase（版本 0.98.0）只能用于 HDInsight 上的 HDInsight 3.1 群集（基于 Apache Hadoop 和 YARN 2.4.0）。有关版本信息，请参阅 [HDInsight 提供的 Hadoop 群集版本的新增功能][hdinsight-versions]

## 先决条件

在开始阅读本教程前，你必须具有：

- **Azure 订阅**：有关获得订阅的详细信息，请参阅[购买选项][azure-purchase-options][试用版][azure-trial]。
- **Azure 存储帐户**：有关说明，请参阅[如何创建存储帐户][azure-create-storageaccount]。
- 安装有 Visual Studio 2013 的**工作站**：有关说明，请参阅[安装 Visual Studio](http://msdn.microsoft.com/zh-cn/library/e2h7fzkw.aspx)。

## 设置 HBase 群集

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**通过使用 Azure 门户设置 HBase 群集**


1. 登录到 [Azure 门户][azure-management-portal]。
2. 单击左下方的**"新建"**，然后单击**"数据服务"**>**"HDINSIGHT"**>**"HBASE"**。

	你还可以使用"自定义创建"选项。
3. 输入**"群集名称"**、**"群集大小"**、群集用户密码和**"存储帐户"**。

	![Choosing an HBase cluster type and entering cluster login credentials.][img-hdinsight-hbase-cluster-quick-create]

	默认 HTTP 用户名是 admin。你可以通过使用"自定义创建"选项自定义该名称。

	> [AZURE.WARNING] 为了实现 HBase 服务的高可用性，你必须设置至少包含**三**个节点的群集。这可确保，如果一个节点发生故障，则 HBase 数据区域在其他节点上可用。

4. 单击右下方的复选标记图标，以创建 HBase 群集。

>[AZURE.NOTE] 在删除 HBase 群集后，你可以通过使用相同的默认 Blob 创建另一个 HBase 群集。新群集将选取你在原始群集中创建的 HBase 表。

## 从 HBase shell 创建 HBase 示例表
本部分介绍如何使用 HBase shell 创建 HBase 表，添加行，并列出行。若要访问 HBase shell，你必须首先启用远程桌面协议 (RDP)，然后建立与 HBase 群集的 RDP 连接。有关说明，请参阅[使用 Azure 门户在 HDInsight 中管理 Hadoop 群集][hdinsight-manage-portal]。


**使用 HBase shell**
 
1. 在 RDP 会话中，单击桌面上的**"Hadoop 命令行"**快捷方式。
2. 将文件夹更改为 HBase 主目录：

		cd %HBASE_HOME%\bin
3. 打开 HBase shell：

		hbase shell

4. 创建包含一个列系列的 HBase，然后插入一行：

		create 'sampletable', 'cf1'
		put 'sampletable', 'row1', 'cf1:col1', 'value1'
		scan 'sampletable'

	有关 Hbase 表架构的详细信息，请参阅 [HBase 架构设计简介][hbase-schema]。有关 HBase 命令的详细信息，请参阅 [Apache HBase 参考指南][hbase-quick-start]。
5. 列出 HBase 表：

		list

**在 HBase WebUI 中检查群集状态**
 
HBase 还附带有可用于帮助监视你的群集的 WebUI。例如，你可以请求统计信息或与区域有关的信息。在 HBase 群集上，你可以在 zookeepernode 地址下方找到 WebUI：


	http://zookeepernode:60010/master-status
		
在高可用性群集中，你将会找到要托管 WebUI 的当前活动 HBase 主节点的链接。

**批量加载示例表**

1. 在 HBase shell 中，创建包含两个列系列的 HBase 表：

		create 'Contacts', 'Personal', 'Office'


3. 创建包含以下数据的联系人列表，并将其上载到 Azure Blob 存储中名为 /tmp/contacts.txt 的文件夹。有关说明，请参阅[在 HDInsight 中为 Hadoop 作业上载数据][hdinsight-upload-data]。

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

2. 在 Hadoop 命令行中，将文件夹更改为 HBase 主目录：

		cd %HBASE_HOME%\bin

3. 运行 ImportTsv。ImportTsv 是一种要以 TSV 格式将数据加载到 HBase 中的工具。它具有两种不同的用法：以 TSV 格式将数据加载到 Hadoop 分布式文件系统 (HDFS) 中，并准备要加载的文件。有关详细信息，请参阅 [Apache HBase 参考指南][hbase-reference]。

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts /tmp/contacts.txt
	
4. 将输出从以前的命令加载到 HBase 中：

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## 使用 Hive 查询 HBase 表

既然你已设置 HBase 群集，并创建 HBase 表，则可以通过使用 Hive 查询表中的数据。本部分将创建要映射到 HBase 表的 Hive 表，并使用该表来查询 HBase 表中的数据。

**打开群集仪表板**

1. 登录到 [Azure 门户][azure-management-portal]。
2. 单击左窗格中的"HDINSIGHT"。你将会看到群集的列表，包括你在上一部分中创建的群集。
3. 单击你要在其中运行 Hive 作业的群集名称。
4. 单击该页底部的**"查询控制台"**，以打开群集仪表板。它将在不同的浏览器标签中打开网页。
5. 输入 Hadoop 用户帐户用户名和密码。默认用户名是 **admin**，而密码是你在设置过程中输入的密码。此时将打开新浏览器标签。
6. 单击该页顶部的**"Hive 编辑器"**。Hive 编辑器的外观如下：

	![HDInsight cluster dashboard.][img-hdinsight-hbase-hive-editor]



 

 

 







	





		







**运行 Hive 查询**

1. 在 Hive 编辑器中输入以下 HiveQL 脚本，然后单击**"提交"**，以创建映射到 HBase 表的 Hive 表。确保你已创建本教程中前面引用的示例表，方法是在运行此语句前使用 HBase shell。

		CREATE EXTERNAL TABLE hbasecontactstable(rowkey STRING, name STRING, homephone STRING, office STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:HomePhone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	等到**"状态"**更新为**"已完成"**。

2. 在 Hive 编辑器中输入以下 HiveQL 脚本，然后单击**"提交"**。Hive 查询会在 HBase 表中查询数据：

     	SELECT count(*) FROM hbasecontactstable;

4. 若要检索 Hive 查询的结果，请在作业完成运行时，单击**"作业会话"**窗口中的**"查看详细信息"**链接。由于你将一条记录放置在 HBase 表中，因此将只有一个作业输出文件。




**浏览输出文件**

1. 在查询控制台中，单击**"文件浏览器"**。
2. 单击用作 HBase 群集的默认文件系统的 Azure 存储帐户。
3. 单击 HBase 群集名称。默认 Azure 存储帐户容器使用群集名称。
4. 单击**"用户"**，然后单击**"管理员"**。（这是 Hadoop 用户名。）
6. 单击具有与运行 SELECT Hive 查询的时间匹配的**"上次修改时间"**的作业名称。
4. 单击**"stdout"**。保存文件，并使用记事本打开该文件。此时将有一个输出文件。

	![HDInsight HBase Hive Editor File Browser][img-hdinsight-hbase-file-browser]

## 使用适用于 .NET C# 的 HBase REST API 客户端库从 HBase 表创建和检索数据。

你必须从 GitHub 下载适用于 .NET 的 HBase REST API 客户端库并构建项目，以便你可以使用 HBase .NET SDK。以下过程包括有关此任务的说明。

1. 创建新的 C# Visual Studio Windows 桌面控制台应用程序。
2. 打开 NuGet 程序包管理器控制台，方法是单击**"工具"**菜单 >**"NuGet 程序包管理器"**>**"程序包管理器控制台"**。
3. 在控制台中运行以下 NuGet 命令：

		Install-Package Microsoft.HBase.Client

5. 在文件的顶部添加以下 **using** 语句：

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. 将 **Main** 函数替换为以下内容：

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.cn";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. 在 **Main** 函数中设置前三个变量。
8. 按 **F5** 运行应用程序。



## 后续步骤
在本教程中，你已了解如何设置 HBase 群集，如何创建表，以及如何从 HBase shell 查看这些表中的数据。你还了解了如何使用 Hive 来查询 HBase 表中的数据，以及如何使用 HBase C# API 创建 HBase 表并从该表中检索数据。

若要了解更多信息，请参阅以下文章：

- [HDInsight HBase 概述][hdinsight-hbase-overview]：
HBase 是构建于 Hadoop 上的 Apache 开源 NoSQL 数据库，用于为大量非结构化和半结构化数据提供随机访问和高度一致性。
- [在 Azure 虚拟网络上设置 HBase 群集][hdinsight-hbase-provision-vnet]：
通过虚拟网络集成，可以将 HBase 群集部署到应用程序所在的虚拟网络，以便应用程序直接与 HBase 进行通信。
- [在 HDInsight 中配置 HBase 复制](hdinsight-hbase-geo-replication.md)：了解如何跨两个 Azure 数据中心配置 HBase 复制。 
了解如何通过在 HDInsight 的 Hadoop 群集中使用 HBase 对大数据执行实时[观点分析](http://zh.wikipedia.org/wiki/Sentiment_analysis)。

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal
[hdinsight-upload-data]: hdinsight-upload-data
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet
[hdinsight-versions]: hdinsight-component-versioning
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment
[azure-purchase-options]: /pricing/purchase-options/
[azure-trial]: /pricing/1rmb-trial/
[azure-management-portal]: https://manage.windowsazure.cn/
[azure-create-storageaccount]: /documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png

<!---HONumber=56-->
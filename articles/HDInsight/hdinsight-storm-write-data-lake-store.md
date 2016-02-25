<!-- not suitable for Mooncake -->

<properties
pageTitle="将 Azure Data Lake 存储与 Azure HDInsight 上的 Apache Storm 配合使用"
description="了解如何通过 HDInsight 上的 Apache Storm 拓扑将数据写入 Azure Data Lake 存储。本文档和相关的示例演示如何使用 HdfsBolt 组件将数据写入 Data Lake 存储。"
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.date="01/14/2016"
	wacn.date=""/>

#将 Azure Data Lake 存储与 HDInsight 上的 Apache Storm 配合使用

Azure Data Lake 存储是与 HDFS 兼容的云存储服务，可为数据提供高吞吐量、可用性、持久性和可靠性。在本文档中，你将学习如何使用基于 Java 的 Storm 拓扑，通过 Apache Storm 中提供的 [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 组件将数据写入 Azure Data Lake 存储。

> [AZURE.IMPORTANT] 本文档中使用的示例拓扑依赖于 Storm on HDInsight 群集随附的组件，而且可能需要进行修改才能配合其他 Apache Storm 群集使用 Azure Data Lake 存储。

##先决条件

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或更高版本
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Azure 订阅
* Storm on HDInsight 群集。本文档中包含有关创建可使用 Azure Data Lake 存储的群集的信息。

###配置环境变量

可以在开发工作站上安装 Java 和 JDK 时设置以下环境变量。不过，你应该检查它们是否存在并且包含系统的正确值。

* __JAVA\_HOME__ - 应该指向已安装 Java 运行时环境 (JRE) 的目录。例如，在 Windows 中，它的值类似于 `c:\Program Files (x86)\Java\jre1.7`。

* __PATH__ - 应该包含以下路径：

    * __JAVA\_HOME__（或等效的路径）
    
    * __JAVA\_HOME\\bin__（或等效的路径）
    
    * 安装 Maven 的目录

##拓扑实现

本文档中使用的示例以 Java 编写，并使用以下组件：

* __TickSpout__：生成拓扑中其他组件使用的数据。

* __PartialCount__：计算 TickSpout 生成的事件。

* __FinalCount__：聚合来自 PartialCount 的计数数据。

* __ADLStoreBolt__：使用 [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 组件将数据写入 Azure Data Lake 存储。

包含此拓扑的项目可从 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 下载。

###了解 ADLStoreBolt

ADLStoreBolt 是拓扑中向 Azure Data Lake 写入数据的 HdfsBolt 实例使用的名称。它不是 Microsoft 创建的 HdfsBolt 特殊版本，但它确实依赖核心站点配置值，以及 Azure HDInsight 中用来与 Data Lake 通信的随附 Hadoop 组件。

具体而言，当你创建 HDInsight 群集时，可以将其关联到 Azure Data Lake 存储。这会将条目写入所选 Data Lake 存储的核心站点，hadoop-client 和 hadoop-hdfs 等组件使用这些条目来与 Data Lake 存储通信。

> [AZURE.NOTE] Microsoft 已在 Apache Hadoop 和 Storm 项目中分发了用来与 Azure Data Lake 存储和 Azure Blob 存储通信的代码，但其他 Hadoop 和 Storm 分发版在默认情况下不一定包含此功能。

拓扑中的 HdfsBolt 配置如下所示：

    // 1. Create sync and rotation policies to control when data is synched
    //    (written) to the file system and when to roll over into a new file.
    SyncPolicy syncPolicy = new CountSyncPolicy(1000);
    FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
    // 2. Set the format. In this case, comma delimited
    RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
    // 3. Set the directory name. In this case, '/stormdata/'
    FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
    // 4. Create the bolt using the previously created settings,
    //    and also tell it the base URL to your Data Lake Store.
    // NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
    HdfsBolt adlsBolt = new HdfsBolt()
		.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
      	.withRecordFormat(recordFormat)
      	.withFileNameFormat(fileNameFormat)
      	.withRotationPolicy(rotationPolicy)
      	.withSyncPolicy(syncPolicy);
    // 4. Give it a name and wire it up to the bolt it accepts data
    //    from. NOTE: The name used here is also used as part of the
    //    file name for the files written to Data Lake Store.
    builder.setBolt("ADLStoreBolt", adlsBolt, 1)
      .globalGrouping("finalcount");
      
如果你熟悉 HdfsBolt 的用法，你会发现，除了 URL 不同以外，其他配置都相当标准。URL 提供 Azure Data Lake 存储的根目录路径。

由于写入 Data Lake 存储时使用的是 HdfsBolt，并且只需更改 URL，因此你应该能够获取使用 HdfsBolt 写入 HDFS 或 WASB 的现有拓扑，并轻松地将它更改为使用 Azure Data Lake 存储。

##创建 HDInsight 群集和 Data Lake 存储

执行[通过 Azure 将 HDInsight 与 Data Lake 存储配合使用](/documentation/articles/data-lake-store-hdinsight-hadoop-use-portal)文档中的步骤创建新的 Storm on HDInsight 群集。此文档中的步骤将逐步引导你创建新的 HDInsight 群集和 Azure Data Lake 存储。

> [AZURE.IMPORTANT] 当创建 HDInsight 群集时，必须选择 __Storm__ 作为群集类型。操作系统可以是 Windows。

##生成和打包拓扑

1. 从 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 将示例项目下载到你的开发环境。

2. 在编辑器中打开 `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` 文件，然后找到包含 `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")` 的行。将 __MYDATALAKE__ 更改为创建 HDInsight 服务器时所用的 Azure Data Lake 存储名称。

3. 从命令提示符、终端或 Shell 会话将目录更改为所下载项目的根目录，然后运行以下命令来生成并打包拓扑。

        mvn compile
        mvn package
    
    生成和打包完成之后，将出现名为 `target` 的新目录，其中包含名为 `StormToDataLakeStore-1.0-SNAPSHOT.jar` 的文件。此文件包含编译的拓扑。

##在基于 Windows 的 HDInsight 上部署和运行

1. 打开 Web 浏览器并转到 HTTPS://CLUSTERNAME.azurehdinsight.cn，其中 __CLUSTERNAME__ 是 HDInsight 群集的名称。出现提示时，请提供创建群集时用于此帐户的管理员用户名称 (`admin`) 和密码。

2. 在 Storm 仪表板上，从“Jar 文件”下拉列表中选择“浏览”，然后选择 `target` 目录中的 StormToDataLakeStore-1.0-SNAPSHOT.jar 文件。为窗体中的其他条目使用以下值：

    * 类名：com.microsoft.example.StormToDataLakeStore
    * 其他参数：datalakewriter
    
    ![Storm 仪表板图像](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. 选择“提交”按钮上载并启动拓扑。拓扑启动之后，“提交”按钮下面的结果字段应显示类似于下面的信息：

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##查看输出数据

可使用多种方法查看数据。在本部分中，我们将使用 Azure 管理门户和 `hdfs` 命令来查看数据。

> [AZURE.NOTE] 在检查输出数据之前，应该先让拓扑运行几分钟，以便数据同步到 Azure Data Lake 存储上的多个文件。

* __从 [Azure 管理门户](https://manage.windowsazure.cn)__：在门户中，选择配合 HDInsight 使用的 Azure Data Lake 存储。

    > [AZURE.NOTE] 如果未将 Data Lake 存储固定到 Azure 管理门户仪表板，可以通过选择左侧列表底部的“浏览”，选择“Data Lake 存储”，最后选择该存储，来查找数据。
    
    通过 Data Lake 存储顶部的图标选择“数据资源管理器”。
    
    ![数据浏览图标](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    接下来，选择 __stormdata__ 文件夹。此时应会显示文本文件的列表。
    
    ![文本文件](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    选择其中一个文件以查看其内容。

* __从群集__：如果已使用远程桌面连接到 HDInsight 群集（Windows 群集），你可以使用以下命令查看数据。将 __DATALAKE__ 替换为 Data Lake 存储的名称

        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt

    这会串联目录中存储的文本文件，并显示类似于下面的信息：
    
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000
        
##停止拓扑

Storm 拓扑将一直运行，直到被停止或群集被删除。若要停止拓扑，请参考以下信息。

__对于基于 Windows 的 HDInsight__：

1. 在“Storm 仪表板”(https://CLUSTERNAME.azurehdinsight.cn,) 中，选择页面顶部的“Storm UI”链接。

2. 加载 Storm UI 后，选择“datalakewriter”链接。

    ![datalakewriter 链接](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. 在“拓扑操作”部分中选择“删除”，然后在出现的对话框中选择“确定”。

    ![拓扑操作](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

##后续步骤

现在，你已了解如何使用 Storm 写入 Azure Data Lake 存储，接下来请学习其他 [HDInsight Storm 示例](/documentation/articles/hdinsight-storm-example-topology)。

<!---HONumber=Mooncake_0215_2016-->
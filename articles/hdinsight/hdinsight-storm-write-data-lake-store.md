---
title: "将 Azure Data Lake 存储与 Azure HDInsight 上的 Apache Storm 配合使用"
description: "了解如何通过 HDInsight 上的 Apache Storm 拓扑将数据写入 Azure Data Lake 存储。 本文档和相关的示例演示如何使用 HdfsBolt 组件将数据写入 Data Lake 存储。"
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5d90b80a7392db51335d099f685369ab825ad645


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight"></a>将 Azure Data Lake 存储与 HDInsight 上的 Apache Storm 配合使用
Azure Data Lake 存储是与 HDFS 兼容的云存储服务，可为数据提供高吞吐量、可用性、持久性和可靠性。 在本文档中，将学习如何使用基于 Java 的 Storm 拓扑，通过 Apache Storm 中提供的 [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 组件将数据写入 Azure Data Lake Store。

> [!IMPORTANT]
> 本文档中使用的示例拓扑依赖于 Storm on HDInsight 群集随附的组件，而且可能需要进行修改才能配合其他 Apache Storm 群集使用 Azure Data Lake 存储。
> 
> 

## <a name="prerequisites"></a>先决条件
* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或更高版本
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Azure 订阅
* Storm on HDInsight 群集版本 3.2。 若要创建新的 Storm on HDInsight 群集，请使用[通过 Azure 将 HDInsight 与 Data Lake Store 配合使用](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)文档中的步骤。 此文档中的步骤将逐步引导你创建新的 HDInsight 群集和 Azure Data Lake 存储。  
  
  > [!IMPORTANT]
  > 创建 HDInsight 群集时，必须选择 **Storm** 作为群集类型，并选择 **3.2** 作为版本。 操作系统可以是 Windows 或 Linux。  
  > 
  > 

### <a name="configure-environment-variables"></a>配置环境变量
可以在开发工作站上安装 Java 和 JDK 时设置以下环境变量。 不过，你应该检查它们是否存在并且包含系统的正确值。

* **JAVA_HOME** - 应该指向已安装 Java 运行时环境 (JRE) 的目录。 例如，在 Unix 或 Linux 分发版中，它的值应该类似于 `/usr/lib/jvm/java-7-oracle`。 在 Windows 中，它的值类似于 `c:\Program Files (x86)\Java\jre1.7`。
* **PATH** - 应该包含以下路径：
  
  * **JAVA\_HOME**（或等效的路径）
  * **JAVA\_HOME\bin**（或等效的路径）
  * 安装 Maven 的目录

## <a name="topology-implementation"></a>拓扑实现
本文档中使用的示例以 Java 编写，并使用以下组件：

* **TickSpout**：生成拓扑中其他组件使用的数据。
* **PartialCount**：计算 TickSpout 生成的事件。
* **FinalCount**：聚合来自 PartialCount 的计数数据。
* **ADLStoreBolt**：使用 [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 组件将数据写入 Azure Data Lake Store。

包含此拓扑的项目可从 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 下载。

### <a name="understanding-adlstorebolt"></a>了解 ADLStoreBolt
ADLStoreBolt 是拓扑中向 Azure Data Lake 写入数据的 HdfsBolt 实例使用的名称。 它不是 Microsoft 创建的 HdfsBolt 特殊版本，但它确实依赖核心站点配置值，以及 Azure HDInsight 中用来与 Data Lake 通信的随附 Hadoop 组件。

具体而言，当你创建 HDInsight 群集时，可以将其关联到 Azure Data Lake 存储。 这会将条目写入所选 Data Lake 存储的核心站点，hadoop-client 和 hadoop-hdfs 等组件使用这些条目来与 Data Lake 存储通信。

> [!NOTE]
> Microsoft 已在 Apache Hadoop 和 Storm 项目中分发了用来与 Azure Data Lake 存储和 Azure Blob 存储通信的代码，但其他 Hadoop 和 Storm 分发版在默认情况下不一定包含此功能。
> 
> 

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

如果你熟悉 HdfsBolt 的用法，你会发现，除了 URL 不同以外，其他配置都相当标准。 URL 提供 Azure Data Lake 存储的根目录路径。

由于写入 Data Lake 存储时使用的是 HdfsBolt，并且只需更改 URL，因此你应该能够获取使用 HdfsBolt 写入 HDFS 或 WASB 的现有拓扑，并轻松地将它更改为使用 Azure Data Lake 存储。

## <a name="build-and-package-the-topology"></a>生成和打包拓扑
1. 从 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 将示例项目下载到你的开发环境。
2. 在编辑器中打开 `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` 文件，然后找到包含 `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")` 的行。 将 **MYDATALAKE** 更改为创建 HDInsight 服务器时所用的 Azure Data Lake Store 名称。
3. 从命令提示符、终端或 Shell 会话将目录更改为所下载项目的根目录，然后运行以下命令来生成并打包拓扑。
   
        mvn compile
        mvn package
   
    生成和打包完成之后，将出现名为 `target` 的新目录，其中包含名为 `StormToDataLakeStore-1.0-SNAPSHOT.jar` 的文件。 此文件包含编译的拓扑。

## <a name="deploy-and-run-on-linux-based-hdinsight"></a>在基于 Linux 的 HDInsight 上部署和运行
创建基于 Linux 的 Storm on HDInsight 群集时，使用以下步骤来部署和运行拓扑。

1. 使用以下命令可将拓扑复制到 HDInsight 群集。 用创建群集时使用的 SSH 用户名替换 **USER**。 将 **CLUSTERNAME** 替换为群集的名称。
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    出现提示时，请输入在创建群集 SSH 用户时使用的密码。 如果使用公钥而不是密码，则可能需要使用 `-i` 参数指定匹配私钥的路径。
   
   > [!NOTE]
   > 如果使用 Windows 客户端开发，可能没有 `scp` 命令。 若此，则可以使用 [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 上提供的 `pscp`。
   > 
   > 
2. 上传完成后，使用以下步骤通过 SSH 可连接到 HDInsight 群集。 用创建群集时使用的 SSH 用户名替换 **USER**。 将 **CLUSTERNAME** 替换为群集的名称。
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    出现提示时，请输入在创建群集 SSH 用户时使用的密码。 如果使用公钥而不是密码，则可能需要使用 `-i` 参数指定匹配私钥的路径。
   
   > [!NOTE]
   > 如果开发使用的是 Windows 客户端，请参阅[在 Windows 中通过 SSH 连接到基于 Linux 的 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) 中的信息，以了解有关使用 PuTTY 客户端连接到群集的信息。
   > 
   > 
3. 连接后，使用以下命令启动拓扑：
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
   
    这会使用 `datalakewriter` 的友好名称来启动拓扑。

## <a name="deploy-and-run-on-windows-based-hdinsight"></a>在基于 Windows 的 HDInsight 上部署和运行
1. 在 Web 浏览器中导航到 HTTPS://CLUSTERNAME.azurehdinsight.net，其中 **CLUSTERNAME** 是 HDInsight 群集的名称。 出现提示时，请提供创建群集时用于此帐户的管理员用户名称 (`admin`) 和密码。
2. 在 Storm 仪表板上，从“Jar 文件”下拉列表中选择“浏览”，然后选择 `target` 目录中的 StormToDataLakeStore-1.0-SNAPSHOT.jar 文件。 为窗体中的其他条目使用以下值：
   
   * 类名：com.microsoft.example.StormToDataLakeStore
   * 其他参数：datalakewriter
     
     ![Storm 仪表板图像](./media/hdinsight-storm-write-data-lake-store/submit.png)
3. 选择“提交”按钮上传并启动拓扑。 拓扑启动之后，“提交”按钮下面的结果字段应显示类似于下面的信息：
   
        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

## <a name="view-output-data"></a>查看输出数据
可使用多种方法查看数据。 在本部分中，将使用 Azure 门户和 `hdfs` 命令来查看数据。

> [!NOTE]
> 在检查输出数据之前，应该先让拓扑运行几分钟，以便数据同步到 Azure Data Lake 存储上的多个文件。
> 
> 

* **从[ Azure 门户](https://portal.azure.com)**：在门户中，选择配合 HDInsight 使用的 Azure Data Lake Store。
  
  > [!NOTE]
  > 如果未将 Data Lake Store 固定到 Azure 门户仪表板，可以通过选择左侧列表底部的“浏览”，选择“Data Lake Store”，最后选择该存储，来查找数据。
  > 
  > 
  
    通过 Data Lake Store 顶部的图标选择“数据资源管理器”。
  
    ![数据浏览图标](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
  
    接下来，选择 **stormdata** 文件夹。 此时应会显示文本文件的列表。
  
    ![文本文件](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
  
    选择其中一个文件以查看其内容。
* **从群集**：如果已使用 SSH（Linux 群集）或远程桌面（Windows 群集）连接到 HDInsight 群集，可以使用以下命令查看数据。 将 **DATALAKE** 替换为 Data Lake Store 的名称
  
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

## <a name="stop-the-topology"></a>停止拓扑
Storm 拓扑将一直运行，直到被停止或群集被删除。 若要停止拓扑，请参考以下信息。

**对于基于 Linux 的 HDInsight**：

在群集的 SSH 会话中，使用以下命令：

    storm kill datalakewriter

**对于基于 Windows 的 HDInsight**：

1. 在“Storm 仪表板”(https://CLUSTERNAME.azurehdinsight.net,) 中，选择页面顶部的“Storm UI”链接。
2. 加载 Storm UI 后，选择“datalakewriter”链接。
   
    ![datalakewriter 链接](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)
3. 在“拓扑操作”部分中选择“删除”，然后在出现的对话框中选择“确定”。
   
    ![拓扑操作](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## <a name="delete-your-cluster"></a>删除群集
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>后续步骤
现在大家已了解如何使用 Storm 写入 Azure Data Lake Store，接下来将学习其他 [HDInsight 的 Storm 示例](hdinsight-storm-example-topology.md)。




<!--HONumber=Nov16_HO3-->



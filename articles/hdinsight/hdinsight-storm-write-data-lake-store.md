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
ms.date: 03/03/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 376415d34592d18de00513ee9142512eb716e426
ms.lasthandoff: 03/04/2017


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight-java"></a>将 Azure Data Lake Store 与 HDInsight (Java) 上的 Apache Storm 配合使用

Azure Data Lake 存储是与 HDFS 兼容的云存储服务，可为数据提供高吞吐量、可用性、持久性和可靠性。 在本文档中，将了解如何使用基于 Java 的 Storm 拓扑将数据写入 Azure Data Lake Store。 本文档中的步骤将使用 Apache Storm 中提供的 [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 组件。

> [!IMPORTANT]
> 本文档中使用的示例拓扑依赖于 Storm on HDInsight 群集随附的组件，而且可能需要进行修改才能配合其他 Apache Storm 群集使用 Azure Data Lake 存储。

## <a name="how-to-work-with-azure-data-lake-store"></a>如何使用 Azure Data Lake Store

在 HDInsight 看来，Data Lake Store 是一种兼容 HDFS 的文件系统，因此可以通过 Storm-HDFS Bolt 向其写入内容。 通过 HDInsight 使用 Azure Data Lake 时，可以使用 `adl://` 文件方案。

* 如果 Data Lake Storage 是群集的主存储器，则使用 `adl:///`。 这是群集存储器在 Azure Data Lake 中的根路径。 该路径在你的 Data Lake Storage 帐户中可能转换为路径 /clusters/CLUSTERNAME。
* 如果 Data Lake Storage 是群集的辅助存储器，则使用 `adl://DATALAKEACCOUNT.azuredatalakestore.net/`。 此 URI 指定数据写入到的 Data Lake Storage 帐户。 数据从 Data Lake Store 的根目录处开始写入。

    > [!NOTE]
    > 你也可以使用此 URI 格式将数据保存到包含群集主存储器的 Data Lake Store 帐户。 这样一来，就可以将数据保存在包含 HDInsight 的目录路径外。

以下 Java 代码演示如何使用 Storm-HDFS Bolt 将数据写入名为 `MYDATALAKE` 的 Data Lake Store 帐户中名为 `/stormdata` 的目录。

```java
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
```

以下 YAML 演示如何使用 Flux 框架中的 Storm-HDFS Bolt：

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - KB

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

  - id: "rotationAction"
    className: "org.apache.storm.hdfs.common.rotation.MoveFileAction"
    configMethods:
      - name: "toDestination"
        args: ["${hdfs.dest.dir}"]

# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1
```

> [!NOTE]
> 本文档中的示例使用 Flux 框架。

## <a name="prerequisites"></a>先决条件

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或更高版本。 HDInsight 3.5 需要 Java 8。

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Storm on HDInsight 群集版本 3.5。 若要创建新的 Storm on HDInsight 群集，请使用[通过 Azure 将 HDInsight 与 Data Lake Store 配合使用](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)文档中的步骤。

### <a name="configure-environment-variables"></a>配置环境变量

可以在开发工作站上安装 Java 和 JDK 时设置以下环境变量。 不过，你应该检查它们是否存在并且包含系统的正确值。

* **JAVA_HOME** - 应该指向已安装 Java 运行时环境 (JRE) 的目录。 例如，在 Unix 或 Linux 分发版中，它的值应该类似于 `/usr/lib/jvm/java-8-oracle`。 在 Windows 中，它的值类似于 `c:\Program Files (x86)\Java\jre1.8`。
* **PATH** - 应该包含以下路径：
  
  * **JAVA\_HOME**（或等效的路径）
  * **JAVA\_HOME\bin**（或等效的路径）
  * 安装 Maven 的目录

## <a name="topology-implementation"></a>拓扑实现

本文档中使用的示例以 Java 编写，并使用以下组件：

* **TickSpout**：生成拓扑中其他组件使用的数据。
* **PartialCount**：计算 TickSpout 生成的事件。
* **FinalCount**：聚合来自 PartialCount 的计数数据。
* **ADLStoreBolt**：使用 [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 组件将数据写入 Azure Data Lake Store。

包含此拓扑的项目可从 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 下载。

## <a name="build-and-package-the-topology"></a>生成和打包拓扑

1. 从 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 将示例项目下载到你的开发环境。

2. 在编辑器中打开 `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` 文件，然后找到包含 `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")` 的行。 将 **MYDATALAKE** 更改为创建 HDInsight 服务器时所用的 Azure Data Lake Store 名称。

3. 从命令提示符、终端或 Shell 会话将目录更改为所下载项目的根目录，然后运行以下命令来生成并打包拓扑。
   
        mvn compile
        mvn package
   
    生成和打包完成之后，将出现名为 `target` 的新目录，其中包含名为 `StormToDataLakeStore-1.0-SNAPSHOT.jar` 的文件。 此文件包含编译的拓扑。

## <a name="deploy-and-run-the-topology"></a>部署并运行拓扑

1. 使用以下命令可将拓扑复制到 HDInsight 群集。 用创建群集时使用的 SSH 用户名替换 **USER**。 将 **CLUSTERNAME** 替换为群集的名称。
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    出现提示时，请输入在创建群集 SSH 用户时使用的密码。 如果使用公钥而不是密码，则可能需要使用 `-i` 参数指定匹配私钥的路径。
   
   > [!NOTE]
   > 如果使用 Windows 客户端开发，可能没有 `scp` 命令。 若此，则可以使用 [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 上提供的 `pscp`。

2. 上传完成后，使用以下步骤通过 SSH 可连接到 HDInsight 群集。 用创建群集时使用的 SSH 用户名替换 **USER**。 将 **CLUSTERNAME** 替换为群集的名称。
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    出现提示时，请输入在创建群集 SSH 用户时使用的密码。 如果使用公钥而不是密码，则可能需要使用 `-i` 参数指定匹配私钥的路径。
   
   > [!NOTE]
   > 如果开发使用的是 Windows 客户端，请参阅[在 Windows 中通过 SSH 连接到基于 Linux 的 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) 中的信息，以了解有关使用 PuTTY 客户端连接到群集的信息。

3. 连接后，使用以下命令创建一个名为 `dev.properties` 的文件：

        nano dev.properties

4. 将以下文本用作 `dev.properties` 文件的内容：

        hdfs.write.dir: /stormdata
        hdfs.url: adl:///
    
    若要保存文件，请使用 __Ctrl + X__，然后输入 __Y__，最后按 __Enter__。 此文件中的值用于设置 Data Lake Store URL 和数据写入到的目录名称。

3. 使用以下命令启动拓扑：
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /datalakewriter.yaml --filter dev.properties

    此命令使用 Flux 框架启动拓扑。 拓扑由 jar 包含的 `datalakewriter.yaml` 文件定义。 `dev.properties` 文件作为筛选器传递，该文件包含的值由拓扑读取。

## <a name="view-output-data"></a>查看输出数据

若要查看数据，请使用以下命令：

    hdfs dfs -ls /stormdata/

此命令会列出拓扑创建的文件。

如果 Data Lake Store 不是群集的默认存储器，则使用以下命令来查看数据：

    hdfs dfs -ls adl://MYDATALAKE.azuredatalakestore.net/stormdata/

在上一个命令中，将 __MYDATALAKE__ 替换为 Data Lake Store 帐户。

以下列表是由前面的命令所返回的数据的示例：

    Found 30 items
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-13-1488568412603.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-14-1488568415055.txt

## <a name="stop-the-topology"></a>停止拓扑

Storm 拓扑将一直运行，直到被停止或群集被删除。 若要停止拓扑，请参考以下信息。

**对于基于 Linux 的 HDInsight**：

在群集的 SSH 会话中，使用以下命令：

    storm kill datalakewriter

## <a name="delete-your-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>后续步骤

现在大家已了解如何使用 Storm 写入 Azure Data Lake Store，接下来将学习其他 [HDInsight 的 Storm 示例](hdinsight-storm-example-topology.md)。



---
title: 教程 - 使用 Apache Storm 写入存储/Data Lake Storage - Azure HDInsight
description: 教程 - 了解如何使用 Apache Storm 将数据写入到与 HDFS 兼容的 Azure HDInsight 存储。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 5c1376c7d1afe9c9702cfb43a146ac1cd17d6e58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428344"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>教程：从 Apache Storm on Azure HDInsight 写入到 Apache Hadoop HDFS

本教程演示如何使用 Apache Storm 将数据写入到 Apache Storm on HDInsight 使用的 HDFS 兼容存储。 HDInsight 可以使用 Azure 存储和 Azure Data Lake Storage 作为 HDFS 兼容的存储。 Storm 提供可将数据写入 HDFS 的 [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 组件。 本文档提供有关从 HdfsBolt 写入上述任一存储类型的信息。

本文档中使用的示例拓扑依赖于 Storm on HDInsight 随附的组件。 对其他 Apache Storm 群集使用此拓扑时，可能需要修改此拓扑才能让它配合 Azure Data Lake Storage 工作。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用脚本操作配置群集
> * 生成和打包拓扑
> * 部署并运行拓扑
> * 查看输出数据
> * 停止拓扑

## <a name="prerequisites"></a>先决条件

* [Java 开发人员工具包 (JDK) 版本 8](https://aka.ms/azure-jdks)

* 根据 Apache 要求正确[安装](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是 Java 项目的项目生成系统。

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* 群集主存储的 [URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 对于 Azure 存储，这将是 `wasb://`，对于 Azure Data Lake Storage Gen2，这将是 `abfs://`，对于 Azure Data Lake Storage Gen1，这将是 `adl://`。 如果为 Azure 存储或 Data Lake Storage Gen2 启用了安全传输，则 URI 将分别为 `wasbs://` 或 `abfss://`，另请参阅[安全传输](../../storage/common/storage-require-secure-transfer.md)。

### <a name="example-configuration"></a>示例配置

以下 YAML 摘自示例中包含的 `resources/writetohdfs.yaml` 文件。 此文件定义对 Apache Storm 使用 [Flux](https://storm.apache.org/releases/1.1.2/flux.html) 框架的 Storm 拓扑。

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

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

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


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
```

此 YAML 定义以下项：

* `syncPolicy`：定义何时将文件同步/刷新到文件系统。 在此示例中，为每隔 1000 个元组。
* `fileNameFormat`：定义写入文件时要使用的路径和文件名模式。 在此示例中，路径是在运行时使用筛选器提供的，文件扩展名为 `.txt`。
* `recordFormat`：定义写入的文件的内部格式。 在此示例中，字段由 `|` 字符分隔。
* `rotationPolicy`：定义何时轮换文件。 在此示例中，不执行轮换。
* `hdfs-bolt`：使用前面的组件作为 `HdfsBolt` 类的配置参数。

有关 Flux 框架的详细信息，请参阅 [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)。

## <a name="configure-the-cluster"></a>配置群集

默认情况下，Storm on HDInsight 不会在 Storm 的类路径中包含 `HdfsBolt` 用来与 Azure 存储或 Data Lake Storage 通信的组件。 使用以下脚本操作可将这些组件添加到群集上 Storm 的 `extlib` 目录：

| 属性 | 值 |
|---|---|
|脚本类型 |- Custom|
|Bash 脚本 URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|节点类型 |Nimbus、Supervisor|
|parameters |无|

有关在群集中使用此脚本的信息，请参阅[使用脚本操作自定义 HDInsight 群集](./../hdinsight-hadoop-customize-cluster-linux.md)文档。

## <a name="build-and-package-the-topology"></a>生成和打包拓扑

1. 将示例项目从 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 下载到开发环境。

2. 从命令提示符、终端或 Shell 会话将目录更改为所下载项目的根目录。 若要生成和打包拓扑，请使用以下命令：

    ```cmd
    mvn compile package
    ```

    生成和打包完成之后，会出现名为 `target` 的新目录，其中包含名为 `StormToHdfs-1.0-SNAPSHOT.jar` 的文件。 此文件包含编译的拓扑。

## <a name="deploy-and-run-the-topology"></a>部署并运行拓扑

1. 使用以下命令可将拓扑复制到 HDInsight 群集。 将 `CLUSTERNAME` 替换为群集的名称。

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. 上传完成后，使用以下步骤通过 SSH 可连接到 HDInsight 群集。 将 `CLUSTERNAME` 替换为群集的名称。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 连接后，使用以下命令创建一个名为 `dev.properties` 的文件：

    ```bash
    nano dev.properties
    ```

1. 将以下文本用作 `dev.properties` 文件的内容。 基于 [URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)根据需要进行修改。

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    要保存文件，请使用 __Ctrl + X__，并输入 __Y__，最后按 __Enter__。 此文件中的值用于设置存储 URL 和数据将写入到的目录名称。

1. 使用以下命令启动拓扑：

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    此命令通过将拓扑提交到群集的 Nimbus 节点，使用 Flux 框架启动拓扑。 拓扑由 jar 包含的 `writetohdfs.yaml` 文件定义。 `dev.properties` 文件作为筛选器传递，该文件包含的值由拓扑读取。

## <a name="view-output-data"></a>查看输出数据

若要查看数据，请使用以下命令：

  ```bash
  hdfs dfs -ls /stormdata/
  ```

此时会显示此拓扑创建的文件列表。 以下列表是由前面的命令所返回的数据的示例：

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>停止拓扑

Storm 拓扑会一直运行，直到被停止或群集被删除。 若要停止拓扑，请使用以下命令：

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>清理资源

若要清理本教程创建的资源，可以删除资源组。 删除资源组也会删除相关联的 HDInsight 群集，以及与资源组相关联的任何其他资源。

若要使用 Azure 门户删除资源组，请执行以下操作：

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“资源组”以显示资源组的列表。 
2. 找到要删除的资源组，然后右键单击列表右侧的“更多”按钮 (...)。 
3. 选择“删除资源组”，然后进行确认。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Apache Storm 将数据写入到 Apache Storm on HDInsight 使用的 HDFS 兼容存储。

> [!div class="nextstepaction"]
> 发现其他 [HDInsight 的 Apache Storm 示例](apache-storm-example-topology.md)
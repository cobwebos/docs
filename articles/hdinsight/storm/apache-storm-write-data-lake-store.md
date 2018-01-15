---
title: "使用 Apache Storm 写入存储/Data Lake Store - Azure HDInsight | Microsoft Docs"
description: "了解如何使用 Apache Storm 将数据写入到与 HDFS 兼容的 HDInsight 存储。 Azure 存储或 Azure Data Lake Store 为 HDInsight 提供 HDFS 兼容的存储。 本文档和相关的示例演示如何使用 HdfsBolt 组件将数据写入到 Storm on HDInsight 群集的默认存储。"
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/04/2018
ms.author: larryfr
ms.openlocfilehash: 5550dc2ffc53c6ccd30ecb4901ec98c4d38e366b
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>从 Apache Storm on HDInsight 写入 HDFS

了解如何使用 Storm 将数据写入到 Apache Storm on HDInsight 使用的 HDFS 兼容存储。 HDInsight 可以使用 Azure 存储和 Azure Data Lake Store 作为 HDFS 兼容的存储。 Storm 提供可将数据写入 HDFS 的 [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 组件。 本文档提供有关从 HdfsBolt 写入上述任一存储类型的信息。 

> [!IMPORTANT]
> 本文档中使用的示例拓扑依赖于 Storm on HDInsight 随附的组件。 对其他 Apache Storm 群集使用此拓扑时，可能需要修改此拓扑才能让它配合 Azure Data Lake Store 工作。

## <a name="get-the-code"></a>获取代码

包含此拓扑的项目可从 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 下载。

若要编译此项目，需要对开发环境进行以下配置：

* [Java JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 或更高版本。 HDInsight 3.5 或更高版本需要 Java 8。

* [Maven 3.x](https://maven.apache.org/download.cgi)

可以在开发工作站上安装 Java 和 JDK 时设置以下环境变量。 不过，应该检查它们是否存在并且包含系统的正确值。

* `JAVA_HOME` - 应该指向 JDK 的安装目录。
* `PATH` - 应该包含以下路径：
  
    * `JAVA_HOME`（或等效路径）。
    * `JAVA_HOME\bin`（或等效路径）。
    * Maven 的安装目录

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>如何对 HDInsight 使用 HdfsBolt

> [!IMPORTANT]
> 对 Storm on HDInsight 使用 HdfsBolt 之前，必须先使用脚本操作将所需的 jar 文件复制到 Storm 的 `extpath` 中。 有关详细信息，请参阅[配置群集](#configure)部分。

HdfsBolt 使用提供的文件方案来了解如何写入 HDFS。 在 HDInsight 中，请使用以下方案之一：

* `wasb://`：与 Azure 存储帐户配合使用。
* `adl://`：与 Azure Data Lake Store 配合使用。

下表提供针对不同方案使用文件方案的示例：

| 方案 | 说明 |
| ----- | ----- |
| `wasb:///` | 默认存储帐户是 Azure 存储帐户中的某个 Blob 容器 |
| `adl:///` | 默认存储帐户是 Azure Data Lake Store 中的某个目录。 在创建群集期间，请指定在 Data Lake Store 中充当群集 HDFS 根目录的目录。 例如 `/clusters/myclustername/` 目录。 |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | 与群集关联的非默认（其他）Azure 存储帐户。 |
| `adl://STORENAME/` | 群集使用的 Data Lake Store 的根目录。 使用此方案可以访问包含群集文件系统的目录外部的数据。 |

有关详细信息，请参阅 apache.org 上的 [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 参考文档。

### <a name="example-configuration"></a>示例配置

以下 YAML 摘自示例中包含的 `resources/writetohdfs.yaml` 文件。 此文件定义对 Apache Storm 使用 [Flux](https://storm.apache.org/releases/1.1.0/flux.html) 框架的 Storm 拓扑。

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

有关 Flux 的详细信息，请参阅 [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html)。

## <a name="configure-the-cluster"></a>配置群集

默认情况下，Storm on HDInsight 不会在 Storm 的类路径中包含 HdfsBolt 用来与 Azure 存储或 Data Lake Store 通信的组件。 使用以下脚本操作可将这些组件添加到群集上 Storm 的 `extlib` 目录：

* 脚本 URI：`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* 要应用于的节点：Nimbus、Supervisor
* 参数：无

有关在群集中使用此脚本的信息，请参阅[使用脚本操作自定义 HDInsight 群集](./../hdinsight-hadoop-customize-cluster-linux.md)文档。

## <a name="build-and-package-the-topology"></a>生成和打包拓扑

1. 从 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 将示例项目下载到开发环境。

2. 从命令提示符、终端或 Shell 会话将目录更改为所下载项目的根目录。 若要生成和打包拓扑，请使用以下命令：
   
        mvn compile package
   
    生成和打包完成之后，会出现名为 `target` 的新目录，其中包含名为 `StormToHdfs-1.0-SNAPSHOT.jar` 的文件。 此文件包含编译的拓扑。

## <a name="deploy-and-run-the-topology"></a>部署并运行拓扑

1. 使用以下命令可将拓扑复制到 HDInsight 群集。 用创建群集时使用的 SSH 用户名替换 **USER**。 将 **CLUSTERNAME** 替换为群集的名称。
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs1.0-SNAPSHOT.jar
   
    出现提示时，请输入在创建群集 SSH 用户时使用的密码。 如果使用公钥而不是密码，则可能需要使用 `-i` 参数指定匹配私钥的路径。
   
   > [!NOTE]
   > 有关在 HDInsight 中使用 `scp` 的详细信息，请参阅[在 HDInsight 中使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 上传完成后，使用以下步骤通过 SSH 可连接到 HDInsight 群集。 用创建群集时使用的 SSH 用户名替换 **USER**。 将 **CLUSTERNAME** 替换为群集的名称。
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    出现提示时，请输入在创建群集 SSH 用户时使用的密码。 如果使用公钥而不是密码，则可能需要使用 `-i` 参数指定匹配私钥的路径。
   
   有关详细信息，请参阅 [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

3. 连接后，使用以下命令创建一个名为 `dev.properties` 的文件：

        nano dev.properties

4. 将以下文本用作 `dev.properties` 文件的内容：

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > 本示例假设群集将 Azure 存储帐户用作默认存储。 如果群集使用 Azure Data Lake Store，请改用 `hdfs.url: adl:///`。
    
    要保存文件，请使用 __Ctrl + X__，并输入 __Y__，最后按 __Enter__。 此文件中的值用于设置 Data Lake Store URL 和数据写入到的目录名称。

3. 使用以下命令启动拓扑：
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    此命令通过将拓扑提交到群集的 Nimbus 节点，使用 Flux 框架启动拓扑。 拓扑由 jar 包含的 `writetohdfs.yaml` 文件定义。 `dev.properties` 文件作为筛选器传递，该文件包含的值由拓扑读取。

## <a name="view-output-data"></a>查看输出数据

若要查看数据，请使用以下命令：

    hdfs dfs -ls /stormdata/

此时会显示此拓扑创建的文件列表。

以下列表是由前面的命令所返回的数据的示例：

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>停止拓扑

Storm 拓扑会一直运行，直到被停止或群集被删除。 若要停止拓扑，请使用以下命令：

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>后续步骤

了解如何使用 Storm 写入 Azure 存储和 Azure Data Lake Store 后，接下来请学习其他 [HDInsight 的 Storm 示例](apache-storm-example-topology.md)。


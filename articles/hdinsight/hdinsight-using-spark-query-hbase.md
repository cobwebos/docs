---
title: 使用 Spark 来读写 HBase 数据 - Azure HDInsight
description: 使用 Spark HBase 连接器将 Spark 群集中的数据读写到 HBase 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/12/2020
ms.openlocfilehash: eb62cf099d7ccc133a207a843a8be3debf5c5454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308412"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>使用 Apache Spark 读取和写入 Apache HBase 数据

通常使用 Apache HBase 的低级别 API（扫描、获取和放置）或者通过 Apache Phoenix 使用 SQL 语法来查询 Apache HBase。 Apache 还提供 Apache Spark HBase 连接器。 对于查询和修改 HBase 存储的数据，使用该连接器是一种便捷高效的替代方式。

## <a name="prerequisites"></a>先决条件

* 部署在同一[虚拟网络](./hdinsight-plan-virtual-network-deployment.md)中的两个单独的 HDInsight 群集。 一个HBase 和一个至少安装了 Spark 2.1 (HDInsight 3.6) 的 Spark。 有关详细信息，请参阅[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)。

* 群集主存储的 URI 方案。 此方案将 wasb://用于 Azure Blob 存储， `abfs://` 适用于 Azure Data Lake Storage Gen1 的 Azure Data Lake Storage Gen2 或 adl://。 如果为 Blob 存储启用了安全传输，则 URI 将为 `wasbs://`。  另请参阅[安全传输](../storage/common/storage-require-secure-transfer.md)。

## <a name="overall-process"></a>整体进程

让 Spark 群集能够查询 HBase 群集的主要过程如下所示：

1. 在 HBase 中准备一些示例数据。
2. 从 HBase 群集配置文件夹 (/etc/hbase/conf) 获取 hbase-site.xml 文件，并将 hbase-site.xml 的副本放入 Spark 2 配置文件夹 (/etc/spark2/conf)。 （可选：使用 HDInsight 团队提供的脚本来自动执行此过程）
4. 运行 `spark-shell`，在 `packages` 中按 Maven 坐标来引用 Spark HBase 连接器。
5. 定义将架构从 Spark 映射到 HBase 的目录。
6. 使用 RDD 或 DataFrame API 与 HBase 数据进行交互。

## <a name="prepare-sample-data-in-apache-hbase"></a>在 Apache HBase 中准备示例数据

此步骤中，将在 Apache HBase 中创建并填充一个表，然后可使用 Spark 对其进行查询。

1. 使用 `ssh` 命令连接到 HBase 群集。 编辑以下命令，将 `HBASECLUSTER` 替换为 HBase 群集的名称，然后输入该命令：

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. 使用 `hbase shell` 命令启动 HBase 交互式 shell。 在 SSH 连接中输入以下命令。

    ```bash
    hbase shell
    ```

3. 使用 `create` 命令创建包含双列系列的 HBase 表。 输入以下命令：

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. 使用 `put` 命令将指定列中的值插入特定表中的指定行。 输入以下命令：

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. 使用 `exit` 命令停止 HBase 交互式 shell。 输入以下命令：

    ```hbase
    exit
    ```
    
## <a name="run-scripts-to-set-up-connection-between-clusters"></a>运行脚本以设置群集之间的连接

若要设置群集之间的通信，请执行以下步骤，在群集上运行两个脚本。 这些脚本将自动执行下面“手动设置通信”一节中所述的文件复制过程。 

* 从 HBase 群集运行的脚本会将 `hbase-site.xml` 和 HBase IP 映射信息上传到 Spark 群集所附加的默认存储。 
* 从 Spark 群集运行的脚本设置两个 cron 作业，以定期运行两个帮助器脚本：  
    1.  HBase cron 作业– `hbase-site.xml` 从 Spark 默认存储帐户向本地节点下载新文件和 HBASE IP 映射
    2.  Spark cron 作业–检查是否发生了 Spark 缩放以及群集是否安全。 如果是，则编辑 `/etc/hosts` 以包含本地存储的 HBase IP 映射

__注意__：在继续操作之前，请确保已将 Spark 群集的存储帐户作为辅助存储帐户添加到 HBase 群集。 请确保按以下所示顺序运行脚本。


1. 在 HBase 群集上使用[脚本操作](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)以应用更改（考虑以下因素）： 


    |properties | Value |
    |---|---|
    |Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-hbase.sh`|
    |节点类型|区域|
    |parameters|`-s SECONDARYS_STORAGE_URL`|
    |持久化|是|

    * `SECONDARYS_STORAGE_URL` 是 Spark 端默认存储的 URL。 参数示例：`-s wasb://sparkcon-2020-08-03t18-17-37-853z@sparkconhdistorage.blob.core.windows.net`


2.  在 Spark 群集上使用脚本操作以应用更改（考虑以下因素）：

    |properties | Value |
    |---|---|
    |Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-spark.sh`|
    |节点类型|头、辅助角色、Zookeeper|
    |parameters|`-s "SPARK-CRON-SCHEDULE"`（可选）`-h "HBASE-CRON-SCHEDULE"`（可选）|
    |持久化|是|


    * 可以指定希望此群集自动检查更新的频率。 默认值：-s "*/1 * * * * *"-h 0 (在此示例中，Spark cron 每分钟运行一次，而 HBase cron 不会运行) 
    * 由于默认情况下未设置 HBase cron，因此在对 HBase 群集执行缩放时需要重新运行此脚本。 如果 HBase 群集经常缩放，可以选择自动设置 HBase cron 作业。 例如：`-h "*/30 * * * *"` 将脚本配置为每 30 分钟执行一次检查。 这样将会定期运行 HBase cron 计划，以自动将公共存储帐户上的新 HBase 信息下载到本地节点。
    
    

## <a name="set-up-communication-manually-optional-if-provided-script-in-above-step-fails"></a>手动设置通信（可选，如果上述步骤中提供的脚本失败）

__注意：__ 每当其中一个群集经历缩放活动时，都需要执行这些步骤。

1. 将 hbase-site.xml 从本地存储复制到 Spark 群集默认存储所在的根目录。  编辑以下命令以反映配置。  然后，在与 HBase 群集建立的 SSH 会话中输入该命令：

    | 语法值 | 新值|
    |---|---|
    |[URI 方案](hdinsight-hadoop-linux-information.md#URI-and-scheme) | 修改此值以反映存储。  以下语法适用于启用了安全传输的 Blob 存储。|
    |`SPARK_STORAGE_CONTAINER`|替换为 Spark 群集使用的默认存储容器名称。|
    |`SPARK_STORAGE_ACCOUNT`|替换为 Spark 群集使用的默认存储帐户名称。|

    ```bash
    hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
    ```

2. 然后退出与 HBase 群集的 ssh 连接。

    ```bash
    exit
    ```


3. 使用 SSH 连接到 Spark 集群的头节点。 编辑以下命令，将 `SPARKCLUSTER` 替换为 Spark 群集的名称，然后输入该命令：

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

4. 输入以下命令，将 `hbase-site.xml` 从 Spark 群集的默认存储复制到群集本地存储上的 Spark 2 配置文件夹中：

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>运行 Spark Shell，引用 Spark HBase 连接器

完成上述步骤后，你应该能够运行 Spark shell，并引用相应版本的 Spark HBase 连接器。 若要找到适合群集方案的最新 Spark HBase 连接器核心版本，请参阅 [SHC 核心 存储库](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/)。

例如，下表列出了 HDInsight 团队当前使用的两个版本和相应的命令。 如果 HBase 和 Spark 的版本与表中指示的版本相同，则可以为群集使用相同的版本。 


1. 在与 Spark 群集建立的 SSH 会话中，输入以下命令以启动 Spark shell：

    |Spark 版本| HDI HBase 版本  | SHC 版本    |  Command  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3.6 (HBase 1.1) | 1.1.1-2.1-s_2.11    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2.4    | HDI 4.0 (HBase 2.0) | 1.1.0.3.1.2.2-1  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. 保持此 Spark shell 实例处于打开状态，并继续[定义目录和查询](#define-a-catalog-and-query)。 如果找不到对应于 SHC Core 存储库中的版本的 jar，请继续阅读。 

可以直接从 [spark-hbase-connector](https://github.com/hortonworks-spark/shc) GitHub 分支生成 jar。 例如，如果运行的是 Spark 2.3 和 HBase 1.1，请完成以下步骤：

1. 克隆存储库：

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. 转到分支-2.3：

    ```bash
    git checkout branch-2.3
    ```

3. 从分支生成（创建 .jar 文件）：

    ```bash
    mvn clean package -DskipTests
    ```
    
3. 运行以下命令（请确保更改与所生成的 .jar 文件相对应的 .jar 名称）：

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. 保持此 Spark shell 实例处于打开状态，并继续执行下一部分。 



## <a name="define-a-catalog-and-query"></a>定义目录和查询

在此步骤中，定义一个将架构从 Apache Spark 映射到 Apache HBase 的目录对象。  

1. 在打开的 Spark Shell 中，输入以下 `import` 语句：

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. 输入以下命令，以定义在 HBase 中创建的 Contacts 表的目录：

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    代码：  

    1. 定义名为 `Contacts` 的 HBase 表的目录架构。  
    1. 将 rowkey 标识为 `key`，并将 Spark 中使用的列名映射到 HBase 中使用的列族、列名和列类型。  
    1. 将 Rowkey 定义为具有 `rowkey` 的特定列族 `cf` 的命名列 (`rowkey`)。  

1. 输入以下命令，以定义一个在 HBase 中提供围绕 `Contacts` 表的 DataFrame 的方法：

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. 创建 DataFrame 的实例：

    ```scala
    val df = withCatalog(catalog)
    ```  

1. 查询 DataFrame：

    ```scala
    df.show()
    ```

    应看到如下两行数据：

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. 注册一个临时表，以便使用 Spark SQL 查询 HBase 表：

    ```scala
    df.createTempView("contacts")
    ```

1. 针对 `contacts` 表发出 SQL 查询：

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    应看到如下结果：

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>插入新数据

1. 若要插入新的 Contact 记录，请定义 `ContactRecord` 类：

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. 创建 `ContactRecord` 的实例并将其放在一个数组中：

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. 将新数据数组保存到 HBase：

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. 检查结果：

    ```scala  
    df.show()
    ```

    应看到如下输出：

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. 通过输入以下命令关闭 spark shell：

    ```scala
    :q
    ```

## <a name="next-steps"></a>后续步骤

* [Apache Spark HBase 连接器](https://github.com/hortonworks-spark/shc)

---
title: 使用 Spark 读取和写入 HBase 数据 - Azure HDInsight
description: 使用 Spark HBase 连接器将 Spark 群集中的数据读写到 HBase 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: e3f5cb726dddbdbfbd1b1f48c800ac681e7a174c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696551"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>使用 Apache Spark 读取和写入 Apache HBase 数据

通常使用 Apache HBase 的低级别 API（扫描、获取和放置）或者通过 Apache Phoenix 使用 SQL 语法来查询 Apache HBase。 Apache 还提供 Apache Spark HBase 连接器，这是一个查询并修改 HBase 存储的数据的方便且高效的替代方案。

## <a name="prerequisites"></a>必备组件

* 两个独立的 HDInsight 群集、一个HBase、一个至少装有 Spark 2.1 (HDInsight 3.6) 的 Spark。
* Spark 集群需要按最低延迟直接与 HBase 集群进行通信，因此推荐的配置是将两个集群都部署在同一个虚拟网络中。 有关详细信息，请参阅[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)。
* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。
* 群集主存储的 [URI 方案](hdinsight-hadoop-linux-information.md#URI-and-scheme)。 这将是 wasb: / / Azure Blob 存储，abfs: / / 用于 Azure 数据湖存储第 2 代或 adl: / / 用于 Azure 数据湖存储 Gen1。 如果为 Blob 存储或 Data Lake Storage Gen2 启用了安全传输，则 URI 分别是 wasbs:// 或 abfss://。另请参阅[安全传输](../storage/common/storage-require-secure-transfer.md)。


## <a name="overall-process"></a>整体进程

启用 Spark 群集查询 HDInsight 群集的概述过程如下所示：

1. 在 HBase 中准备一些示例数据。
2. 从 HBase 集群配置文件夹 (/etc/hbase/conf) 中获取 hbase-site.xml 文件。
3. 将 hbase-site.xml 的副本放在 Spark 2 配置文件夹 (/etc/spark2/conf)。
4. 运行 `spark-shell`，在 `packages` 中按 Maven 坐标来引用 Spark HBase 连接器。
5. 定义将架构从 Spark 映射到 HBase 的目录。
6. 使用 RDD 或 DataFrame API 与 HBase 数据进行交互。

## <a name="prepare-sample-data-in-apache-hbase"></a>在 Apache HBase 中准备示例数据

此步骤中，在 Apache HBase 中创建并填充一个简单的表，然后可使用 Spark 来查询。

1. 使用 SSH 连接到 HBase 集群的头节点。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)。  编辑以下命令，将 `HBASECLUSTER` 替换为 HBase 群集的名称，将 `sshuser` 替换为 SSH 用户帐户名，然后输入该命令。

    ```
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. 输入以下命令，以启动 HBase shell：

        hbase shell

3. 输入以下命令，以创建包含列系列 `Personal` 和 `Office` 的 `Contacts` 表：

        create 'Contacts', 'Personal', 'Office'

4. 输入以下命令，以加载几行示例数据：

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

5. 输入以下命令，以退出 HBase shell：

        exit 

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>将 hbase-site.xml 复制到 Spark 群集
将 hbase-site.xml 从本地存储复制到 Spark 群集默认存储所在的根目录。  编辑以下命令以反映配置。  然后，在与 HBase 群集建立的 SSH 会话中输入该命令：

| 语法值 | 新值|
|---|---|
|[URI 方案](hdinsight-hadoop-linux-information.md#URI-and-scheme) | 修改此值以反映存储。  以下语法适用于启用了安全传输的 Blob 存储。|
|`SPARK_STORAGE_CONTAINER`|替换为 Spark 群集使用的默认存储容器名称。|
|`SPARK_STORAGE_ACCOUNT`|替换为 Spark 群集使用的默认存储帐户名称。|

```
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>将 hbase-site.xml 放置于 Spark 集群上

1. 使用 SSH 连接到 Spark 集群的头节点。

2. 输入以下命令，将 `hbase-site.xml` 从 Spark 群集的默认存储复制到群集本地存储上的 Spark 2 配置文件夹中：

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>运行 Spark Shell，引用 Spark HBase 连接器

1. 在与 Spark 群集建立的 SSH 会话中，输入以下命令以启动 Spark shell：

    ```
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. 保持此 Spark Shell 实例处于打开状态，并继续执行下一步操作。

## <a name="define-a-catalog-and-query"></a>定义目录和查询

在此步骤中，定义一个将架构从 Apache Spark 映射到 Apache HBase 的目录对象。  

1. 在打开的 Spark Shell 中，输入以下 `import` 语句：

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. 输入以下命令，以定义在 HBase 中创建的 Contacts 表的目录：

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

    该代码执行以下操作：  

     a. 定义名为 `Contacts` 的 HBase 表的目录架构。  
     b. 将 rowkey 标识为 `key`，并将 Spark 中使用的列名映射到 HBase 中使用的列族、列名和列类型。  
     c. Rowkey 还必须详细定义为具有 `rowkey` 的特定列族 `cf` 的命名列 (`rowkey`)。  

3. 输入以下命令，以定义一个在 HBase 中提供围绕 `Contacts` 表的 DataFrame 的方法：

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. 创建 DataFrame 的实例：

    ```scala
    val df = withCatalog(catalog)
    ```  

5. 查询 DataFrame：

    ```scala
    df.show()
    ```

6. 应看到如下两行数据：

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. 注册一个临时表，以便使用 Spark SQL 查询 HBase 表：

    ```scala
    df.createTempView("contacts")
    ```

8. 针对 `contacts` 表发出 SQL 查询：

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. 应看到如下结果：

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

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

2. 创建 `ContactRecord` 的实例并将其放在一个数组中：

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. 将新数据数组保存到 HBase：

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. 检查结果：

    ```scala  
    df.show()
    ```

5. 应看到如下输出：

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>后续步骤

* [Apache Spark HBase 连接器](https://github.com/hortonworks-spark/shc)

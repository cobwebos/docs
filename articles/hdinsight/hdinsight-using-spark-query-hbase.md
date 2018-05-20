---
title: 使用 Spark 来读写 HBase 数据 - Azure HDInsight | Microsoft Docs
description: 使用 Spark HBase 连接器将 Spark 群集中的数据读写到 HBase 群集。
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 7cfc7f586e8a92c29736a7c4cff0b12796be430a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>使用 Spark 读取和写入 HBase 数据

通常使用 Apache HBase 的低级别 API（扫描、获取和放置）或者通过 Phoenix 使用 SQL 语法来查询 Apache HBase。 Apache 还提供 Spark HBase 连接器，这是一个查询并修改 HBase 存储的数据的方便且有效的替代方案。

## <a name="prerequisites"></a>先决条件

* 两个独立的 HDInsight 群集、一个HBase、一个装有 Spark 2.1 (HDInsight 3.6) 的 Spark。
* Spark 集群需要按最低延迟直接与 HBase 集群进行通信，因此推荐的配置是将两个集群都部署在同一个虚拟网络中。 有关详细信息，请参阅[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)。
* 针对每个群集的 SSH 访问。
* 访问每个群集的默认存储。

## <a name="overall-process"></a>整体进程

启用 Spark 群集查询 HDInsight 群集的概述过程如下所示：

1. 在 HBase 中准备一些示例数据。
2. 从 HBase 集群配置文件夹 (/etc/hbase/conf) 中获取 hbase-site.xml 文件。
3. 将 hbase-site.xml 的副本放在 Spark 2 配置文件夹 (/etc/spark2/conf)。
4. 运行 `spark-shell`，在 `packages` 中按 Maven 坐标来引用 Spark HBase 连接器。
5. 定义将架构从 Spark 映射到 HBase 的目录。
6. 使用 RDD 或 DataFrame API 与 HBase 数据进行交互。

## <a name="prepare-sample-data-in-hbase"></a>在 HBase 中准备示例数据

此步骤中，在 HBase 中创建并填充一个简单的表，然后可使用 Spark 来查询。

1. 使用 SSH 连接到 HBase 集群的头节点。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)。
2. 运行 HBase shell：

        hbase shell

3. 使用列族 `Personal` 和 `Office` 创建一个 `Contacts` 表：

        create 'Contacts', 'Personal', 'Office'

4. 加载一些示例数据行：

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>从 HBase 集群中获取 hbase-site.xml

1. 使用 SSH 连接到 HBase 集群的头节点。
2. 将 hbase-site.xml 从本地存储复制到 HBase 集群的默认存储的根目录：

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. 使用 [Azure 门户](https://portal.azure.com)导航到 HBase 群集。
4. 选择“存储帐户”。 

    ![存储帐户](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. 选择列表中在“默认”列下有复选标记的“存储帐户”。

    ![默认存储器帐户](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. 在“存储帐户”窗格上，选择“Blob”磁贴。

    ![“Blob”磁贴](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. 在容器列表中，选择 HBase 集群使用的容器。
8. 在文件列表中，选择 `hbase-site.xml`。

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. 在“Blob 属性”面板上，选择“下载”并将 `hbase-site.xml` 保存到本地计算机上的某个位置。

    ![下载](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>将 hbase-site.xml 放置于 Spark 集群上

1. 使用 [Azure 门户](https://portal.azure.com)导航到 Spark 群集。
2. 选择“存储帐户”。

    ![存储帐户](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. 选择列表中在“默认”列下有复选标记的“存储帐户”。

    ![默认存储器帐户](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. 在“存储帐户”窗格上，选择“Blob”磁贴。

    ![“Blob”磁贴](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. 在容器列表中，选择 Spark 集群使用的容器。
6. 选择“上传”。

    ![上载](./media/hdinsight-using-spark-query-hbase/upload.png)

7. 选择之前下载到本地计算机的 `hbase-site.xml` 文件。

    ![上传 hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. 选择“上传”。
9. 使用 SSH 连接到 Spark 集群的头节点。
10. 将 `hbase-site.xml` 从 Spark 群集的默认存储复制到群集本地存储上的 Spark 2 配置文件夹中：

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>运行 Spark Shell，引用 Spark HBase 连接器

1. 使用 SSH 连接到 Spark 集群的头节点。
2. 启动 spark shell，指定 Spark HBase 连接器包：

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11 --repositories http://repo.hortonworks.com/coroups/public/

3. 保持此 Spark Shell 实例处于打开状态，并继续执行下一步操作。

## <a name="define-a-catalog-and-query"></a>定义目录和查询

在此步骤中，定义一个将架构从 Spark 映射到 HBase 的目录对象。 

1. 在打开的 Spark Shell 中，运行以下 `import` 语句：

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. 定义在 HBase 中创建的 Contacts 表的目录：
    1. 定义名为 `Contacts` 的 HBase 表的目录架构。
    2. 将 rowkey 标识为 `key`，并将 Spark 中使用的列名映射到 HBase 中使用的列族、列名和列类型。
    3. Rowkey 还必须详细定义为具有 `rowkey` 的特定列族 `cf` 的命名列 (`rowkey`)。

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

3. 定义一个在 HBase 中提供围绕 `Contacts` 表的 DataFrame 的方法：

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. 创建 DataFrame 的实例：

        val df = withCatalog(catalog)

5. 查询 DataFrame：

        df.show()

6. 应看到如下两行数据：

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. 注册一个临时表，以便使用 Spark SQL 查询 HBase 表：

        df.registerTempTable("contacts")

8. 针对 `contacts` 表发出 SQL 查询：

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. 应看到如下结果：

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>插入新数据

1. 若要插入新的 Contact 记录，请定义 `ContactRecord` 类：

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. 创建 `ContactRecord` 的实例并将其放在一个数组中：

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. 将新数据数组保存到 HBase：

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. 检查结果：
    
        df.show()

5. 应看到如下输出：

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>后续步骤

* [Spark HBase 连接器](https://github.com/hortonworks-spark/shc)

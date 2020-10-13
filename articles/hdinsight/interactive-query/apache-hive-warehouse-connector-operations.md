---
title: Azure HDInsight 中的 Hive Warehouse Connector 支持的 Apache Spark 操作
description: 了解 Azure HDInsight 上的 Hive Warehouse Connector 的不同功能。
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: ed3dbe4cb5b9d8b50c028a68feeded170130bfb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085831"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Azure HDInsight 中的 Hive Warehouse Connector 支持的 Apache Spark 操作

本文介绍 Hive Warehouse Connector (HWC) 支持的基于 Spark 的操作。 下面显示的所有示例都将通过 Apache Spark shell 执行。

## <a name="prerequisite"></a>先决条件

完成 [Hive Warehouse Connector 设置](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)步骤。

## <a name="getting-started"></a>入门

若要启动 spark-shell 会话，请执行以下步骤：

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到 Apache Spark 群集。 编辑以下命令，将 CLUSTERNAME 替换为群集的名称，然后输入该命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 在 ssh 会话中，执行以下命令以记下 `hive-warehouse-connector-assembly` 版本：

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. 使用上面标识的 `hive-warehouse-connector-assembly` 版本编辑下面的代码。 然后执行命令启动 spark shell：

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. 启动 spark shell 后，可以使用以下命令启动 Hive Warehouse Connector 实例：

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>使用 Hive 查询创建 Spark 数据帧

使用 HWC 库的所有查询的结果都将作为数据帧返回。 以下示例演示如何创建基本的 hive 查询。

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

查询的结果是 Spark 数据帧，可以与 MLIB 和 SparkSQL 等 Spark 库一起使用。

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>将 Spark 数据帧写入 Hive 表

Spark 本身不支持写入 Hive 的托管 ACID 表。 但是，使用 HWC 可以将任何数据帧写入 Hive 表。 可以在以下示例中看到此功能的工作方式：

1. 创建名为 `sampletable_colorado` 的表，并使用以下命令指定其列：

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. 筛选列 `state` 等于 `Colorado` 的表 `hivesampletable`。 此 hive 查询使用 `write` 函数返回存储在 Hive 表 `sampletable_colorado` 中的 Spark 数据帧 ans sis。

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. 通过以下命令查看结果：

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![hive warehouse connector 显示 hive 表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)


## <a name="structured-streaming-writes"></a>结构化流写入

使用 Hive Warehouse Connector，可以使用 Spark 流将数据写入 Hive 表。

> [!IMPORTANT]
> 启用 ESP 的 Spark 4.0 群集不支持结构化流写入。

按照以下步骤将数据从 localhost 端口 9999 上的 Spark 流引入到 Hive 表中。 Hive Warehouse Connector。

1. 从打开的 Spark shell 中，使用以下命令启动 spark 流：

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. 通过执行以下步骤，为创建的 Spark 流生成数据：
    1. 在同一个 Spark 群集上打开第二个 SSH 会话。
    1. 在命令提示符下键入 `nc -lk 9999`。 此命令使用 netcat 实用程序将数据从命令行发送到指定端口。

1. 返回到第一个 SSH 会话并创建一个新 Hive 表来保存流数据。 在 spark shell 中，输入以下命令：

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 然后使用以下命令将流数据写入新创建的表：

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > 由于 Apache Spark 中的已知问题，目前必须手动设置 `metastoreUri` 和 `database` 选项。 有关此问题的详细信息，请参阅 [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)。

1. 返回到第二个 SSH 会话，并输入以下值：

    ```bash
    foo
    HiveSpark
    bar
    ```

1. 返回到第一个 SSH 会话并记下简短的活动。 使用以下命令查看数据：

    ```scala
    hive.table("stream_table").show()
    ```

使用“Ctrl + C”在第二个 SSH 会话上停止 netcat。 使用 `:q` 在第一个 SSH 会话中退出 spark shell。

## <a name="next-steps"></a>后续步骤

* [将 HWC 与 Apache Spark 和 Apache Hive 集成](./apache-hive-warehouse-connector.md)
* [将交互式查询与 HDInsight 配合使用](./apache-interactive-query-get-started.md)
* [将 HWC 与 Apache Zeppelin 集成](./apache-hive-warehouse-connector-zeppelin.md)

---
title: Apache Spark & Hive-Hive 仓库连接器-Azure HDInsight
description: 了解如何将 Apache Spark 和 Apache Hive 与 Azure HDInsight 上的 Hive 仓库连接器相集成。
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 2448550cf35f92bc8d91bc6ad9d5b22cc90b5ae0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494309"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>将 Apache Spark 和 Apache Hive 与 Hive 仓库连接器相集成

Apache Hive 仓库连接器 (HWC) 是一个库，可让你更轻松地使用 Apache Spark 和 Apache Hive 来为多种任务提供支持，例如，在 Spark 数据帧与 Hive 表之间移动数据，以及将 Spark 流数据定向到 Hive 表中。 Hive 仓库连接器如同 Spark 与 Hive 之间的桥梁。 它支持使用 Scala、Java 和 Python 进行开发。

Hive 仓库连接器可让你利用 Hive 和 Spark 的独特功能来生成强大的大数据应用程序。 Apache Hive 为原子性、一致性、隔离性和持久性 (ACID) 数据库事务提供支持。 有关 Hive 中的 ACID 和事务的详细信息，请参阅 [Hive 事务](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)。 Hive 还通过 Apache Ranger 和低延迟分析处理来提供精细的安全控制，而 Apache Spark 无法提供此类功能。

Apache Spark 中的结构化流式处理 API 可以提供流式处理功能，而 Apache Hive 则无法提供此类功能。 从 HDInsight 4.0 开始，Apache Spark 2.3.1 和 Apache Hive 3.1.0 使用单独的元存储，这可能会增大互操作性的难度。 使用 Hive 仓库连接器可以更轻松地将 Spark 与 Hive 一起使用。 HWC 库将数据从 LLAP 守护程序并行加载到 Spark 执行器，这比使用从 Spark 到 Hive 的标准 JDBC 连接更为有效，且更具可伸缩性。

![hive 仓库连接器体系结构](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive 仓库连接器支持的部分操作如下：

* 描述表
* 为 ORC 格式的数据创建表
* 选择 Hive 数据和检索数据帧
* 将数据帧批量写入到 Hive
* 执行 Hive 更新语句
* 从 Hive 读取表数据、在 Spark 中转换数据，然后将数据写入到新的 Hive 表
* 使用 HiveStreaming 将数据帧或 Spark 流写入到 Hive

## <a name="hive-warehouse-connector-setup"></a>Hive 仓库连接器设置

按照以下步骤在 Azure HDInsight 中的 Spark 和交互式查询群集之间设置 Hive 仓库连接器：

### <a name="create-clusters"></a>创建群集

1. 使用存储帐户和自定义 Azure 虚拟网络创建 HDInsight Spark **4.0**群集。 有关在 Azure 虚拟网络中创建群集的信息，请参阅[将 HDInsight 添加到现有的虚拟网络](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)。

1. 使用与 Spark 群集相同的存储帐户和 Azure 虚拟网络创建 HDInsight 交互式查询（LLAP） **4.0**群集。

### <a name="modify-hosts-file"></a>修改主机文件

从交互式查询群集的 headnode0 上的 `/etc/hosts` 文件复制节点信息，并将信息连接到 Spark 群集 headnode0 上的 `/etc/hosts` 文件。 执行此步骤可让 Spark 群集解析交互式查询群集中节点的 IP 地址。 使用 `cat /etc/hosts` 查看已更新文件的内容。 最终输出应类似于下面的屏幕截图中所示的内容。

![hive 仓库连接器主机文件](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>收集初步信息

#### <a name="from-your-interactive-query-cluster"></a>从交互式查询群集

1. 使用 `https://LLAPCLUSTERNAME.azurehdinsight.net` 定位到群集的 Apache Ambari 主页，其中 `LLAPCLUSTERNAME` 是交互式查询群集的名称。

1. 导航到**hive** > 配置 > **高级** > **高级 Hive-site** > **zookeeper** ，并记下该值。 该值可能类似于： `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`。

1. 导航到**hive** > 配置 > **Advanced** > **General** > **元存储**并记下该值。 该值可能类似于： `thrift://hn0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`。

#### <a name="from-your-apache-spark-cluster"></a>从 Apache Spark 群集

1. 使用 `https://SPARKCLUSTERNAME.azurehdinsight.net` 定位到群集的 Apache Ambari 主页，其中 `SPARKCLUSTERNAME` 是 Apache Spark 群集的名称。

1. 导航到**Hive** > 配置 > **高级** > **高级 hive-交互站点** > **llap** ，并记下该值。 该值可能类似于： `@llap0`。

### <a name="configure-spark-cluster-settings"></a>配置 Spark 群集设置

在 Spark Ambari web UI 中，导航到**custom-spark2-defaults** > **配置** > **自定义 custom-spark2-defaults**。

![Apache Ambari Custom-spark2-defaults 配置](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

根据需要选择 "**添加属性 ...** "，添加/更新以下内容：

| 键 | 值 |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|你之前从**llap**中获取的值。|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`。 设置为 JDBC 连接字符串，它连接到交互式查询群集上的 Hiveserver2。 将 `LLAPCLUSTERNAME` 替换为交互式查询群集的名称。 将 `PWD` 替换为实际密码。|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`。 设置为合适的 HDFS 兼容暂存目录。 如果你有两个不同的群集，则暂存目录应是 LLAP 群集存储帐户的暂存目录中的某个文件夹，这样，HiveServer2 才能访问它。  将 `STORAGE_ACCOUNT_NAME` 替换为群集使用的存储帐户的名称，并将 `STORAGE_CONTAINER_NAME` 替换为存储容器的名称。|
|`spark.datasource.hive.warehouse.metastoreUri`|之前从**元存储**获取的值。|
|`spark.security.credentials.hiveserver2.enabled`|对于 YARN 客户端部署模式 `false`。|
|`spark.hadoop.hive.zookeeper.quorum`|之前从**zookeeper**获取的值。|

根据需要保存更改并重新启动组件。

## <a name="using-the-hive-warehouse-connector"></a>使用 Hive 仓库连接器

### <a name="connecting-and-running-queries"></a>连接和运行查询

可以通过多种不同的方法使用 Hive 仓库连接器来连接到交互式查询群集并执行查询。 支持的方法包括以下工具：

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

本文中的所有示例将通过 spark-shell 执行。

若要启动 spark-shell 会话，请执行以下步骤：

1. 通过 SSH 连接到 Apache Spark 群集的头节点。 有关使用 SSH 连接到群集的详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 输入以下命令启动 spark shell：

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.2.1-8.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    此时会出现一条欢迎消息，以及一条可以输入命令的 `scala>` 提示。

1. 启动 spark-shell 后，可以使用以下命令启动 Hive 仓库连接器实例：

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>在企业安全性套餐 (ESP) 群集上连接和运行查询

企业安全性套餐 (ESP) 为 Azure HDInsight 中的 Apache Hadoop 群集提供企业级功能，例如，基于 Active Directory 的身份验证、多用户支持和基于角色的访问控制。 有关 ESP 的详细信息，请参阅[在 HDInsight 中使用企业安全性套餐](../domain-joined/apache-domain-joined-architecture.md)。

1. 通过 SSH 连接到 Apache Spark 群集的头节点。 有关使用 SSH 连接到群集的详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 键入 `kinit` 并使用域用户登录。

1. 使用配置参数的完整列表启动 spark-shell，如下所示。 必须根据群集指定尖括号中所有全大写的值。 如果需要了解以下任何参数的输入值，请参阅有关 [Hive 仓库连接器设置](#hive-warehouse-connector-setup)的部分。

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>基于 Hive 查询创建 Spark 数据帧

使用 HWC 库的所有查询的结果将作为数据帧返回。 以下示例演示如何创建基本查询。

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

查询的结果是可与 MLIB 和 SparkSQL 等 Spark 库配合使用的 Spark 数据帧。

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>将 Spark 数据帧写出到 Hive 表

Spark 原生并不支持写入到 Hive 管理的 ACID 表。 但是，使用 HWC 可将任何数据帧写出到 Hive 表。 以下示例演示了此功能的用法：

1. 创建名为 `sampletable_colorado` 的表，并使用以下命令指定其列：

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. 筛选列 `hivesampletable` 等于 `state` 的表 `Colorado`。 此 Hive 表查询将作为 Spark 数据帧返回。 然后使用 `sampletable_colorado` 函数将数据帧保存在 Hive 表 `write` 中。

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. 通过以下命令查看结果：

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![hive 仓库连接器显示 hive 表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>结构化流写入

使用 Hive 仓库连接器可以通过 Spark 流式传输将数据写入 Hive 表。

遵循以下步骤创建一个 Hive 仓库连接器示例，用于在 localhost 端口 9999 上将 Spark 流中的数据引入 Hive 表。

1. 按照 "[连接和运行查询](#connecting-and-running-queries)" 中的步骤进行操作。

1. 开始使用以下命令执行 Spark 流式传输：

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. 执行以下步骤，为创建的 Spark 流生成数据：
    1. 在同一 Spark 群集上打开另一个 SSH 会话。
    1. 在命令提示符处，键入：`nc -lk 9999`。 此命令使用 netcat 实用工具通过命令行将数据发送到指定的端口。

1. 返回到第一个 SSH 会话，并创建一个新的 Hive 表来保存流式处理数据。 在 spark shell 中，输入以下命令：

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 然后使用以下命令将流数据写入新创建的表：

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > 由于 Apache Spark 的已知问题，目前必须手动设置 `metastoreUri` 和 `database` 选项。 有关此问题的详细信息，请参阅 [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)。

1. 返回到第二个 SSH 会话，并输入以下值：

    ```bash
    foo
    HiveSpark
    bar
    ```

1. 返回到第一个 SSH 会话并记下 brief 活动。 使用以下命令查看数据：

    ```scala
    hive.table("stream_table").show()
    ```

使用**Ctrl + C**停止第二个 SSH 会话上的 netcat。 使用 `:q` 可在第一个 SSH 会话上退出 spark shell。

### <a name="securing-data-on-spark-esp-clusters"></a>在 Spark ESP 群集上保护数据

1. 输入以下命令，创建包含一些示例数据的 `demo` 表：

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 使用以下命令查看该表的内容。 在应用策略之前，`demo` 表会显示完整的列。

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![应用 Ranger 策略之前的演示表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 应用仅显示该列最后四个字符的列掩码策略。  
    1. 转到 Ranger 管理 UI (`https://CLUSTERNAME.azurehdinsight.net/ranger/`)。
    1. 在“Hive”下单击群集的 Hive 服务。
        ![ranger service manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. 单击 "**屏蔽**" 选项卡，然后单击 "**添加新策略**"

        ![hive 仓库连接器 ranger hive 策略列表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. 提供所需的策略名称。 选择数据库：**默认**，hive 表：**演示**，hive 列：**名称**，用户： **rsadmin2**，访问类型： **Select**，和**部分掩码：** 从 "**选择屏蔽" 选项**菜单中显示最后4项。 单击“添加”。
                ![创建策略](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 再次查看该表的内容。 应用 Ranger 策略之后，我们只能看到该列的最后四个字符。

    ![应用 Ranger 策略之后的演示表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>后续步骤

* [将交互式查询与 HDInsight 配合使用](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [有关使用 Zeppelin、Livy、spark-submit 和 pyspark 集成 Hive 仓库连接器的示例](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)

---
title: Apache Spark & Hive-Hive 仓库连接器-Azure HDInsight
description: 了解如何在 Azure HDInsight 上将 Apache Spark 和 Apache Hive 与 Hive 仓库连接器集成。
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 47bcc9a4f906fa1e0cc0560cdbd2e0cebec481ab
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435374"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>将 Apache Spark 和 Apache Hive 与 Hive 仓库连接器集成

Apache Hive 仓库连接器（HWC）是一个库，通过支持在 Spark DataFrames 和 Hive 表之间移动数据以及将 Spark 流式传输数据定向到 Hive 表中的任务，你可以更轻松地使用 Apache Spark 和 Apache Hive。 Hive 仓库连接器的工作方式类似于 Spark 与 Hive 之间的桥梁。 它支持 Scala、Java 和 Python 进行开发。

使用 Hive 仓库连接器，可利用 Hive 和 Spark 的独特功能构建功能强大的大数据应用程序。 Apache Hive 为原子性、一致性、隔离性和持久性（ACID）的数据库事务提供支持。 有关 Hive 中的 ACID 和事务的详细信息，请参阅[Hive 事务](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)。 Hive 还通过 Apache Ranger 和低延迟分析处理提供详细的安全控制，Apache Spark 中不可用。

Apache Spark 提供了结构化流式处理 API，可提供 Apache Hive 中不可用的流式处理功能。 从 HDInsight 4.0 开始，Apache Spark 2.3.1 和 Apache Hive 3.1.0 有单独的元存储，这会使互操作性变得困难。 使用 Hive Warehouse Connector 可以更轻松地将 Spark 和 Hive 结合使用。 HWC 库将数据从 LLAP 守护程序并行加载到 Spark 执行器，使其比使用从 Spark 到 Hive 的标准 JDBC 连接更有效、更具可扩展性。

![hive 仓库连接器体系结构](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive Warehouse Connector 支持的部分操作包括：

* 描述表
* 为 ORC 格式的数据创建表
* 选择 Hive 数据并检索数据帧
* 将数据帧批量写入 Hive
* 执行 Hive 更新语句
* 从 Hive 读取表数据，在 Spark 中转换数据，然后将数据写入新的 Hive 表
* 使用 HiveStreaming 将数据帧或 Spark 流写入 Hive

## <a name="hive-warehouse-connector-setup"></a>Hive 仓库连接器设置

按照以下步骤在 Azure HDInsight 中的 Spark 和交互式查询群集之间设置 Hive 仓库连接器：

### <a name="create-clusters"></a>创建群集

1. 使用存储帐户和自定义 Azure 虚拟网络创建 HDInsight Spark **4.0**群集。 若要了解如何在 Azure 虚拟网络中创建群集，请参阅[将 HDInsight 添加到现有虚拟网络](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)。

1. 使用与 Spark 群集相同的存储帐户和 Azure 虚拟网络创建 HDInsight 交互式查询（LLAP） **4.0**群集。

### <a name="modify-hosts-file"></a>修改主机文件

从交互式查询群集的 headnode0 上的 `/etc/hosts` 文件复制节点信息，并将信息连接到 Spark 群集 headnode0 上的 `/etc/hosts` 文件。 此步骤将允许 Spark 群集解析交互式查询群集中节点的 IP 地址。 查看 `cat /etc/hosts`的已更新文件的内容。 最终输出应类似于下面的屏幕截图中所示的内容。

![hive 仓库连接器主机文件](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>收集初步信息

#### <a name="from-your-interactive-query-cluster"></a>从交互式查询群集

1. 使用 `https://LLAPCLUSTERNAME.azurehdinsight.net` 导航到群集的 Apache Ambari 主页，其中 `LLAPCLUSTERNAME` 是交互式查询群集的名称。

1. 导航到**hive** > 配置 > **高级** > **高级 Hive-site** > **zookeeper** ，并记下该值。 该值可能类似于： `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`。

1. 导航到**hive** > 配置 > **Advanced** > **General** > **元存储**并记下该值。 该值可能类似于： `thrift://hn0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`。

#### <a name="from-your-apache-spark-cluster"></a>从 Apache Spark 群集

1. 使用 `https://SPARKCLUSTERNAME.azurehdinsight.net` 导航到群集的 Apache Ambari 主页，其中 `SPARKCLUSTERNAME` 是 Apache Spark 群集的名称。

1. 导航到**Hive** > 配置 > **高级** > **高级 hive-交互站点** > **llap** ，并记下该值。 该值可能类似于： `@llap0`。

### <a name="configure-spark-cluster-settings"></a>配置 Spark 群集设置

在 Spark Ambari web UI 中，导航到**custom-spark2-defaults** > **配置** > **自定义 custom-spark2-defaults**。

![Apache Ambari Custom-spark2-defaults 配置](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

根据需要选择 "**添加属性 ...** "，添加/更新以下内容：

| 密钥 | 值 |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|你之前从**llap**中获取的值。|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`。 设置为 JDBC 连接字符串，它连接到交互式查询群集上的 Hiveserver2。 将 `LLAPCLUSTERNAME` 替换为您的交互式查询群集的名称。 将 `PWD` 替换为实际密码。|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`。 设置为合适的 HDFS 兼容暂存目录。 如果有两个不同的群集，则暂存目录应该是 LLAP 群集的存储帐户的暂存目录中的文件夹，以便 HiveServer2 有权访问它。  将 `STORAGE_ACCOUNT_NAME` 替换为群集使用的存储帐户的名称，并将 `STORAGE_CONTAINER_NAME` 替换为存储容器的名称。|
|`spark.datasource.hive.warehouse.metastoreUri`|之前从**元存储**获取的值。|
|`spark.security.credentials.hiveserver2.enabled`|用于 YARN 客户端部署模式的 `false`。|
|`spark.hadoop.hive.zookeeper.quorum`|之前从**zookeeper**获取的值。|

根据需要保存更改并重新启动组件。

## <a name="using-the-hive-warehouse-connector"></a>使用 Hive 仓库连接器

### <a name="connecting-and-running-queries"></a>连接和运行查询

可以在几种不同的方法之间进行选择，以连接到交互式查询群集并使用 Hive 仓库连接器执行查询。 支持的方法包括以下工具：

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

本文中提供的所有示例都将通过 spark shell 执行。

若要启动 spark shell 会话，请执行以下步骤：

1. 通过 SSH 连接到 Apache Spark 群集的头节点。 有关使用 SSH 连接到群集的详细信息，请参阅[使用 Ssh 连接到 HDInsight （Apache Hadoop）](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 输入以下命令以启动 spark shell：

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    你将看到一条欢迎消息和一个 `scala>` 提示，你可以在其中输入命令。

1. 启动 spark shell 后，可以使用以下命令启动 Hive 仓库连接器实例：

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>在企业安全性套餐（ESP）群集上连接和运行查询

企业安全性套餐（ESP）提供企业级功能，如基于 Active Directory 的身份验证、多用户支持和 Azure HDInsight 中 Apache Hadoop 群集的基于角色的访问控制。 有关 ESP 的详细信息，请参阅[在 HDInsight 中使用企业安全性套餐](../domain-joined/apache-domain-joined-architecture.md)。

1. 通过 SSH 连接到 Apache Spark 群集的头节点。 有关使用 SSH 连接到群集的详细信息，请参阅[使用 Ssh 连接到 HDInsight （Apache Hadoop）](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 键入 `kinit` 并使用域用户登录。

1. 启动 spark-shell，并显示完整的配置参数列表，如下所示。 必须根据群集指定尖括号中所有大写字母的所有值。 如果需要确定要为以下任何参数输入的值，请参阅 " [Hive 仓库连接器设置](#hive-warehouse-connector-setup)" 部分。

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>从 Hive 查询创建 Spark DataFrames

使用 HWC 库的所有查询的结果都作为数据帧返回。 下面的示例演示如何创建基本查询。

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

查询的结果是 Spark DataFrames，它可用于 Spark 库，如 MLIB 和 SparkSQL。

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>将 Spark DataFrames 写到 Hive 表

Spark 不支持写入 Hive 的托管 ACID 表。 不过，使用 HWC 时，可以将任何数据帧写出到 Hive 表。 在以下示例中，可以看到此功能在工作中：

1. 使用以下命令创建名为 `sampletable_colorado` 的表并指定其列：

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. 筛选列 `state` 等于 `Colorado``hivesampletable` 表。 Hive 表的这一查询以 Spark 数据帧的形式返回。 然后，使用 `write` 函数 `sampletable_colorado` 将数据帧保存在 Hive 表中。

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. 通过以下命令查看结果：

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![hive 仓库连接器显示 hive 表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>结构化流式写入

使用 Hive 仓库连接器，可以使用 Spark 流式处理将数据写入 Hive 表中。

按照以下步骤创建一个 Hive 仓库连接器示例，该示例将来自 localhost 端口9999上的 Spark 流的数据引入到 Hive 表中。

1. 按照 "[连接和运行查询](#connecting-and-running-queries)" 中的步骤进行操作。

1. 用以下命令开始 spark 流：

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. 通过执行以下步骤，为创建的 Spark 流生成数据：
    1. 在同一 Spark 群集上打开另一个 SSH 会话。
    1. 在命令提示符下键入 `nc -lk 9999`。 此命令使用 netcat 实用程序将数据从命令行发送到指定端口。

1. 返回到第一个 SSH 会话，并创建一个新的 Hive 表来保存流式处理数据。 在 spark shell 中，输入以下命令：

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 然后使用以下命令将流数据写入新创建的表：

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > 当前必须手动设置 `metastoreUri` 和 `database` 选项，因为 Apache Spark 中存在一个已知问题。 有关此问题的详细信息，请参阅[SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)。

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

使用**Ctrl + C**停止第二个 SSH 会话上的 netcat。 使用 `:q` 在第一个 SSH 会话上退出 spark shell。

### <a name="securing-data-on-spark-esp-clusters"></a>保护 Spark ESP 群集上的数据

1. 通过输入以下命令创建一个表 `demo`，其中包含一些示例数据：

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 通过以下命令查看表的内容。 在应用策略之前，`demo` 表将显示完整的列。

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![应用 ranger 策略之前的演示表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 应用只显示列的最后四个字符的列掩码策略。  
    1. 请在 `https://CLUSTERNAME.azurehdinsight.net/ranger/`中转到 Ranger 管理 UI。
    1. 在**hive**下，单击群集的 hive 服务。
        ![ranger service manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. 单击 "**屏蔽**" 选项卡，然后单击 "**添加新策略**"

        ![hive 仓库连接器 ranger hive 策略列表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 提供所需的策略名称。 选择数据库：**默认**，hive 表：**演示**，hive 列：**名称**，用户： **rsadmin2**，访问类型： **Select**，和**部分掩码：** 从 "**选择屏蔽" 选项**菜单中显示最后4项。 单击“添加”。
                ![创建策略](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 再次查看表的内容。 应用 ranger 策略后，只能看到列的最后四个字符。

    ![应用 ranger 策略后的演示表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>后续步骤

* [将交互式查询与 HDInsight 配合使用](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [使用 Zeppelin、Livy、spark-submit 和 pyspark 与 Hive 仓库连接器交互的示例](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)

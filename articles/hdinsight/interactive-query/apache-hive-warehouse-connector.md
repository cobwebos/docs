---
title: 将 Apache Spark 和 Apache Hive 与 Hive 仓库连接器相集成
description: 了解如何将 Apache Spark 和 Apache Hive 与 Azure HDInsight 上的 Hive 仓库连接器相集成。
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 8a946a75a2dbd487494d70d0fd195a5becf5bd5a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122206"
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

遵循以下步骤在 Azure HDInsight 中的 Spark 与交互式查询群集之间设置 Hive 仓库连接器：

1. 在 Azure 门户中使用存储帐户和自定义的 Azure 虚拟网络创建 HDInsight Spark 4.0 群集。 有关在 Azure 虚拟网络中创建群集的信息，请参阅[将 HDInsight 添加到现有的虚拟网络](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)。
1. 在 Azure 门户中使用与在 Spark 群集中所用的同一个存储帐户和 Azure 虚拟网络创建 HDInsight 交互式查询 (LLAP) 4.0 群集。
1. 将交互式查询群集的头节点 0 上的 `/etc/hosts` 文件内容，复制到 Spark 群集的头节点 0 上的 `/etc/hosts` 文件中。 执行此步骤可让 Spark 群集解析交互式查询群集中节点的 IP 地址。 使用 `cat /etc/hosts` 查看已更新文件的内容。 输出应如以下屏幕截图所示。

    ![hive 仓库连接器主机文件](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. 执行以下步骤配置 Spark 群集设置： 
    1. 转到 Azure 门户，选择 HDInsight 群集，然后单击群集名称。
    1. 在右侧的**群集仪表板**下，选择“Ambari 主页”。
    1. 在 Ambari Web UI 中，单击“SPARK2” > “配置” > “自定义 spark2-defaults”。

        ![Apache Ambari Custom-spark2-defaults 配置](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. 将`spark.hadoop.hive.llap.daemon.service.hosts`设置为与**llap** * * 高级 hive-interactive * * 下的属性相同的值。 例如： `@llap0`

    1. 将 `spark.sql.hive.hiveserver2.jdbc.url` 设置为 JDBC 连接字符串，用于连接到交互式查询群集上的 Hiveserver2。 群集的连接字符串如以下 URI 所示。 `CLUSTERNAME` 是 Spark 群集的名称，`user` 和 `password` 参数设置为群集的正确值。

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        > [!Note]
        > JDBC URL 应包含用于连接到 Hiveserver2 的凭据（包括用户名和密码）。

    1. 将 `spark.datasource.hive.warehouse.load.staging.dir` 设置为与 HDFS 兼容的适当暂存目录。 如果你有两个不同的群集，则暂存目录应是 LLAP 群集存储帐户的暂存目录中的某个文件夹，这样，HiveServer2 才能访问它。 例如 `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`，其中，`STORAGE_ACCOUNT_NAME` 是群集所用存储帐户的名称，`STORAGE_CONTAINER_NAME` 是存储容器的名称。

    1. 使用交互式查询群集的元存储 URI 值设置 `spark.datasource.hive.warehouse.metastoreUri`。 若要查找 LLAP 群集的 metastoreUri，请在 LLAP 群集的 Ambari UI 中的“Hive” > “高级” > “常规”下查找“hive.metastore.uris”属性。 该值如以下 URI 所示：

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. 将 `spark.security.credentials.hiveserver2.enabled` 设置为 YARN 客户端部署模式的 `false`。
    1. 将 `spark.hadoop.hive.zookeeper.quorum` 设置为 LLAP 群集的 Zookeeper 仲裁。 若要查找 LLAP 群集的 Zookeeper 仲裁，请在 LLAP 群集的 Ambari UI 中的“Hive” > “高级” > “高级 hive-site”下查找“hive.zookeeper.quorum”属性。 该值如以下字符串所示：

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

若要测试 Hive 仓库连接器的配置，请遵循[连接和运行查询](#connecting-and-running-queries)部分中的步骤。

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

1. 通过 SSH 连接到群集的头节点。 有关使用 SSH 连接到群集的详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。
1. 键入 `cd /usr/hdp/current/hive_warehouse_connector` 切换到正确的目录，或者提供在 spark-shell 命令中用作参数的所有 jar 文件的完整路径。
1. 输入以下命令启动 spark shell：

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. 此时会出现一条欢迎消息，以及一条可以输入命令的 `scala>` 提示。

1. 启动 spark-shell 后，可以使用以下命令启动 Hive 仓库连接器实例：

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>在企业安全性套餐 (ESP) 群集上连接和运行查询

企业安全性套餐 (ESP) 为 Azure HDInsight 中的 Apache Hadoop 群集提供企业级功能，例如，基于 Active Directory 的身份验证、多用户支持和基于角色的访问控制。 有关 ESP 的详细信息，请参阅[在 HDInsight 中使用企业安全性套餐](../domain-joined/apache-domain-joined-architecture.md)。

1. 执行[连接和运行查询](#connecting-and-running-queries)中的初始步骤 1 和 2。
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

2. 筛选列 `state` 等于 `Colorado` 的表 `hivesampletable`。 此 Hive 表查询将作为 Spark 数据帧返回。 然后使用 `write` 函数将数据帧保存在 Hive 表 `sampletable_colorado` 中。

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

以下屏幕截图显示了生成的表。

![hive 仓库连接器显示 hive 表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>结构化流写入

使用 Hive 仓库连接器可以通过 Spark 流式传输将数据写入 Hive 表。

遵循以下步骤创建一个 Hive 仓库连接器示例，用于在 localhost 端口 9999 上将 Spark 流中的数据引入 Hive 表。

1. 在 Spark 群集上打开一个终端。
1. 开始使用以下命令执行 Spark 流式传输：

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. 执行以下步骤，为创建的 Spark 流生成数据：
    1. 在同一个 Spark 群集上打开另一个终端。
    1. 在命令提示符处，键入：`nc -lk 9999`。 此命令使用 netcat 实用工具通过命令行将数据发送到指定的端口。
    1. 键入希望 Spark 流引入的单词，后接回车符。

        ![向 Apache spark 流输入数据](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)

1. 创建新的 Hive 表用于保存流数据。 在 spark-shell 中键入以下命令：

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 使用以下命令将流数据写入新建的表：

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > 由于 Apache Spark 的已知问题，目前必须手动设置 `metastoreUri` 和 `database` 选项。 有关此问题的详细信息，请参阅 [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)。

1. 可以使用以下命令查看插入到表中的数据：

    ```scala
    hive.table("stream_table").show()
    ```

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

    a. 提供所需的策略名称。 从“选择掩码选项”菜单中选择数据库“默认”、Hive 表“演示”、Hive 列“名称”、用户“rsadmin2”、访问类型“选择”和“部分掩码: 显示最后 4 个”。 单击“添加”。
                ![创建策略](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 再次查看该表的内容。 应用 Ranger 策略之后，我们只能看到该列的最后四个字符。

    ![应用 Ranger 策略之后的演示表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>后续步骤

* [将交互式查询与 HDInsight 配合使用](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [有关使用 Zeppelin、Livy、spark-submit 和 pyspark 集成 Hive 仓库连接器的示例](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)

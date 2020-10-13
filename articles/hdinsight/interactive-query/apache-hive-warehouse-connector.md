---
title: Apache Spark & Hive - Hive 仓库连接器 - Azure HDInsight
description: 了解如何在 Azure HDInsight 上将 Apache Spark 和 Apache Hive 与 Hive Warehouse Connector 集成。
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 24968511d038b2cea41a59187c0a361684c6720e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86511885"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>在 Azure HDInsight 中将 Apache Spark 和 Apache Hive 与 Hive Warehouse Connector 集成

Apache Hive Warehouse Connector (HWC) 是一个库，可让你更轻松地使用 Apache Spark 和 Apache Hive。 它支持在 Spark DataFrames 和 Hive 表之间移动数据等任务。 此外，通过将 Spark 流数据定向到 Hive 表中。 Hive Warehouse Connector 的工作方式类似于 Spark 与 Hive 之间的桥梁。 它还支持将 Scala、Java 和 Python 作为开发编程语言。

通过 Hive Warehouse Connector 可利用 Hive 和 Spark 的独特功能构建功能强大的大数据应用程序。

Apache Hive 为原子性、一致性、隔离性和持久性 (ACID) 数据库事务提供支持。 有关 Hive 中的 ACID 和事务的详细信息，请参阅 [Hive 事务](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)。 Hive 还通过 Apache Ranger 以及在 Apache Spark 中不可用的低延迟分析处理 (LLAP) 提供详细安全控制。

Apache Spark 具有一个结构化流 API，可提供 Apache Hive 中不可用的流式处理功能。 从 HDInsight 4.0 开始，Apache Spark 2.3.1 和 Apache Hive 3.1.0 具有单独的元存储。 单独的元存储可能会使互操作性难以实现。 通过 Hive Warehouse Connector 可更轻松地将 Spark 和 Hive 一起使用。 HWC 库将数据从 LLAP 守护程序并行加载到 Spark 执行程序。 与从 Spark 到 Hive 的标准 JDBC 连接相比，此过程可更高效且更具适应性。

![hive warehouse connector 体系结构](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive Warehouse Connector 支持的部分操作包括：

* 描述表
* 为 ORC 格式的数据创建表
* 选择 Hive 数据和检索数据帧
* 将数据帧批量写入到 Hive
* 执行 Hive 更新语句
* 从 Hive 读取表数据、在 Spark 中转换数据，然后将数据写入到新的 Hive 表
* 使用 HiveStreaming 将数据帧或 Spark 流写入到 Hive

## <a name="hive-warehouse-connector-setup"></a>Hive Warehouse Connector 设置

> [!IMPORTANT]
> 不支持将 Spark 2.4 企业安全性套餐群集上安装的 HiveServer2 Interactive 实例与 Hive Warehouse Connector 一起使用。 相反，必须配置一个独立的 HiveServer2 Interactive 群集来承载 HiveServer2 Interactive 工作负载。 不支持使用单一 Spark 2.4 群集的 Hive Warehouse Connector 配置。

Hive Warehouse Connector 对于 Spark 和 Interactive Query 工作负责需要单独的群集。 按照以下步骤在 Azure HDInsight 中设置这些群集。

### <a name="create-clusters"></a>创建群集

1. 使用存储帐户和自定义的 Azure 虚拟网络创建 HDInsight Spark **4.0** 群集。 有关在 Azure 虚拟网络中创建群集的信息，请参阅[将 HDInsight 添加到现有虚拟网络](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)。

1. 使用与 Spark 群集相同的存储帐户和 Azure 虚拟网络创建 HDInsight Interactive Query (LLAP) 4.0 群集。

### <a name="configure-hwc-settings"></a>配置 HWC 设置

#### <a name="gather-preliminary-information"></a>收集初步信息

1. 在 Web 浏览器中，导航到 `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE`，其中 LLAPCLUSTERNAME 是 Interactive Query 群集的名称。

1. 导航到“摘要” > “HiveServer2 交互式 JDBC URL”并记下该值。 该值可能类似于：`jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive`。

1. 导航到“配置” > “高级” > “高级 hive-site” > “hive.zookeeper.quorum”，并记下该值。 该值可能类似于：`zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`。

1. 导航到“配置” > “高级” > “常规” > “hive.metastore.uris”，并记下该值。 该值可能类似于：`thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`。

1. 导航到“配置” > “高级” > “高级 hive-interactive-site” > “hive.llap.daemon.service.hosts”，并记下该值。 该值可能类似于：`@llap0`。

#### <a name="configure-spark-cluster-settings"></a>配置 Spark 群集设置

1. 在 Web 浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`，其中 CLUSTERNAME 是 Apache Spark 群集的名称。

1. 展开“自定义 spark2-defaults”。

    ![Apache Ambari Spark2 配置](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

1. 选择“添加属性...”，以添加以下配置：

    | 配置 | 值 |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> 设置为合适的 HDFS 兼容分段目录。 如果有两个不同的群集，则分段目录应该是 LLAP 群集存储帐户的分段目录中的文件夹，以便 HiveServer2 有权访问它。  将 `STORAGE_ACCOUNT_NAME` 替换为群集使用的存储帐户的名称，并将 `STORAGE_CONTAINER_NAME` 替换为存储容器的名称。 |
    |`spark.sql.hive.hiveserver2.jdbc.url`| 之前从“HiveServer2 交互式 JDBC URL”获取的值 |
    |`spark.datasource.hive.warehouse.metastoreUri`| 之前从“hive.metastore.uris”获取的值。 |
    |`spark.security.credentials.hiveserver2.enabled`|对于 YARN 群集模式为 `true`，对于 YARN 客户端模式为 `false`。 |
    |`spark.hadoop.hive.zookeeper.quorum`| 之前从“hive.zookeeper.quorum”获取的值。 |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| 之前从“hive.llap.daemon.service.hosts”获取的值。 |

1. 保存更改并重启所有受影响的组件。

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>为企业安全性套餐 (ESP) 群集配置 HWC

企业安全性套餐 (ESP) 为 Azure HDInsight 中的 Apache Hadoop 群集提供企业级功能，如基于 Active Directory 的身份验证、多用户支持以及基于角色的访问控制。 有关 ESP 的详细信息，请参阅[在 HDInsight 中使用企业安全性套餐](../domain-joined/apache-domain-joined-architecture.md)。

除了上一部分中提到的配置之外，请添加以下配置以在 ESP 群集上使用 HWC。

1. 从 Spark 群集的 Ambari Web UI 导航到“Spark2” > “配置” > “自定义 spark2-defaults”。

1. 更新以下属性。

    | 配置 | 值 |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * 在 Web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`，其中 CLUSTERNAME 是 Interactive Query 群集的名称。 单击 HiveServer2 Interactive。 将看到运行 LLAP 的头节点的完全限定的域名 (FQDN)，如屏幕截图中所示。 将 `<llap-headnode>` 替换为此值。

        ![Hive Warehouse Connector 头节点](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到 Interactive Query 群集。 在 `/etc/krb5.conf` 文件中查找 `default_realm` 参数。 以大写字符串的形式使用此值替换 `<AAD-DOMAIN>`，否则会找不到凭据。

        ![Hive Warehouse Connector AAD 域](./media/apache-hive-warehouse-connector/aad-domain.png)

    * 例如：`hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET`。
    
1. 保存更改并根据需要重启组件。

## <a name="hive-warehouse-connector-usage"></a>Hive Warehouse Connector 用法

可以在几种不同方法之间进行选择，以使用 Hive Warehouse Connector 连接到 Interactive Query 群集并执行查询。 支持的方法包括以下工具：

* [Spark-shell/PySpark](../spark/apache-spark-shell.md)
* [Spark-submit](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


下面是一些从 Spark 连接到 HWC 的示例。

### <a name="spark-shell"></a>Spark-shell

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
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. 启动 spark shell 后，可以使用以下命令启动配置 Hive Warehouse Connector 实例：

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark-submit

将 scala/java 代码连同依赖项一起生成到程序集 jar 后，使用以下命令启动 Spark 应用程序。 将 `<VERSION>` 和 `<APP_JAR_PATH>` 替换为实际值。

* YARN 客户端模式
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* YARN 群集模式
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

对于 Python，还应添加以下配置。 

```python
--py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>对企业安全性套餐 (ESP) 群集运行查询

在启动 spark-shell 或 spark-submit 之前使用 `kinit`。 将 USERNAME 替换为有权访问群集的域帐户的名称，然后执行以下命令：

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>保护 Spark ESP 群集上的数据

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
    1. 转到 Ranger 管理 UI (`https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/`)。
    1. 在“Hive”下单击用于群集的 Hive 服务。
        ![ranger 服务管理器](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. 单击“掩码”选项卡，然后单击“添加新策略” 

        ![hive warehouse connector ranger hive 策略列表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    1. 提供所需的策略名称。 从“选择掩码选项”菜单中选择数据库“默认”、Hive 表“演示”、Hive 列“名称”、用户“rsadmin2”、访问类型“选择”和“部分掩码: 显示最后 4 个”。       单击“添加”。
                ![创建策略](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 再次查看表的内容。 应用 Ranger 策略之后，我们只能看到该列的最后四个字符。

    ![应用 Ranger 策略之后的演示表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>后续步骤

* [HWC 和 Apache Spark 操作](./apache-hive-warehouse-connector-operations.md)
* [将交互式查询与 HDInsight 配合使用](./apache-interactive-query-get-started.md)
* [将 HWC 与 Apache Zeppelin 集成](./apache-hive-warehouse-connector-zeppelin.md)
* [使用 Zeppelin、Livy、spark-submit 和 pyspark 与 Hive Warehouse Connector 交互的示例](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)

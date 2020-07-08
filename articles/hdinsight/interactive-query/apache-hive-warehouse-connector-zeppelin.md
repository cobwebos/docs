---
title: Hive Warehouse Connector - 使用 Livy 的 Apache Zeppelin - Azure HDInsight
description: 了解如何在 Azure HDInsight 上将 Hive Warehouse Connector 与 Apache Zeppelin 集成。
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 6ee1c70ec02af2a24f7867a6e6b06593361612b2
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86083111"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>在 Azure HDInsight 中将 Apache Zeppelin 与 Hive Warehouse Connector 集成

HDInsight Spark 群集包含具有不同解释器的 Apache Zeppelin 笔记本。 在本文中，我们将只关注 Livy 解释器如何使用 Hive Warehouse Connector 从 Spark 访问 Hive 表。

## <a name="prerequisite"></a>先决条件

完成 [Hive Warehouse Connector 设置](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)步骤。

## <a name="getting-started"></a>入门

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到 Apache Spark 群集。 编辑以下命令，将 CLUSTERNAME 替换为群集的名称，然后输入该命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 在 ssh 会话中，执行以下命令以记下 `hive-warehouse-connector-assembly` 和 `pyspark_hwc` 版本：

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    保存输出，以便稍后配置 Apache Zeppelin 时使用。

## <a name="configure-livy"></a>配置 Livy

需要以下配置才能通过 Livy 解释器访问 Zeppelin 中的 hive 表。

### <a name="interactive-query-cluster"></a>交互式查询群集

1. 在 Web 浏览器中，导航到 `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs`，其中 LLAPCLUSTERNAME 是 Interactive Query 群集的名称。

1. 导航到“高级” > “自定义核心站点”。 选择“添加属性...”，以添加以下配置：

    | 配置                 | 值 |
    | ----------------------------- |-------|
    | hadoop.proxyuser.livy.groups  | *     |
    | hadoop.proxyuser.livy.hosts   | *     |

1. 保存更改并重启所有受影响的组件。

### <a name="spark-cluster"></a>Spark Cluster

1. 在 Web 浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`，其中 CLUSTERNAME 是 Apache Spark 群集的名称。

1. 展开“自定义 livy2-conf”。 选择“添加属性...”，以添加以下配置：

    | 配置                 | 值                                      |
    | ----------------------------- |------------------------------------------  |
    | livy.file.local-dir-whitelist | /usr/hdp/current/hive_warehouse_connector/ |

1. 保存更改并重启所有受影响的组件。

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>在 Zeppelin UI（Spark 群集）中配置 Livy 解释器

1. 在 Web 浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter`，其中 `CLUSTERNAME` 是 Apache Spark 群集的名称。

1. 导航到“livy2”。

1. 添加以下配置：

    | 配置                 | 值                                      |
    | ----------------------------- |:------------------------------------------:|
    | livy.spark.hadoop.hive.llap.daemon.service.hosts | @llap0 |
    | livy.spark.security.credentials.hiveserver2.enabled | true |
    | livy.spark.sql.hive.llap | true |
    | livy.spark.yarn.security.credentials.hiveserver2.enabled | true |
    | livy.superusers | livy,zeppelin |
    | livy.spark.jars | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar` 列中的一个值匹配。<br>将 VERSION 替换为之前在[入门](#getting-started)中获取的值。 |
    | livy.spark.submit.pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip` 列中的一个值匹配。<br>将 VERSION 替换为之前在[入门](#getting-started)中获取的值。 |
    | livy.spark.sql.hive.hiveserver2.jdbc.url | 将其设置为 Interactive Query 群集的 HiveServer2 Interactive JDBC URL。 |
    | spark.security.credentials.hiveserver2.enabled | true |

1. 仅针对 ESP 群集添加以下配置：

    | 配置| 值|
    |---|---|
    | livy.spark.sql.hive.hiveserver2.jdbc.url.principal | `hive/<llap-headnode>@<AAD-Domain>` |

    * 在 web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` 其中 CLUSTERNAME 是您的交互式查询群集的名称。 单击 " **HiveServer2 Interactive**"。 你将看到运行 LLAP 的头节点的完全限定的域名（FQDN），如屏幕截图中所示。 替换 `<llap-headnode>` 为此值。

        ![hive 仓库连接器头节点](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到交互式查询群集。 `default_realm`在文件中查找参数 `/etc/krb5.conf` 。 替换 `<AAD-DOMAIN>` 为大写字符串形式的此值，否则将找不到凭据。

        ![hive 仓库连接器 AAD 域](./media/apache-hive-warehouse-connector/aad-domain.png)

    * 例如， `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` 。

1. 保存更改并重启 Livy 解释器。

如果 Livy 解释器不可访问，请修改 Ambari 中 Zeppelin 组件的 `shiro.ini` 文件。 有关详细信息，请参阅[配置 Apache Zeppelin Security](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html)。  


## <a name="running-queries-in-zeppelin"></a>在 Zeppelin 中运行查询 

使用 Livy 解释器启动 Zeppelin 笔记本并执行以下操作

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>后续步骤

* [HWC 和 Apache Spark 操作](./apache-hive-warehouse-connector-operations.md)
* [将 HWC 与 Apache Spark 和 Apache Hive 集成](./apache-hive-warehouse-connector.md)
* [将交互式查询与 HDInsight 配合使用](./apache-interactive-query-get-started.md)

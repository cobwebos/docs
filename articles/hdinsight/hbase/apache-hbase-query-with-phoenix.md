---
title: 快速入门：Apache HBase 和 Apache Phoenix - Azure HDInsight
description: 本快速入门介绍如何在 HDInsight 中使用 Apache Phoenix。 此外，了解如何在计算机上安装和设置 SQLLine 以连接到 HDInsight 中的 HBase 群集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 1c400e41c4c10023d2595bde8c0d62e26184cf05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79370315"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>快速入门：使用 Apache Phoenix 在 Azure HDInsight 中查询 Apache HBase

在本快速入门中，你将学习如何使用 Apache Phoeni 在 Azure HDInsight 中运行 HBase 查询。 Apache Phoenix 是 Apache HBase 的 SQL 查询引擎。 该引擎以 JDBC 驱动程序的形式供用户访问，并且支持使用 SQL 来查询和管理 HBase 表。 [SQLLine](http://sqlline.sourceforge.net/) 是用于执行 SQL 的命令行实用工具。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* Apache HBase 群集。 若要创建 HDInsight 群集，请参阅[创建群集](../hadoop/apache-hadoop-linux-tutorial-get-started.md)。  确保选择 **HBase** 群集类型。

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="identify-a-zookeeper-node"></a>识别 ZooKeeper 节点

在连接到 HBase 群集时，需要连接到 Apache ZooKeeper 节点之一。 每个 HDInsight 群集都有三个 ZooKeeper 节点。 可以使用 Curl 来快速识别 ZooKeeper 节点。 编辑以下 curl 命令，将 `PASSWORD` 和 `CLUSTERNAME` 替换为相关的值，然后在命令提示符下输入该命令：

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

输出的一部分将类似于以下内容：

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

记下 `host_name` 的值供以后使用。

## <a name="create-a-table-and-manipulate-data"></a>创建表并操作数据

可以使用 SSH 连接到 HBase 群集，然后使用 Apache Phoenix 来创建 HBase 表以及插入和查询数据。

1. 使用 `ssh` 命令连接到 HBase 群集。 编辑以下命令，将 `CLUSTERNAME` 替换为群集的名称，然后输入该命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 将目录更改到 Phoenix 客户端。 输入以下命令：

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. 启动 [SQLLine](http://sqlline.sourceforge.net/)。 编辑以下命令，将 `ZOOKEEPER` 替换为群集的名称，然后输入该命令：

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. 创建一个 HBase 表。 输入以下命令：

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. 使用 SQLLine `!tables` 命令列出 HBase 中的所有表。 输入以下命令：

    ```sqlline
    !tables
    ```

6. 在表中插入值。 输入以下命令：

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. 查询表。 输入以下命令：

    ```sql
    SELECT * FROM Company;
    ```

8. 删除记录。 输入以下命令：

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. 删除表。 输入以下命令：

    ```hbase
    DROP TABLE Company;
    ```

10. 使用 SQLLine `!quit` 命令退出 SQLLine。 输入以下命令：

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

若要删除群集，请参阅[使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已学习了如何使用 Apache Phoenix 在 Azure HDInsight 中运行 HBase 查询。 若要详细了解 Apache Phoenix，下一篇文章将提供更深层次的介绍。

> [!div class="nextstepaction"]
> [HDInsight 中的 Apache Phoenix](../hdinsight-phoenix-in-hdinsight.md)

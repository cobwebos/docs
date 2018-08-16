---
title: HDInsight 上的 Hadoop 服务所使用的端口 - Azure
description: HDInsight 上运行的 Hadoop 服务使用的端口列表。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jasonh
ms.openlocfilehash: 78aed9d74325dfae0a02a4b95082baf60f6af603
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599242"
---
# <a name="ports-used-by-hadoop-services-on-hdinsight"></a>HDInsight 上的 Hadoop 服务所使用的端口

本文档提供在基于 Linux 的 HDInsight 群集上运行的 Hadoop 服务使用的端口列表。 此外，还提供了用于通过 SSH 连接到群集的端口的信息。

## <a name="public-ports-vs-non-public-ports"></a>公共端口与非公共端口

基于 Linux 的 HDInsight 群集只在 Internet 上公开三个端口：22、23 和 443。 使用这些端口可以通过 SSH 安全访问群集，以及访问通过安全 HTTPS 协议公开的服务。

在内部，HDInsight 由在 Azure 虚拟网络上运行的多个 Azure 虚拟机（群集内的节点）实现。 从虚拟网络内部可以访问不是通过 Internet 公开的端口。 例如，如果使用 SSH 连接到某个头节点，则可以从该头节点直接访问群集节点上运行的服务。

> [!IMPORTANT]
> 如果尚未指定某个 Azure 虚拟网络作为 HDInsight 的配置选项，系统会自动创建一个 Azure 虚拟网络。 但无法将其他计算机（例如其他 Azure 虚拟机或客户端开发计算机）加入到此虚拟网络中。


要将其他计算机添加到虚拟网络，必须先创建虚拟网络，然后在创建 HDInsight 群集时指定该网络。 有关详细信息，请参阅[使用 Azure 虚拟网络扩展 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>公共端口

HDInsight 群集中的所有节点都在 Azure 虚拟网络中，无法直接从 Internet 访问。 使用公共网关可以通过 Internet 访问以下端口（在所有 HDInsight 群集类型中很常见）。

| 服务 | 端口 | 协议 | Description |
| --- | --- | --- | --- | --- |
| sshd |22 |SSH |将客户端连接到主头节点上的 sshd。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。 |
| sshd |22 |SSH |将客户端连接到边缘节点上的 sshd。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。 |
| sshd |23 |SSH |将客户端连接到辅助头节点上的 sshd。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。 |
| Ambari |443 |HTTPS |Ambari Web UI。 请参阅[使用 Ambari Web UI 管理 HDInsight](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API。 请参阅[使用 Ambari REST API 管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API。 请参阅[将 Hive 与 Curl 配合使用](hadoop/apache-hadoop-use-pig-curl.md)、[将 Pig 与 Curl 配合使用](hadoop/apache-hadoop-use-pig-curl.md)、[将 MapReduce 与 Curl 配合使用](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |使用 ODBC 连接到 Hive。 请参阅[使用 Microsoft ODBC 驱动程序将 Excel 连接到 HDInsight](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。 |
| HiveServer2 |443 |JDBC |使用 JDBC 连接到 Hive。 请参阅[使用 Hive JDBC 驱动程序连接到 HDInsight 上的 Hive](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

以下各项适用于特定的群集类型：

| 服务 | 端口 | 协议 | 群集类型 | Description |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST API。 请参阅[开始使用 HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API。 请参阅[使用 Livy 远程提交 Spark 作业](spark/apache-spark-livy-rest-interface.md) |
| Spark Thrift 服务器 |443 |HTTPS |Spark |用来提交 Hive 查询的 Spark Thrift 服务器。 请参阅[在 HDInsight 上将 Beeline 与 Hive 配合使用](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Storm Web UI。 请参阅[在 HDInsight 上部署和管理 Storm 拓扑](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>身份验证

在 Internet 上公开的所有服务都必须经过身份验证：

| 端口 | 凭据 |
| --- | --- |
| 22 或 23 |在创建群集期间指定的 SSH 用户凭据 |
| 443 |在创建群集期间设置的登录名（默认为 admin）和密码 |

## <a name="non-public-ports"></a>非公共端口

> [!NOTE]
> 某些服务仅适用于特定的群集类型。 例如，HBase 仅适用于 HBase 群集类型。

> [!IMPORTANT]
> 某些服务仅在一个头节点上运行一次。 如果尝试连接到主头节点上的服务并收到错误，请重试使用辅助头节点。

### <a name="ambari"></a>Ambari

| 服务 | Nodes | 端口 | URL 路径 | 协议 | 
| --- | --- | --- | --- | --- |
| Ambari Web UI | 头节点 | 8080 | / | HTTP |
| Ambari REST API | 头节点 | 8080 | /api/v1 | HTTP |

示例：

* Ambari REST API：`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS 端口

| 服务 | Nodes | 端口 | 协议 | Description |
| --- | --- | --- | --- | --- |
| NameNode Web UI |头节点 |30070 |HTTPS |用于查看状态的 Web UI |
| NameNode 元数据服务 |头节点 |8020 |IPC |文件系统元数据 |
| DataNode |所有辅助角色节点 |30075 |HTTPS |用于查看状态、日志等的 Web UI |
| DataNode |所有辅助角色节点 |30010 |&nbsp; |数据传输 |
| DataNode |所有辅助角色节点 |30020 |IPC |元数据操作 |
| 辅助 NameNode |头节点 |50090 |HTTP |NameNode 元数据检查点 |

### <a name="yarn-ports"></a>YARN 端口

| 服务 | Nodes | 端口 | 协议 | Description |
| --- | --- | --- | --- | --- |
| Resource Manager Web UI |头节点 |8088 |HTTP |Resource Manager 的 Web UI |
| Resource Manager Web UI |头节点 |8090 |HTTPS |Resource Manager 的 Web UI |
| Resource Manager 管理界面 |头节点 |8141 |IPC |用于应用程序提交（Hive、Hive 服务器、Pig 等） |
| Resource Manager 计划程序 |头节点 |8030 |HTTP |管理界面 |
| Resource Manager 应用程序界面 |头节点 |8050 |HTTP |应用程序管理器界面的地址 |
| NodeManager |所有辅助角色节点 |30050 |&nbsp; |容器管理器的地址 |
| NodeManager Web UI |所有辅助角色节点 |30060 |HTTP |Resource Manager 界面 |
| Timeline 地址 |头节点 |10200 |RPC |Timeline 服务 RPC 服务。 |
| Timeline Web UI |头节点 |8181 |HTTP |Timeline 服务 Web UI |

### <a name="hive-ports"></a>Hive 端口

| 服务 | Nodes | 端口 | 协议 | Description |
| --- | --- | --- | --- | --- |
| HiveServer2 |头节点 |10001 |Thrift |用于连接到 Hive 的服务 (Thrift/JDBC) |
| Hive 元存储 |头节点 |9083 |Thrift |用于连接到 Hive 元数据的服务 (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat 端口

| 服务 | Nodes | 端口 | 协议 | Description |
| --- | --- | --- | --- | --- |
| WebHCat 服务器 |头节点 |30111 |HTTP |位于 HCatalog 和其他 Hadoop 服务顶层的 Web API |

### <a name="mapreduce-ports"></a>MapReduce 端口

| 服务 | Nodes | 端口 | 协议 | Description |
| --- | --- | --- | --- | --- |
| JobHistory |头节点 |19888 |HTTP |MapReduce JobHistory Web UI |
| JobHistory |头节点 |10020 |&nbsp; |MapReduce JobHistory 服务器 |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |将中间映射输出传输到请求化简器 |

### <a name="oozie"></a>Oozie

| 服务 | Nodes | 端口 | 协议 | Description |
| --- | --- | --- | --- | --- |
| Oozie 服务器 |头节点 |11000 |HTTP |Oozie 服务的 URL |
| Oozie 服务器 |头节点 |11001 |HTTP |Oozie 管理端口 |

### <a name="ambari-metrics"></a>Ambari 指标

| 服务 | Nodes | 端口 | 协议 | Description |
| --- | --- | --- | --- | --- |
| TimeLine（应用程序历史记录） |头节点 |6188 |HTTP |TimeLine 服务 Web UI |
| TimeLine（应用程序历史记录） |头节点 |30200 |RPC |TimeLine 服务 Web UI |

### <a name="hbase-ports"></a>HBase 端口

| 服务 | Nodes | 端口 | 协议 | Description |
| --- | --- | --- | --- | --- |
| HMaster |头节点 |16000 |&nbsp; |&nbsp; |
| HMaster 信息 Web UI |头节点 |16010 |HTTP |HBase 主控 Web UI 的端口 |
| 区域服务器 |所有辅助角色节点 |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |客户端用来连接 ZooKeeper 的端口 |

### <a name="kafka-ports"></a>Kafka 端口

| 服务 | Nodes | 端口 | 协议 | Description |
| --- | --- | --- | --- | --- |
| 代理 |辅助角色节点 |9092 |[Kafka Wire Protocol](http://kafka.apache.org/protocol.html)（Kafka 线路协议） |用于客户端通信 |
| &nbsp; |Zookeeper 节点 |2181 |&nbsp; |客户端用来连接 Zookeeper 的端口 |

### <a name="spark-ports"></a>Spark 端口

| 服务 | Nodes | 端口 | 协议 | URL 路径 | Description |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift 服务器 |头节点 |10002 |Thrift | &nbsp; | 用于连接到 Spark SQL 的服务 (Thrift/JDBC) |
| Livy 服务器 | 头节点 | 8998 | HTTP | &nbsp; | 用于运行语句、作业和应用程序的服务 |
| Jupyter 笔记本 | 头节点 | 8001 | HTTP | &nbsp; | Jupyter notebook 网站 |

示例：

* Livy：`curl -u admin -G "http://10.0.0.11:8998/"`。 在此示例中，`10.0.0.11` 是托管 Livy 服务的头节点的 IP 地址。

---
title: Hadoop 的高可用性 - Azure HDInsight
description: 了解 HDInsight 群集如何使用附加的头节点提高可靠性和可用性。 了解这种方式对 Ambari 和 Hive 等 Hadoop 服务造成怎样的影响，以及如何使用 SSH 分别连接到每个头节点。
ms.reviewer: jasonh
author: hrasheed-msft
keywords: hadoop 高可用性
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: hrasheed
ms.openlocfilehash: 1828efb410849677e859d341e4e16e4f5d4ca681
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405991"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>HDInsight 中的 Apache Hadoop 群集的可用性和可靠性

HDInsight 群集提供了两个头节点，以提升 Apache Hadoop 服务和作业运行的可用性与可靠性。

Hadoop 通过将服务和数据复制到群集的多个节点中来实现高可用性和可靠性。 不过，Hadoop 的标准分发功能通常只能有一个头节点。 单个头节点发生任何中断都可能导致群集停止工作。 HDInsight 提供了两个头节点来提高 Hadoop 的可靠性和可用性。

## <a name="availability-and-reliability-of-nodes"></a>节点的可用性和可靠性

HDInsight 群集中的节点是使用 Azure 虚拟机实现的。 以下部分介绍可配合 HDInsight 使用的节点类型。 

> [!NOTE]  
> 并非所有节点类型都可用于某个群集类型。 例如，Hadoop 群集类型就没有任何 Nimbus 节点。 有关 HDInsight 群集类型使用的节点详细信息，请参阅[在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)文档的“群集类型”部分。

### <a name="head-nodes"></a>头节点

为确保 Hadoop 服务的高可用性，HDInsight 提供了两个头节点。 这两个头节点同时处于活动状态并在 HDInsight 群集中运行。 某些服务，例如 Apache HDFS 或 Apache Hadoop YARN，在任何给定的时间仅能在其中一个头节点上处于“活动”状态。 HiveServer2 或 Hive MetaStore 等其他服务同时在这两个头节点上处于活动状态。

头节点（以及 HDInsight 中的其他节点）的主机名中包含一个数字值。 例如 `hn0-CLUSTERNAME` 或 `hn4-CLUSTERNAME`。

> [!IMPORTANT]  
> 请勿将数字值与某个节点是主节点还是辅助节点相关联。 使用数字值是为了为每个节点提供唯一名称。

### <a name="nimbus-nodes"></a>Nimbus 节点

Apache Storm 群集提供了 Nimbus 节点。 Nimbus 节点通过在辅助角色节点之间分发和监视处理资源来提供类似于 Hadoop JobTracker 的功能。 HDInsight 为 Storm 群集提供了两个 Nimbus 节点

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper 节点

[ZooKeeper](https://zookeeper.apache.org/) 节点用于为头节点上的主服务进行领导选拨。 它们还用来确保服务、数据（辅助角色）节点和网关知道主服务在哪个头节点上处于活动状态。 默认情况下，HDInsight 提供三个 ZooKeeper 节点。

### <a name="worker-nodes"></a>辅助角色节点

将作业提交到群集时，辅助角色节点执行实际的数据分析。 如果辅助角色节点发生故障，它执行的任务将提交到另一个辅助角色节点。 默认情况下，HDInsight 创建四个辅助角色节点。 可以在群集创建过程中以及之后根据需要更改此数字。

### <a name="edge-node"></a>边缘节点

边缘节点不主动参与群集内的数据分析。 在使用 Hadoop 时，它由开发人员或数据科学家使用。 边缘节点与群集中的其他节点一样驻留在同一个 Azure 虚拟网络中，可直接访问其他所有节点。 可以在不将资源带离关键的 Hadoop 服务或分析作业的情况下使用边缘节点。

目前，HDInsight 上的 ML Services 是默认提供边缘节点的唯一群集类型。 对于 HDInsight 上的 ML Services 而言，边缘节点用于在将 R 代码提交到群集进行分布式处理之前，在本地节点上对这些代码进行测试。

若要了解如何将边缘节点与其他群集类型配合使用，请参阅[在 HDInsight 中使用边缘节点](hdinsight-apps-use-edge-node.md)文档。

## <a name="accessing-the-nodes"></a>访问节点

可以通过公共网关经 Internet 访问群集。 访问仅限连接到头节点和边缘节点（如果存在）。 访问头节点上运行的服务不会受存在多个头节点的影响。 公共网关将请求路由到托管所请求服务的头节点。 例如，如果 Apache Ambari 当前托管在辅助头节点上，网关会将 Ambari 收到的请求路由到该节点。

通过公共网关进行访问仅限端口 443 (HTTPS)、22 和 23。

* 端口 __443__ 用于访问托管在头节点上的 Ambari 和其他 Web UI 或 REST API。

* 端口 __22__ 用于通过 SSH 访问主头节点或边缘节点。

* 端口 __23__ 用于通过 SSH 访问辅助头节点。 例如，`ssh username@mycluster-ssh.azurehdinsight.net` 连接到群集名为 **mycluster** 的主头节点。

有关如何使用 SSH 的详细信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)文档。

### <a name="internal-fully-qualified-domain-names-fqdn"></a>内部完全限定的域名 (FQDN)

HDInsight 群集中的节点具有内部 IP 地址和 FQDN，这些只能从群集访问。 使用内部 FQDN 或 IP 地址访问群集上的服务时，应该使用 Ambari 来验证访问服务时使用的 IP 或 FQDN。

例如，Apache Oozie 服务只能在一个头节点上运行，而且从 SSH 会话使用 `oozie` 命令需要有该服务的 URL。 可以通过 Ambari 使用以下命令来检索该 URL：

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

该命令将返回类似以下命令的值，其中包含要在 `oozie` 命令中使用的内部 URL：

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

若要详细了解如何使用 Ambari REST API，请参阅[使用 Apache Ambari REST API 监视和管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md)。

### <a name="accessing-other-node-types"></a>访问其他节点类型

可以使用以下方法连接到无法直接通过 Internet 访问的节点：

* **SSH**：使用 SSH 连接到头节点后，可以从头节点使用 SSH 连接到群集中的其他节点。 有关详细信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)文档。

* **SSH 隧道**：如果需要访问托管在某个节点上的 Web 服务，并且该服务不在 Internet 上公开，则必须使用 SSH 隧道。 有关详细信息，请参阅[将 SSH 隧道与 HDInsight 配合使用](hdinsight-linux-ambari-ssh-tunnel.md)文档。

* **Azure 虚拟网络**：如果 HDInsight 群集是 Azure 虚拟网络的一部分，则同一虚拟网络中的任何资源都可以直接访问该群集中的所有节点。 有关详细信息, 请参阅为[HDInsight 规划虚拟网络](hdinsight-plan-virtual-network-deployment.md)文档。

## <a name="how-to-check-on-a-service-status"></a>如何检查服务状态

若要检查头节点中运行的服务的状态，请使用 Ambari Web UI 或 Ambari REST API。

### <a name="ambari-web-ui"></a>Ambari Web UI

可在 `https://CLUSTERNAME.azurehdinsight.net` 处查看 Ambari Web UI。 将 **CLUSTERNAME** 替换为群集名称。 如果出现提示，请输入群集的 HTTP 用户凭据。 默认 HTTP 用户名为 **admin**，密码是创建群集时输入的密码。

出现 Ambari 页面时，该页的左侧将列出已安装的服务。

![已安装的服务](./media/hdinsight-high-availability-linux/services.png)

服务旁边可能会出现一系列表示状态的图标。 可以使用页面顶部的“警报”链接查看与服务相关的任何警报。  Ambari 提供多个预定义的警报。

以下警报可以帮助监视群集的可用性：

| 警报名称                               | 描述                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 指标监视器状态                    | 此警报指示监视器状态脚本确定的指标监视器进程状态。                                                                                   |
| Ambari 代理检测信号                   | 如果服务器与代理之间的通信断开，则会触发此警报。                                                                                                                        |
| ZooKeeper 服务器进程                 | 如果无法确定 ZooKeeper 服务器进程是否正在运行并在网络上侦听，则会触发此主机级别的警报。                                                               |
| IOCache 元数据服务器状态           | 如果无法确定 IOCache 元数据服务器是否正在运行并在响应客户端请求，则会触发此主机级别的警报                                                            |
| JournalNode Web UI                       | 如果无法访问 JournalNode Web UI，则会触发此主机级别的警报。                                                                                                                 |
| Spark2 Thrift 服务器                     | 如果无法确定 Spark2 Thrift 服务器是否正在运行，则会触发此主机级别的警报。                                                                                                |
| 历史记录服务器进程                   | 如果无法确定历史记录服务器进程是否正在运行并在网络上侦听，则会触发此主机级别的警报。                                                                |
| 历史记录服务器 Web UI                    | 如果无法访问历史记录服务器 Web UI，则会触发此主机级别的警报。                                                                                                              |
| ResourceManager Web UI                   | 如果无法访问 ResourceManager Web UI，则会触发此主机级别的警报。                                                                                                             |
| NodeManager 运行状况摘要               | 如果存在不正常的 NodeManager，则会触发此服务级别的警报                                                                                                                    |
| App Timeline Web UI                      | 如果无法访问 App Timeline 服务器 Web UI，则会触发此主机级别的警报。                                                                                                         |
| DataNode 运行状况摘要                  | 如果存在不正常的 DataNode，则会触发此服务级别的警报                                                                                                                       |
| NameNode Web UI                          | 如果无法访问 NameNode Web UI，则会触发此主机级别的警报。                                                                                                                    |
| ZooKeeper 故障转移控制器进程    | 如果无法确认 ZooKeeper 故障转移控制器进程是否正在运行并在网络上侦听，则会触发此主机级别的警报。                                                   |
| Oozie 服务器 Web UI                      | 如果无法访问 Oozie 服务器 Web UI，则会触发此主机级别的警报。                                                                                                                |
| Oozie 服务器状态                      | 如果无法确定 Oozie 服务器是否正在运行并在响应客户端请求，则会触发此主机级别的警报。                                                                      |
| Hive 元存储进程                   | 如果无法确定 Hive 元存储进程是否正在运行并在网络上侦听，则会触发此主机级别的警报。                                                                 |
| HiveServer2 进程                      | 如果无法确定 HiveServer 是否正在运行并在响应客户端请求，则会触发此主机级别的警报。                                                                        |
| WebHCat 服务器状态                    | 如果 Templeton 服务器状态不正常，则会触发此主机级别的警报。                                                                                                            |
| 可用 ZooKeeper 服务器百分比      | 如果群集中已关闭的 ZooKeeper 服务器数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 ZooKeeper 进程检查的结果。     |
| Spark2 Livy 服务器                       | 如果无法确定 Livy2 服务器是否正在运行，则会触发此主机级别的警报。                                                                                                        |
| Spark2 历史记录服务器                    | 如果无法确定 Spark2 历史记录服务器是否正在运行，则会触发此主机级别的警报。                                                                                               |
| 指标收集器进程                | 如果在与阈值相等的秒数内无法确认指标收集器是否正在运行并在配置的端口上侦听，则会触发此警报。                                 |
| 指标收集器 - HBase Master 进程 | 如果在配置的“严重”阈值（以秒为单位）内无法确认指标收集器的 HBase Master 进程是否正在运行并在网络上侦听，则会触发此警报。 |
| 可用指标监视器百分比       | 如果在配置的“警告”和“严重”阈值内，有特定百分比的指标监视器进程未运行并在网络上侦听，则会触发此警报。                             |
| 可用 NodeManager 百分比           | 如果群集中已关闭的 NodeManager 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 NodeManager 进程检查的结果。        |
| NodeManager 运行状况                       | 此主机级别的警报检查 NodeManager 组件中提供的节点运行状况属性。                                                                                              |
| NodeManager Web UI                       | 如果无法访问 NodeManager Web UI，则会触发此主机级别的警报。                                                                                                                 |
| NameNode 高可用性运行状况        | 如果主动 NameNode 或待机 NameNode 未运行，则会触发此服务级别的警报。                                                                                     |
| DataNode 进程                         | 如果无法确定单个 DataNode 进程是否正在运行并在网络上侦听，则会触发此主机级别的警报。                                                         |
| DataNode Web UI                          | 如果无法访问 DataNode Web UI，则会触发此主机级别的警报。                                                                                                                    |
| 可用 JournalNode 百分比           | 如果群集中已关闭的 JournalNode 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 JournalNode 进程检查的结果。        |
| 可用 DataNode 百分比              | 如果群集中已关闭的 DataNode 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 DataNode 进程检查的结果。              |
| Zeppelin 服务器状态                   | 如果无法确定 Zeppelin 服务器是否正在运行并在响应客户端请求，则会触发此主机级别的警报。                                                                   |
| HiveServer2 交互进程          | 如果无法确定 HiveServerInteractive 是否正在运行并在响应客户端请求，则会触发此主机级别的警报。                                                             |
| LLAP 应用程序                         | 如果无法确定 LLAP 应用程序是否正在运行并在响应请求，则会触发此警报。                                                                                    |

可以选择每个服务来查看其详细信息。

尽管服务页提供了有关每个服务的状态和配置的信息，但并不提供有关该服务正在哪个头节点上运行的信息。 若要查看此信息，请使用页面顶部的“主机”链接。 此页会显示群集内的主机，包括头节点。

![主机列表](./media/hdinsight-high-availability-linux/hosts.png)

选择一个头节点的链接会显示该节点上运行的服务与组件。

![组件状态](./media/hdinsight-high-availability-linux/nodeservices.png)

有关如何使用 Ambari 的详细信息，请参阅[使用 Apache Ambari Web UI 监视和管理 HDInsight](hdinsight-hadoop-manage-ambari.md)。

### <a name="ambari-rest-api"></a>Ambari REST API

Ambari REST API 可以通过 Internet 使用。 HDInsight 公共网关处理以当前托管着 REST API 的头节点为目的地的路由请求。

可以通过 Ambari REST API 使用以下命令来检查服务的状态：

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* 将 **PASSWORD** 替换为 HTTP 用户 (admin) 帐户密码。
* 将 **CLUSTERNAME** 替换为群集的名称。
* 将 **SERVICENAME** 替换为要检查其状态的服务的名称。

例如，若要检查名为 **mycluster** 的群集上的、密码为 **password** 的 **HDFS** 服务的状态，可使用以下命令：

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

响应类似于以下 JSON：

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

该 URL 表示，服务当前在名为 **hn0-CLUSTERNAME** 的头节点上运行。

该状态表示，此服务目前正在运行，或“已启动”。

如果不知道有哪些服务安装在该群集上，可以使用以下命令检索列表：

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

若要详细了解如何使用 Ambari REST API，请参阅[使用 Apache Ambari REST API 监视和管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md)。

#### <a name="service-components"></a>服务组件

服务可能包含想要单独检查其状态的组件。 例如，HDFS 包含 NameNode 组件。 若要查看有关组件的信息，请使用以下命令：

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

如果不知道服务提供了哪些组件，可以使用以下命令检索列表：

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>如何访问头节点上的日志文件

### <a name="ssh"></a>SSH

通过 SSH 连接到头节点时，可以在 **/var/log** 中找到日志文件。 例如， **/var/log/hadoop-yarn/yarn** 包含 YARN 的日志。

每个头节点可能具有唯一的日志条目，因此应该检查两个头节点上的日志。

### <a name="sftp"></a>SFTP

也可以使用 SSH 文件传输协议或安全文件传输协议 (SFTP) 连接到头节点并直接下载日志。

与使用 SSH 客户端一样，在连接到群集时，必须提供 SSH 用户帐户名和群集的 SSH 地址。 例如， `sftp username@mycluster-ssh.azurehdinsight.net` 。 在出现提示时，提供帐户密码或使用 `-i` 参数提供公钥。

建立连接后，会出现 `sftp>` 提示符。 在此提示符下，可以更改目录以及上传和下载文件。 例如：以下命令将目录切换到 **/var/log/hadoop/hdfs** 目录，并下载该目录中的所有文件。

    cd /var/log/hadoop/hdfs
    get *

有关可用命令的列表，请在 `sftp>` 提示符下输入 `help`。

> [!NOTE]  
> 使用 SFTP 连接时，还会出现一个图形界面用于可视化文件系统。 例如，通过 [MobaXTerm](https://mobaxterm.mobatek.net/) 可以使用类似于 Windows 资源管理器的界面浏览文件系统。

### <a name="ambari"></a>Ambari

> [!NOTE]  
> 若要通过 Ambari 访问日志文件，必须使用 SSH 隧道。 个体服务的 Web 接口没有通过 Internet 公开。 有关使用 SSH 隧道的信息，请参阅[使用SSH 隧道](hdinsight-linux-ambari-ssh-tunnel.md)文档。

在 Ambari Web UI 中选择要查看其日志的服务（例如 YARN）。 然后使用“快速链接”选择要查看其日志的头节点。

![使用快速链接查看日志](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>如何配置节点大小

只能在创建群集期间选择节点大小。 可以在 [HDInsight 定价页](https://azure.microsoft.com/pricing/details/hdinsight/)上找到 HDInsight 可用的不同 VM 大小的列表。

创建群集时，可以指定节点的大小。 以下信息介绍了如何使用 [Azure 门户][preview-portal], [Azure PowerShell module Az][azure-powershell]和 [Azure CLI][azure-cli] 指定大小：

* **Azure 门户**：创建群集时，可以设置群集所用节点的大小：

    ![群集创建向导的图像，其中包含节点大小选项](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**：使用 [az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) 命令时，可以使用 `--headnode-size`、`--workernode-size` 和 `--zookeepernode-size` 参数设置头节点、辅助角色节点与 ZooKeeper 节点的大小。

* **Azure PowerShell**：使用 [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet 时，可以使用 `-HeadNodeSize`、`-WorkerNodeSize` 和 `-ZookeeperNodeSize` 参数设置头节点、辅助角色节点与 ZooKeeper 节点的大小。

## <a name="next-steps"></a>后续步骤

请使用以下链接深入了解本文档中所述的内容。

* [Apache Ambari REST 参考](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [安装和配置 Azure CLI](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [安装和配置 Azure PowerShell 模块 Az](/powershell/azure/overview)
* [使用 Apache Ambari 管理 HDInsight](hdinsight-hadoop-manage-ambari.md)
* [配置基于 Linux 的 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md

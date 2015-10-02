<properties
	pageTitle="HDInsight 中 Hadoop 群集的可用性 | Azure"
	description="基于 Linux 的 HDInsight 群集可以使用附加头节点提高可靠性和可用性。"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.date="07/24/2014"
	wacn.date=""/>

# HDInsight 中 Hadoop 群集的可用性和可靠性

第二个头节点由 Azure HDInsight 部署的基于 Linux 的 Hadoop 群集使用。这可以提高 Azure 中运行的 Hadoop 服务与作业的可用性和可靠性。

> [AZURE.NOTE]本文档中使用的步骤特定于基于 Linux 的 HDInsight 群集。如果你使用基于 Windows 的群集，请参阅 [HDInsight 中基于 Windows 的 Hadoop 群集的可用性和可靠性](hdinsight-high-availability.md)，以了解有关特定于 Windows 的信息。

## 了解头节点

Hadoop 的某些实现包含单个头节点，其中托管的服务和组件可以顺利处理数据（工作线程）节点失败的情况。但是，在头节点上运行的主控服务发生任何中断都会导致群集停止工作。

HDInsight 群集提供了一个辅助头节点，在主节点发生故障时，主控服务和组件可以继续在辅助节点上运行。

> [AZURE.IMPORTANT]这两个头节点同时处于活动状态并在群集中运行。某些服务，例如 HDFS 或 YARN，在任何给定的时间仅能在其中一个头节点上处于“活动”状态（在另一个头节点上保持“待机”状态）。HiveServer2 或 Hive MetaStore 等其他服务同时在这两个头节点上处于活动状态。

[ZooKeeper](http://zookeeper.apache.org/) 节点 (ZK) 用于头节点上主控服务的前置选择，可确保服务、数据（工作线程）节点和网关知道主控服务在哪个头节点上保持活动状态。

## 访问头节点

一般而言，使用多个头节点并不影响通过公共网关（Ambari Web 和 REST API）对群集的所有访问。请求将会适当地路由到活动的头节点和服务。

当使用 SSH 访问群集时，通过端口 22（SSH 的默认值）建立的连接将连接到 headnode0；通过端口 23 建立的连接将连接到 headnode1。

### 内部完全限定的域名 (FQDN)

HDInsight 群集中的节点具有内部 IP 地址和 FQDN，这些只能从群集访问（例如连接到头节点的 SSH 会话或群集上运行的作业。） 使用内部 FQDN 或 IP 地址访问群集上的服务时，应该使用 Ambari 来验证访问服务时使用的 IP 或 FQDN。

例如，Oozie 服务只能在一个头节点上运行，而且从 SSH 会话使用 `oozie` 命令需要有该服务的 URL。可以通过 Ambari 使用以下命令来检索 URL：

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

这将返回如下所示的值，其中包含要在 `oozie` 命令中使用的内部 URL：

	"oozie.base.url": "http://headnode0.CLUSTERNAME-ssh.d9.internal.cloudapp.net:11000/oozie"

## 如何检查服务状态

Ambari Web UI 或 Ambari REST API 可用于检查头节点中运行的服务的状态。

### Ambari REST API

可以通过 Ambari REST API 使用以下命令来检查服务的状态：

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* 将 **PASSWORD** 替换为 HTTP 用户 (admin) 的帐户密码

* 将 **CLUSTERNAME** 替换为群集的名称

* 将 **SERVICENAME** 替换为要检查其状态的服务的名称

例如，若要检查名为 **mycluster** 的群集上的、密码为 **password** 的 **HDFS** 服务的状态，可使用以下命令：

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

响应将如下所示：

	{
	  "href" : "http://headnode0.mycluster-ssh.j7.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

该 URL 告诉我们 **headnode0** 上目前运行的服务。

该状态告诉我们，此服务目前正在运行，或**已启动**。

如果不知道有哪些服务安装在该群集上，可以使用以下命令检索列表：

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

#### 服务组件

服务可能包含你想要单独检查状态的组件。例如，HDFS 包含 NameNode 组件。若要查看有关组件的信息，请使用以下命令：

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

如果你不知道服务提供了哪些组件，可以使用以下命令检索列表：

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

### Ambari Web UI

可以通过 https://CLUSTERNAME.azurehdinsight.net 查看 Ambari Web UI。将 **CLUSTERNAME** 替换为群集名称。如果出现提示，请输入群集的 HTTP 用户凭据。默认 HTTP 用户名为 **admin**，密码是创建群集时输入的密码。

出现 Ambari 页面时，该页的左侧将列出已安装的服务。

![已安装的服务](./media/hdinsight-high-availability-linux/services.png)

服务旁边可能会出现一系列表示状态的图标。你可以使用页面顶部的“警报”链接查看与服务相关的任何警报。你可以选择每个服务以查看其详细信息。

尽管服务页提供了有关每个服务的状态和配置的信息，但并不提供有关该服务正在哪个头节点上运行的信息。若要查看此信息，请使用页面顶部的“主机”链接。这会显示群集内的主机，包括头节点。

![主机列表](./media/hdinsight-high-availability-linux/hosts.png)

选择一个头节点的链接会显示该节点上运行的服务与组件。

![组件状态](./media/hdinsight-high-availability-linux/nodeservices.png)

## 如何访问辅助头节点上的日志文件

### SSH

通过 SSH 连接到头节点时，可以在 **/var/log** 中找到日志文件。例如，**/var/log/hadoop-yarn/yarn** 包含 YARN 的日志。

每个头节点可能具有唯一的日志条目，因此你应该检查两个头节点上的日志。

### Ambari

> [AZURE.NOTE]由于单个服务的网站不会在 Internet 上公开，因此通过 Ambari 访问日志文件需要建立 SSH 隧道。有关使用 SSH 隧道的信息，请参阅以下主题之一：
>
> * [在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md#tunnel)
>
> * [在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel)

从 Ambari Web UI 中，选择想要查看其日志的服务（例如 YARN），然后使用“快速链接”选择要查看哪个头节点的日志。

![使用快速链接查看日志](./media/hdinsight-high-availability-linux/viewlogs.png)

## 如何配置头节点的大小 ##

只能在创建群集期间选择头节点的大小。头节点的默认大小是 **A3**，这会提供 4 个核心，7GB 内存和 285GB 本地存储。可以在 [HDInsight 定价页](http://azure.microsoft.com/pricing/details/hdinsight/)中找到 HDInsight 可用的不同 VM 大小（包括核心、内存和本地存储）的列表。

创建新的群集时，你可以指定节点的大小。下面提供了有关如何使用 [Azure 预览门户][preview-portal]、[Azure PowerShell][azure-powershell] 和 [Azure CLI][azure-cli] 指定大小的信息：

* **Azure 预览门户**：创建新群集时，提供设置该群集头节点和数据（工作线程）节点大小的选项（定价层）：

	![群集创建向导的图像，其中包含节点大小选项](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**：使用 `azure hdinsight cluster create` 命令时，可以使用 `--headNodeSize` 参数设置头节点的大小。

* **Azure PowerShell**：使用 `New-AzureHDInsightCluster` cmdlet 时，可以使用 `-HeadNodeVMSize` 参数设置头节点的大小。

## 后续步骤

在本文档中，你已了解 Azure HDInsight 如何提高 Hadoop 的可用性。请使用以下链接深入了解本文档中所述的内容。

- [Ambari REST 参考](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [安装和配置 Azure CLI](../xplat-cli.md)

- [安装和配置 Azure PowerShell](../powershell-install-configure.md)

- [使用 Ambari 管理 HDInsight](hdinsight-hadoop-manage-ambari.md)

- [预配基于 Linux 的 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli.md

<!---HONumber=71-->
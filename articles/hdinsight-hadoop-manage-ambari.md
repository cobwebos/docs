<properties
   pageTitle="使用 Ambari 管理 HDInsight 群集 | Azure"
   description="了解如何使用 Ambari 监视和管理基于 Linux 的 HDInsight 群集。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight" 
   ms.devlang="" 
   ms.topic="article" 
   ms.tgt_pltfrm="na" 
   ms.workload="big-data" 
   ms.date="02/18/2015" 
   wacn.date="" 
   ms.author="larryfr"/>

# 使用 Ambari 管理 HDInsight 群集（预览版）

了解如何使用 Ambari 管理和监视基于 Linux 的 Azure HDInsight 群集。

> [AZURE.NOTE] 本文中的许多信息仅适用于基于 Linux 的 HDInsight 群集。对于基于 Windows 的 HDInsight 群集，只能通过 Ambari REST API 进行监视。请参阅[使用 Ambari API 监视 HDInsight 上的基于 Windows 的 Hadoop](hdinsight-monitor-use-ambari-api)。

## <a id="whatis"></a>什么是 Ambari？

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> 提供简单易用的 Web UI，让你可以设置、管理和监视 Hadoop 群集，以此简化 Hadoop 管理。开发人员可以使用 <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">Ambari REST API</a> 在其应用程序中集成这些功能。 

基于 Linux 的 HDInsight 群集已按默认提供 Ambari。基于 Windows 的 HDInsight 群集通过 Ambari REST API 提供监视功能。

## SSH 代理

> [AZURE.NOTE] 虽然可以直接通过 Internet 访问群集的 Ambari，但若要使用某些功能，则需要根据访问群集所用的内部域名的节点来达到目的。由于这是内部域名且未公开，因此，在尝试通过 Internet 访问某些功能时，你将会收到"找不到服务器"的错误。

若要解决此问题，请使用 Secure Shell (SSH) 隧道通过代理将 Web 流量传送到群集头节点，以便能够成功解析内部域名。使用以下文章创建从本地计算机上的端口到群集的 SSH 隧道：

* <a href="/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用</a> - 通过使用  `ssh` 命令创建 SSH 隧道的步骤。

* <a href="/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用</a> - 使用 PuTTY 创建 SSH 隧道的步骤

## Ambari Web UI

Ambari Web UI 适用于每个你在 **https://&lt;群集名称>.azurehdinsight.cn** 上创建的基于 Linux 的 HDInsight 群集。你也可以通过 Azure 门户的群集仪表板底部的"Ambari Web"按钮来访问此页面。

![ambari web icon](./media/hdinsight-hadoop-manage-ambari/ambari-web.png)

系统将提示你向该页进行两次身份验证。第一个提示是向 HDInsight 群集进行身份验证，而第二个提示是向 Ambari 进行身份验证。

* **群集身份验证** - 使用群集管理员用户名（默认值为 **admin**）和密码。

* **Ambari 身份验证** - 用户名和密码的默认值都是 **admin**。

	> [AZURE.NOTE] 如果你已更改 **admin** 用户的密码，则必须输入新密码。

当该页打开时，请注意顶栏。这包含以下信息和控件：

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari 徽标** - 打开仪表板，以便可以监视群集。

* **群集名称选项数** - 显示正在进行的 Ambari 操作的数目。选择群集名称或**"选项数"**将显示后台操作的列表。

* **警报数** - 与群集相关的警告或严重警报（如果有）。选择此项将显示警报的列表。

* **仪表板** - 显示仪表板。

* **服务** - 群集中服务的信息和配置设置。

* **主机** - 群集中节点的信息和配置设置。

* **警报** - 包含信息、警告和严重警报的日志。

* **管理** - 安装或可添加到群集的软件堆栈/服务、服务帐户信息和 Kerberos 安全性。

* **管理按钮** - Ambari 管理、用户设置和注销。

### 监视

#### 警报

Ambari 提供许多警报，其可能状态如下：

* **正常**

* **警告**

* **关键**

* **未知**

除"正常"以外的警报会导致页面顶部以"# 个警报"条目显示警报数目。选择此条目会显示警报及其状态。

警报已组织成若干个默认组，你可以从"警报"页面进行查看。 

![alerts page](./media/hdinsight-hadoop-manage-ambari/alerts.png)

你可以通过使用"操作"菜单并选择"管理警报组"来管理这些组。这样，你便可以修改现有组，或创建新组。

![manage alert groups dialog](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

你还可以从**"操作"**菜单创建警报通知。这样，你便可以创建触发器，以在发生特定的警报/严重性组合时，通过"电子邮件"或"SNMP"发送通知。例如，你可以在"YARN 默认设置"组中的任何警报设为"关键"时发送警报。

![Create alert dialog](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

#### 群集

仪表板的**"指标"**选项卡包含一系列 Widget，可让你一目了然地轻松监视群集状态。"CPU 使用率"等多个 Widget 可在单击后提供更多信息。

![dashboard with metrics](./media/hdinsight-hadoop-manage-ambari/metrics.png)

"热图"选项卡以绿色到红色的彩色热图显示度量值。

![dashboard with heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

如需有关群集中节点的详细信息，请选择"主机"，然后选择所需的特定节点。

![host details](./media/hdinsight-hadoop-manage-ambari/host-details.png)

#### 服务

仪表板上的"服务"边栏可让你快速了解群集上运行的服务的状态。其中使用了各种图标来指出状态或应采取的措施，例如，如果需要回收某个服务，便会显示黄色回收符号。

![services side-bar](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

选择一个服务会显示有关该服务的更多详细信息。

![service summary information](./media/hdinsight-hadoop-manage-ambari/service-details.png)

##### 快速链接

某些服务会在页面顶部显示"快速链接"链接。这可以用于访问特定于服务的 Web UI，例如：

* **作业历史记录** - MapReduce 作业历史记录。

* **资源管理器** - YARN ResourceManager UI。

* **NameNode** - Hadoop 分布式文件系统 (HDFS) NameNode UI。

* **Oozie Web UI** - Oozie UI。

选择其中任何一个链接会在浏览器中打开新选项卡，以显示选择的页面。

> [AZURE.NOTE] 选择任何服务的"快速链接"链接将会导致出现"找不到服务器"的错误，除非你使用安全套接字层 (SSL) 隧道通过代理将 Web 流量发送到群集。这是因为，Ambari 使用这些链接的内部域名。
> 
> 有关将 SSL 隧道与 HDInsight 配合使用的信息，请参阅以下主题之一：
> 
> * <a href="/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用</a> - 通过使用  `ssh` 命令创建 SSH 隧道的步骤。
>
>* <a href="/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用</a> - 使用 PuTTY 来创建 SSH 隧道的步骤。

### 管理

#### Ambari 用户、组和权限

在预览基于 Linux 的 HDInsight 期间，不应使用用户、组和权限管理功能。

#### 主机

"主机"页面列出群集中的所有主机。若要管理主机，请遵循以下步骤。

![hosts page](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE] 对于 HDInsight 群集，不应使用添加、停用或重用主机的功能。

1. 选择你要管理的主机。

2. 使用**"操作"**菜单选择你要执行的操作：

	* **启动所有组件** - 启动主机上的所有组件。

	* **停止所有组件** - 停止主机上的所有组件。

	* **重新启动所有组件** - 停止然后启动主机上的所有组件。

	* **打开维护模式** - 隐藏主机的警报。如果你要执行操作，以生成重新启动正在运行的服务所依赖的服务等警报，则应启用此项。

	* **关闭维护模式** - 让主机恢复正常警报功能。

	* **停止** - 停止主机上的 DataNode 或 NodeManagers。

	* **启动** - 启动主机上的 DataNode 或 NodeManagers。

	* **重新启动** - 停止然后启动主机上的 DataNode 或 NodeManagers。

	* **停用** - 从群集中删除主机。

		> [AZURE.NOTE] 请勿在 HDInsight 群集上使用此操作。

	* **重用** - 将以前已停用的主机添加到群集中。

		> [AZURE.NOTE] 请勿在 HDInsight 群集上使用此操作。

#### <a id="service"></a>服务

在"仪表板"或"服务"页中，使用服务列表底部的"操作"按钮来添加新服务，或停止然后启动所有服务。

![service actions](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

下面是添加服务的一般步骤：

1. 在"仪表板"或"服务"页面中，使用"操作"按钮并选择"添加服务"。

2. 在"添加服务向导"中，选择要添加的服务，然后单击"下一步"。

	![add service](./media/hdinsight-hadoop-manage-ambari/add-service.png)

3. 继续运行向导，提供服务的配置信息。有关配置要求的详细信息，请参阅你要添加的特定服务的相关文档。

4. 在"审阅"页面中，可以使用"打印"来打印配置信息，或使用"部署"将服务部署到群集。

5. 部署服务后，"安装、启动和测试"页面将在安装和测试服务时显示进度信息。当"状态"变为绿色后，请选择"下一步"。

	![image of install, start, and test page](./media/hdinsight-hadoop-manage-ambari/install-start-test.png)

6. **"摘要"**页显示安装过程的摘要，以及你需要执行的任何可能的操作；例如重新启动其他服务。选择"完成"退出向导。

虽然"操作"按钮可以重新启动所有服务，但你要启动、停止或重新启动的往往是特定服务。使用以下步骤来对单个服务执行操作：

1. 从"仪表板"或"服务"页面中选择一个服务。

2. 在"摘要"选项卡的顶部，使用"服务操作"按钮，然后选择要执行的操作。这会重新启动所有节点上的服务。

	![service action](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE] 在群集运行时重新启动某些服务可能会生成警报。若要避免这个问题，可以使用"服务操作"按钮来启用服务的"维护模式"，然后再执行重新启动。

3. 选择某个操作后，页面顶部的"# 项操作"条目便会递增数字，指出正在进行后台操作。如果已配置为显示，则将显示后台操作的列表。

	> [AZURE.NOTE] 如果你已对服务启用了**"维护模式"**，请记得在操作完成后使用"服务操作"按钮来将它禁用。

若要配置服务，请使用以下步骤：

1. 从"仪表板"或"服务"页面中选择一个服务。

2. 选择"配置"选项卡。此时会显示当前配置。同时，还会显示以前的配置列表。

	![configurations](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. 使用显示的字段修改配置，然后选择"保存"。或者，选择以前的某个配置，然后选择"设为当前配置"以回滚到以前的设置。

## REST API

Ambari Web 依赖于基础 REST API，你可以使用 REST API 创建自己的管理和监视工具。虽然 API 相对简单易用，但仍有一些要注意的 Azure 特殊事项：

* **身份验证** - 群集管理员用户名（默认值为 **admin**）和密码应用于向服务进行身份验证。

* **安全性** - Ambari 使用基本身份验证，因此在与 API 进行通信时，应始终使用安全的 HTTP (HTTPS)。

* **IP 地址** - 你无法从群集外部访问为群集内的主机返回的地址，除非群集是 Azure 虚拟网络的成员。即便是这样，也只能由虚拟网络的其他成员访问 IP 地址，而不能从网络外部访问。

* **某项功能未启用** - 禁用某项 Ambari 功能，因为它由 HDInsight 云服务管理；例如，从群集中添加或删除主机。其他功能有可能未在基于 Linux 的 HDInsight 预览期内完全实现。

有关 REST API 的完整参考，请参阅 [Ambari API 参考 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

<!---HONumber=56-->
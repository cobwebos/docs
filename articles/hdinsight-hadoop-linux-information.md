<properties
   pageTitle="基于 Linux 的 HDInsight 上的 Hadoop 须知信息 | Azure"
   description="基于 Linux 的 HDInsight 群集在你熟悉的 Linux 环境中提供可在 Azure 云中运行的 Hadoop。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/17/2015"
   wacn.date="" 
   ms.author="larryfr"/>

# 在 Linux 上使用 HDInsight（预览版）

基于 Linux 的 Azure HDInsight 群集提供基于熟悉的 Linux 环境并在 Azure 云中运行的 Hadoop。在大多数情况下，它的工作方式应该与其他任何 Hadoop-on-Linux 安装完全相同。本文档指出了你应该注意的具体差异。

## 域名

连接到群集时要使用的完全限定域名 (FQDN) 是 **&lt;群集名称>.azurehdinsight.net** 或（仅限 SSH）**&lt;clustername-ssh>.azurehdinsight.cn**。


## 对服务的远程访问

* **Ambari (Web)** - https://&lt;群集名称>.azurehdinsight.cn

	> [AZURE.NOTE] 使用群集管理员用户和密码进行身份验证，然后登录到 Ambari。这也使用群集管理员用户和密码。
	>
	> 身份验证是纯文本身份验证 - 始终使用 HTTPS 来帮助确保连接是安全的。

	虽然可以直接通过 Internet 访问群集的 Ambari，但若要使用某些功能，则需要根据访问群集所用的内部域名的节点来达到目的。由于这是内部域名且未公开，因此，在尝试通过 Internet 访问某些功能时，你将会收到"找不到服务器"的错误。

	若要解决此问题，请使用 SSH 隧道通过代理将 Web 流量传送到群集头节点。使用以下文章中的**"SSH 隧道"**部分，创建从本地计算机上的端口到群集的 SSH 隧道：

	* [在 Linux、Unix 或 OS X](hdinsight-hadoop-linux-use-ssh-unix) 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用：通过使用  `ssh` 命令创建 SSH 隧道的步骤。

	* [在 Windows](hdinsight-hadoop-linux-use-ssh-windows) 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用：使用 PuTTY 创建 SSH 隧道的步骤。

* **Ambari (REST)** - https://&lt;群集名称>.azurehdinsight.cn/ambari

	> [AZURE.NOTE] 通过使用群集管理员用户和密码进行身份验证。
	> 
	> 身份验证是纯文本身份验证 - 始终使用 HTTPS 来帮助确保连接是安全的。

* **WebHCat (Templeton)** - https://&lt;群集名称>.azurehdinsight.cn/templeton

	> [AZURE.NOTE] 通过使用群集管理员用户和密码进行身份验证。
	>
	> 身份验证是纯文本身份验证 - 始终使用 HTTPS 来帮助确保连接是安全的。

* **SSH** - 端口 22 上的 &lt;群集名称>-ssh.azurehdinsight.cn

	> [AZURE.NOTE] 你只能通过 SSH 从客户端计算机访问群集头节点。在连接后，你可以通过使用 SSH 从头节点访问从节点。

## 文件位置

与 Hadoop 相关的文件可以在群集节点上的  `/usr/hdp/current` 处找到。

示例数据和 JAR 文件可以在 Hadoop 分布式文件系统 (HDFS) 或 Azure Blob 存储上的"/example"或"wasb:///example"处找到。

## HDFS、Azure Blob 存储和存储最佳做法

在大部分的 Hadoop 分发中，HDFS 受群集中计算机上的本地存储的支持。尽管这种方式很有效率，但用于基于云的解决方案时可能费用高昂，因为计算资源以小时为单位来计费。

HDInsight 使用 Azure Blob 存储作为默认存储，以提供以下优势：

* 成本低廉的长期存储

* 可从外部服务访问，例如网站、文件上载/下载实用程序、各种语言 SDK 和 Web 浏览器

由于它是 HDInsight 的默认存储，因此你通常不需要进行任何设置就能使用。例如，以下命令将列出 **/example/data** 文件夹中的文件，该文件夹存储在 Azure Blob 存储上：

	hadoop fs -ls /example/data

一些命令可能会要求你指定使用的是 Blob 存储。对于这些命令，你可以在它的前面加上前缀 **WASB://**.

HDInsight 还允许你将多个 Blob 存储帐户与群集相关联。若要访问非默认 Blob 存储帐户上的数据，可以使用格式 **WASB://&lt;container-name>@&lt;account-name>.blob.core.chinacloudapi.cn/**. 例如，下面将列出指定容器和 Blob 存储帐户的 **/example/data** 目录的内容：

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.chinacloudapi.cn/example/data

### 群集要使用哪种 Blob 存储？

在群集创建期间，你选择了使用现有的 Azure 存储帐户和容器，或创建新的存储帐户和容器。事后，你可能会忘记了该存储帐户和容器。你可以通过使用以下方法查找存储帐户和容器。

**Ambari API**

1. 使用以下命令可检索 HDFS 配置信息：

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. 查找  `fs.defaultFS` 条目。其中将包含如下格式的默认容器和存储帐户名称：

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.chinacloudapi.cn

> [AZURE.TIP] 如果你已安装 [jq](http://stedolan.github.io/jq/)，则可以使用以下方式仅返回  `fs.defaultFS` 条目：
>
> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`

**Azure 门户**

1. 在 [Azure 门户](https://manage.windowsazure.cn/)中，选择你的 HDInsight 群集。

2. 选择页面顶部的"仪表板"。

3. 存储帐户和容器在该页的**"链接的资源"**部分中列出。

	![linked resources](./media/hdinsight-hadoop-linux-information/storageportal.png)

### 如何访问 Blob 存储？

除了通过群集的 Hadoop 命令，还有各种不同方式可用来访问 Blob：

* [Azure 跨平台命令行界面](xplat-cli)：用于 Azure 的跨平台命令。在安装后，使用  `azure storage` 命令获取使用存储的帮助，或者使用  `azure blob` 获取特定于 Blob 的命令。

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage)：用于 Azure 存储中的 Blob 的 python 脚本。

* 各种 SDK：

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.js](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [存储 REST API](https://msdn.microsoft.com/zh-cn/library/azure/dd135733.aspx)

## 后续步骤

* [将 Hive 与 HDInsight 配合使用](/documentation/articles/hdinsight-use-hive/)
* [将 Pig 与 HDInsight 配合使用](/documentation/articles/hdinsight-use-pig/)
* [将 MapReduce 作业与 HDInsight 配合使用](/documentation/articles/hdinsight-use-mapreduce)

<!---HONumber=56-->
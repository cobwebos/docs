---
title: "有关在基于 Linux 的 HDInsight 上使用 Hadoop 的提示 | Microsoft Docs"
description: "获取有关在 Azure 云中运行的你所熟悉的 Linux 环境中使用基于 Linux 的 HDInsight (Hadoop) 群集的实施提示。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 854df55045d4d9a062ade3905b9be1daad0a7d8c
ms.openlocfilehash: 3ce0444427ba10e7247aec500017ea4bae3af161


---
# <a name="information-about-using-hdinsight-on-linux"></a>有关在 Linux 上使用 HDInsight 的信息

基于 Linux 的 Azure HDInsight 群集提供基于熟悉的 Linux 环境并在 Azure 云中运行的 Hadoop。 在大多数情况下，它的工作方式应该与其他任何 Hadoop-on-Linux 安装完全相同。 本文档指出了你应该注意的具体差异。

## <a name="prerequisites"></a>先决条件

此文档的许多步骤都使用以下实用程序，可能需要在系统上安装这些实用程序。

* [cURL](https://curl.haxx.se/) - 用于与基于 Web 的服务进行通信
* [jq](https://stedolan.github.io/jq/) - 用于分析 JSON 文档
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)（预览版）- 用于远程管理 Azure 服务

## <a name="users"></a>用户

除非[加入域](hdinsight-domain-joined-introduction.md)，HDInsight 应被视为**单用户**系统。 单一 SSH 用户帐户是使用具有管理员级别权限的群集创建的。 可创建其他 SSH 帐户，但它们也具有对群集的管理员权限。

加入域的 HDInsight 为多个用户、更具体的权限以及角色设置提供支持。 有关详细信息，请参阅[管理已加入域的 HDInsight 群集](hdinsight-domain-joined-manage.md)。

## <a name="domain-names"></a>域名

从 Internet 连接到群集时要使用的完全限定域名 (FQDN) 是**&lt;clustername>.azurehdinsight.net** 或（仅限 SSH）**&lt;clustername-ssh>.azurehdinsight.net**。

在内部，群集中每个节点都具有一个名称，该名称在群集配置期间分配。 若要查找群集名称，可以访问 Ambari Web UI 上的“主机”页，或者使用以下步骤从 Ambari REST API 返回主机列表：

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

将 **PASSWORD** 替换为管理员帐户的密码，并将 **CLUSTERNAME** 替换为你的群集名称。 这将返回包含群集中主机列表的 JSON 文档，然后 jq 将拉取群集中每个主机的 `host_name` 元素值。

如果要查找某个特定服务的节点名称，可以查询该组件的 Ambari。 例如，若要查找 HDFS 名称节点的主机，请使用以下方法。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

这将返回一个描述该服务的 JSON 文档，然后 jq 将只拉取主机的 `host_name` 值。

## <a name="remote-access-to-services"></a>对服务的远程访问

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

    使用群集管理员用户和密码进行身份验证，然后登录到 Ambari。 这也使用群集管理员用户和密码。

    身份验证是纯文本身份验证 - 始终使用 HTTPS 来帮助确保连接是安全的。

    > [!IMPORTANT]
    > 虽然可以直接通过 Internet 访问群集的 Ambari，但若要使用某些功能，则需要根据访问群集所用的内部域名的节点来达到目的。 由于这是内部域名且未公开，因此，在尝试通过 Internet 访问某些功能时，你将会收到“找不到服务器”的错误。
    >
    > 若要使用 Ambari web UI 的全部功能，请使用 SSH 隧道通过代理将 Web 流量传送到群集头节点。 请参阅[使用 SSH 隧道访问 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 和其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

    > [!NOTE]
    > 通过使用群集管理员用户和密码进行身份验证。
    >
    > 身份验证是纯文本身份验证 - 始终使用 HTTPS 来帮助确保连接是安全的。

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

    > [!NOTE]
    > 通过使用群集管理员用户和密码进行身份验证。
    >
    > 身份验证是纯文本身份验证 - 始终使用 HTTPS 来帮助确保连接是安全的。

* 端口 22 或 23 上的 **SSH** - &lt;clustername>-ssh.azurehdinsight.net。 端口 22 用于连接主头结点，而端口 23 用于连接辅助头结点。 有关头节点的详细信息，请参阅 [HDInsight 中的 Hadoop 群集的可用性和可靠性](hdinsight-high-availability-linux.md)。

    > [!NOTE]
    > 只能通过 SSH 从客户端计算机访问群集头节点。 连接后，可以通过使用 SSH 从头节点访问辅助角色节点。

## <a name="file-locations"></a>文件位置

Hadoop 相关文件可在群集节点上的 `/usr/hdp` 中找到。 此目录包含以下子目录：

* **2.2.4.9-1**：此目录是根据 HDInsight 使用的 Hortonworks 数据平台版本命名的，因此群集上的编号可能不同于此处列出的编号。
* **current**：此目录包含 **2.2.4.9-1** 目录下的目录的链接，有了此目录，每次访问某个文件时，便不需要键入版本号（可能会变化）。

示例数据和 JAR 文件可以在 Hadoop 分布式文件系统 (HDFS) 或 Azure Blob 存储上的“/example”或“wasbs:///example”处找到。

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>HDFS、Azure Blob 存储和存储最佳做法

在大部分的 Hadoop 分发中，HDFS 受群集中计算机上的本地存储的支持。 尽管这种方式很有效率，但用于基于云的解决方案时可能费用高昂，因为计算资源以小时或分钟为单位来计费。

HDInsight 使用 Azure Blob 存储作为默认存储，以提供以下优势：

* 成本低廉的长期存储
* 可从外部服务访问，例如网站、文件上载/下载实用程序、各种语言 SDK 和 Web 浏览器

由于它是 HDInsight 的默认存储，因此你通常不需要进行任何设置就能使用。 例如，以下命令将列出 **/example/data** 文件夹中的文件，该文件夹存储在 Azure Blob 存储上：

    hdfs dfs -ls /example/data

一些命令可能会要求你指定使用的是 Blob 存储。 对于这些命令，可以在它的前面加上前缀 **wasb://** 或 **wasbs://**。

HDInsight 还允许你将多个 Blob 存储帐户与群集相关联。 若要访问非默认 Blob 存储帐户上的数据，可以使用以下格式：**wasbs://&lt;container-name>@&lt;account-name>.blob.core.windows.net/**。 例如，以下命令会列出指定容器和 Blob 存储帐户的 **/example/data** 目录的内容：

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>群集要使用哪种 Blob 存储？

在群集创建期间，你选择了使用现有的 Azure 存储帐户和容器，或创建新的存储帐户和容器。 事后，你可能会忘记了该存储帐户和容器。 你可以使用 Ambari REST API 查找默认的存储帐户和容器。

1. 使用以下命令可使用 curl 检索 HDFS 配置信息，然后使用 [jq](https://stedolan.github.io/jq/) 进行筛选：

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'

    > [!NOTE]
    > 这将返回应用到服务器的第一个配置 (`service_config_version=1`)，其中包含此信息。 如果要检索创建群集后修改的值，可能需要列出配置版本并检索最新版本。

    这将返回值类似于下面的值，其中，“CONTAINER”是默认容器，“ACCOUNTNAME”是 Azure 存储帐户名：

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

2. 使用以下命令获取存储帐户的唯一 ID。 在以下命令中，将 **ACCOUNTNAME** 替换为从 Ambari 检索的存储帐户名：

        az storage account list --query "[?name=='ACCOUNTNAME'].id --out list

3. 使用以下命令获取存储帐户的密钥。 将 **STORAGEID** 替换为存储帐户 ID：

        az storage account keys list --ids STORAGEID --out list

    这将返回帐户的主密钥。

还可以使用 Azure 门户查找存储信息：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 HDInsight 群集。
2. 从“基本功能”部分中选择“所有设置”。
3. 从“设置”中，选择“Azure 存储密钥”。
4. 从“Azure 存储密钥”中，选择一个列出的存储帐户。 这将显示有关存储帐户的信息。
5. 选择密钥图标。 这将显示此存储帐户的密钥。

### <a name="how-do-i-access-blob-storage"></a>如何访问 Blob 存储？

除了通过群集的 Hadoop 命令，还有各种不同方式可用来访问 Blob：

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)：适用于 Azure 的命令行接口命令。 在安装后，使用 `az storage` 命令获取有关使用存储的帮助，或者使用 `az storage blob` 获取特定于 Blob 的命令。
* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage)：用于 Azure 存储中的 blob 的 python 脚本。
* 各种 SDK：

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [存储 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

## <a name="a-namescalingascaling-your-cluster"></a><a name="scaling"></a>缩放你的群集

群集缩放功能可让你更改 Azure HDInsight 中运行的群集使用的数据节点数，而无需删除然后再重新创建群集。

你可以在其他作业或进程正在群集上运行时执行缩放操作。

不同的群集类型会受缩放操作影响，如下所示：

* **Hadoop**：减少群集中的节点数时，群集中的某些服务将重新启动。 这会导致正在运行或挂起的作业在缩放操作完成时失败。 你可以在操作完成后重新提交这些作业。
* **HBase**：在完成缩放操作后的几分钟内，区域服务器会自动进行平衡。 若要手动平衡区域服务器，请使用以下步骤：

    1. 使用 SSH 连接到 HDInsight 群集。 有关如何将 SSH 与 HDInsight 配合使用的详细信息，请参阅以下文档之一：

        * [在 Linux、Unix 和 Mac OS X 上将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [在 Windows 上将 SSH (PuTTY) 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)

    2. 使用以下命令来启动 HBase shell：

            hbase shell
    
    3. 加载 HBase shell 后，使用以下方法来手动平衡区域服务器：

            balancer

* **Storm**：应在执行缩放操作后重新平衡任何正在运行的 Storm 拓扑。 这允许拓扑根据群集中的新节点数重新调整并行度设置。 若要重新平衡正在运行的拓扑，请使用下列选项之一：

    * **SSH**：连接到服务器并使用以下命令来重新平衡拓扑：

            storm rebalance TOPOLOGYNAME

        你还可以指定参数来替代拓扑原来提供的并行度提示。 例如，`storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` 会将拓扑重新配置为 5 个工作进程，蓝色的 BlueSpout 组件有 3 个 executor，黄色 YellowBolt 组件有 10 个 executor。

    * **Storm UI**：使用以下步骤来重新平衡使用 Storm UI 的拓扑。

        1. 在 Web 浏览器中打开 **https://CLUSTERNAME.azurehdinsight.net/stormui**，其中“CLUSTERNAME”是 Storm 群集的名称。 如果系统提示，请输入创建群集时指定的 HDInsight 群集管理员 (admin) 名称和密码。
        2. 选择要重新平衡的拓扑，然后选择“重新平衡”按钮。 输入执行重新平衡操作前的延迟。

有关缩放 HDInsight 群集的特定信息，请参阅：

* [使用 Azure 门户管理 HDInsight 中的 Hadoop 群集](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [使用 Azure PowerShell 管理 HDinsight 中的 Hadoop 群集](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>如何安装 Hue（或其他 Hadoop 组件）？

HDInsight 是一项托管服务，这意味着如果检测到问题，Azure 可能会自动破坏并重新预配群集中的节点。 因此，不建议直接在群集节点上手动安装组件。 需要安装以下内容时，请改用 [HDInsight 脚本操作](hdinsight-hadoop-customize-cluster.md)：

* 服务或网站，如 Spark 或 Hue。
* 需要更改群集中多个节点上的配置的组件。 例如，必需的环境变量，创建日志目录，或创建配置文件。

脚本操作是在群集预配期间运行的 Bash 脚本，可用于在群集上安装并预配其他组件。 提供了用于安装以下组件的示例脚本：

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

有关开发你自己的脚本操作的信息，请参阅 [使用 HDInsight 进行脚本操作开发](hdinsight-hadoop-script-actions-linux.md)。

### <a name="jar-files"></a>jar 文件

一些 Hadoop 技术以自包含 jar 文件形式提供，这些文件是用作 MapReduce 作业的部分或来自 Pig 或 Hive 的包含函数。 虽然可以使用脚本操作对其进行安装，但它们通常不需要任何设置，预配后即可上传到群集，并直接使用。 如果要确保组件存在群集的重置映像，可以将 jar 文件存储在 WASB 中。

例如，如果要使用 [DataFu](http://datafu.incubator.apache.org/) 的最新版本，可以下载包含项目的 jar，并将其上传到 HDInsight 群集。 然后按照关于如何从 Pig 或 Hive 中使用它的 DataFu 文档进行操作。

> [!IMPORTANT]
> 一些属于独立 jar 文件的组件是 HDInsight 随附的，但是不在路径中。 如果正在寻找特定组件，可以使用以下步骤在群集上进行搜索：
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> 这将返回任何匹配的 jar 文件的路径。

如果群集已提供作为独立 jar 文件的组件的版本，但是你希望使用不同的版本，则可以将新版本组件上传到群集，然后尝试在你的作业中使用它。

> [!WARNING]
> 完全支持通过 HDInsight 群集提供的组件，Microsoft 支持部门将帮助你找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助你进一步排查问题。 这可能导致问题解决，或要求你参与可用的开放源代码技术渠道，在该处可找到该技术的深入专业知识。 有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)和 [http://stackoverflow.com](http://stackoverflow.com)。 此外，Apache 项目在 [http://apache.org](http://apache.org) 上提供了项目站点，例如 [Hadoop](http://hadoop.apache.org/)、[Spark](http://spark.apache.org/)。

## <a name="next-steps"></a>后续步骤

* [从基于 Windows 的 HDInsight 迁移到基于 Linux 的 HDInsight](hdinsight-migrate-from-windows-to-linux.md)
* [将 Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 作业与 HDInsight 配合使用](hdinsight-use-mapreduce.md)



<!--HONumber=Dec16_HO1-->



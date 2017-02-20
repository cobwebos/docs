---
title: "有关在基于 Linux 的 HDInsight 上使用 Hadoop 的提示 - Azure | Microsoft Docs"
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
ms.date: 02/02/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 1d2d3d9d6c8dee02f2eb96ba20894e1d52541102
ms.openlocfilehash: 584af73f3f2d428f7551de0b12b498b1a118e5dc


---
# <a name="information-about-using-hdinsight-on-linux"></a>有关在 Linux 上使用 HDInsight 的信息

Azure HDInsight 群集提供基于熟悉的 Linux 环境并在 Azure 云中运行的 Hadoop。 在大多数情况下，它的工作方式应该与其他任何 Hadoop-on-Linux 安装完全相同。 本文档指出了你应该注意的具体差异。

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

## <a name="prerequisites"></a>先决条件

此文档的许多步骤都使用以下实用程序，可能需要在系统上安装这些实用程序。

* [cURL](https://curl.haxx.se/) - 用于与基于 Web 的服务进行通信
* [jq](https://stedolan.github.io/jq/) - 用于分析 JSON 文档
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)（预览版）- 用于远程管理 Azure 服务

## <a name="users"></a>用户

除非[加入域](hdinsight-domain-joined-introduction.md)，HDInsight 应被视为**单用户**系统。 单一 SSH 用户帐户是使用具有管理员级别权限的群集创建的。 可创建其他 SSH 帐户，但它们也具有对群集的管理员权限。

加入域的 HDInsight 支持多个用户、更具体的权限以及角色设置。 有关详细信息，请参阅[管理已加入域的 HDInsight 群集](hdinsight-domain-joined-manage.md)。

## <a name="domain-names"></a>域名

从 Internet 连接到群集时要使用的完全限定域名 (FQDN) 是**&lt;clustername>.azurehdinsight.net** 或（仅限 SSH）**&lt;clustername-ssh>.azurehdinsight.net**。

在内部，群集中每个节点都具有一个名称，该名称在群集配置期间分配。 若要查找群集名称，请参阅 Ambari Web UI 上的“主机”页面。 还可以使用以下方法从 Ambari REST API 返回主机列表：

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

将 **PASSWORD** 替换为管理员帐户的密码，并将 **CLUSTERNAME** 替换为你的群集名称。 这将返回包含群集中主机列表的 JSON 文档，然后 jq 将拉取群集中每个主机的 `host_name` 元素值。

如果要查找某个特定服务的节点名称，可以查询该组件的 Ambari。 例如，若要查找 HDFS 名称节点的主机，请使用以下方法。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

该请求将返回一个描述该服务的 JSON 文档，然后 jq 将只拉取主机的 `host_name` 值。

## <a name="remote-access-to-services"></a>对服务的远程访问

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

    使用群集管理员用户和密码进行身份验证，然后登录到 Ambari。 必须使用群集管理员用户和密码进行身份验证。

    身份验证是纯文本身份验证 - 始终使用 HTTPS 来帮助确保连接是安全的。

    > [!IMPORTANT]
    > 虽然可以直接通过 Internet 访问群集的 Ambari，但若要使用某些功能，则需要根据访问群集所用的内部域名的节点来达到目的。 由于这是内部域名且未公开，因此，在尝试通过 Internet 访问某些功能时，你可能会收到“找不到服务器”的错误。
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

示例数据和 JAR 文件可以在 Hadoop 分布式文件系统 (HDFS) 或 Azure Blob 存储上的 `/example` 和 `/HdiSamples` 处找到

## <a name="hdfs-blob-storage-and-data-lake-store"></a>HDFS、Blob 存储和 Data Lake Store

在大部分的 Hadoop 分发中，HDFS 受群集中计算机上的本地存储的支持。 尽管这种方式很有效率，但用于基于云的解决方案时可能费用高昂，因为计算资源以小时或分钟为单位来计费。

HDInsight 使用 Azure Blob 存储或 Azure Data Lake Store 作为默认存储。 这可以提供以下优点：

* 成本低廉的长期存储
* 可从外部服务访问，例如网站、文件上载/下载实用程序、各种语言 SDK 和 Web 浏览器

> [!IMPORTANT]
> Blob 存储最多可容纳 4.75 TB，但单个 blob（或 HDInsight 透视图中的文件）容量最多仅可达 195 GB。 Azure Data Lake Store 可以动态增长以保存数万亿个文件，并且单个文件大于&1; PB。
>
> 有关详细信息，请参阅[了解 blob](https://docs.microsoft.com/rest/api/storageservices/fileservices/understanding-block-blobs--append-blobs--and-page-blobs) 和 [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)。

使用 Azure 存储或 Data Lake Store 时，通常不需要从 HDInsight 进行任何特殊操作即可访问数据。 例如，以下命令将列出 `/example/data` 文件夹中的文件，无论它是存储在 Azure Blob 存储还是 Data Lake Store 上：

    hdfs dfs -ls /example/data

### <a name="uri-and-scheme"></a>URI 和方案

某些命令可能需要你在访问文件时将方案指定为 URI 的一部分。 例如，Storm-HDFS 组件就需要指定方案。 使用非默认存储（作为“附加”存储添加到群集的存储）时，必须始终将方案作为 URI 的一部分来使用。

使用 __Blob 存储__时，方案可以是以下之一：

* `wasb:///`：使用未加密通信访问默认存储。

* `wasbs:///`：使用加密通信访问默认存储。

* `wasbs://<container-name>@<account-name>.blob.core.windows.net/`：与非默认存储帐户通信时使用。 例如，有额外的存储帐户时，或访问存储在可公开访问的存储帐户中的数据时。

使用 __Data Lake Store__ 时，方案可以是以下之一：

* `adl:///`：访问群集的默认 Data Lake Store。

* `adl://<storage-name>.azuredatalakestore.net/`：与非默认 Data Lake Store 进行通信或访问 HDInsight 群集根目录以外的数据时使用。

> [!IMPORTANT]
> 使用 Data Lake Store 作为 HDInsight 的默认存储时，必须在存储中指定一个用作 HDInsight 存储根目录的路径。 默认路径为 `/clusters/<cluster-name>/`。
>
> 使用 `/` 或 `adl:///` 访问数据时，只能访问存储在群集根目录（例如 `/clusters/<cluster-name>/`）中的数据。 若要在商店中的任意位置访问数据，请使用 `adl://<storage-name>.azuredatalakestore.net/` 格式。

### <a name="what-storage-is-the-cluster-using"></a>群集正在使用哪种存储

可使用 Ambari 检索群集的默认存储配置。 使用以下命令可使用 curl 检索 HDFS 配置信息，然后使用 [jq](https://stedolan.github.io/jq/) 进行筛选：

```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]
> 这将返回应用到服务器的第一个配置 (`service_config_version=1`)，其中包含此信息。 如果要检索创建群集后修改的值，可能需要列出配置版本并检索最新版本。

这会返回类似于以下形式的值：

* `wasbs://<container-name>@<account-name>.blob.core.windows.net`（如果使用 Azure 存储帐户）。

    帐户名称是 Azure 存储帐户的名称，而容器名称是作为群集存储器根目录的 blob 容器。

* `adl://home`（如果使用 Azure Data Lake Store）。 要获取 Data Lake Store 名称，请使用以下 REST 调用：

    ```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    这将返回以下主机名：`<data-lake-store-account-name>.azuredatalakestore.net`。

    要获取作为 HDInsight 根目录的存储中的目录，请使用以下 REST 调用：

    ```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    这会返回类似于以下内容的路径：`/clusters/<hdinsight-cluster-name>/`。

也可通过 Azure 门户按以下步骤查找存储信息：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 HDInsight 群集。

2. 在“属性”部分中，选择“存储帐户”。 将显示群集的存储信息。

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>如何从外部 HDInsight 访问文件

从 HDInsight 群集外部访问数据有多种方法。 以下是可用于处理数据的实用程序和 SDK 的几个链接：

如果使用 __Azure 存储__，请参阅以下链接，了解访问数据的方法：

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

如果使用 __Azure Data Lake Store__，请参阅以下链接，了解访问数据的方法：

* [Web 浏览器](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [针对 Visual Studio 的 Azure Data Lake 工具](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="a-namescalingascaling-your-cluster"></a><a name="scaling"></a>缩放你的群集

使用群集缩放功能可更改群集使用的数据节点数，而无需删除然后再重新创建群集。 你可以在其他作业或进程正在群集上运行时执行缩放操作。

不同的群集类型会受缩放操作影响，如下所示：

* **Hadoop**：减少群集中的节点数时，群集中的某些服务将重新启动。 这会导致正在运行或挂起的作业在缩放操作完成时失败。 你可以在操作完成后重新提交这些作业。
* **HBase**：在完成缩放操作后的几分钟内，区域服务器会自动进行平衡。 若要手动均衡区域服务器，请使用以下步骤：

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

        你还可以指定参数来替代拓扑原来提供的并行度提示。 例如，`storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` 会将拓扑重新配置为 5 个工作进程，3 个用于 BlueSpout 组件的执行程序和 10 个用于 YellowBolt 组件的执行程序。

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

某些 Hadoop 技术以自包含 jar 文件形式提供，这些文件是包含函数，用作 MapReduce 作业的一部分，或在 Pig 或 Hive 内部使用。 虽然可以使用脚本操作安装这些技术，但通常不需要任何设置，预配后即可上传到群集，并直接使用。 如需确保组件在群集重置映像后仍存在，可将 jar 文件存储在群集的默认存储（WASB 或 ADL）中。

例如，如果要使用 [DataFu](http://datafu.incubator.apache.org/) 的最新版本，可以下载包含项目的 jar，并将其上传到 HDInsight 群集。 然后按照关于如何从 Pig 或 Hive 中使用它的 DataFu 文档进行操作。

> [!IMPORTANT]
> 一些属于独立 jar 文件的组件是 HDInsight 随附的，但是不在路径中。 如果正在寻找特定组件，可以使用以下步骤在群集上进行搜索：
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> 这将返回任何匹配的 jar 文件的路径。

如果群集已提供作为独立 jar 文件的组件的版本，但是你希望使用不同的版本，则可以将新版本组件上传到群集，然后尝试在你的作业中使用它。

> [!WARNING]
> 完全支持通过 HDInsight 群集提供的组件，Microsoft 支持部门将帮助找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助你进一步排查问题。 这可能导致问题解决，或要求你参与可用的开放源代码技术渠道，在该处可找到该技术的深入专业知识。 有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)和 [http://stackoverflow.com](http://stackoverflow.com)。 此外，Apache 项目在 [http://apache.org](http://apache.org) 上提供了项目站点，例如 [Hadoop](http://hadoop.apache.org/)、[Spark](http://spark.apache.org/)。

## <a name="next-steps"></a>后续步骤

* [从基于 Windows 的 HDInsight 迁移到基于 Linux 的 HDInsight](hdinsight-migrate-from-windows-to-linux.md)
* [将 Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 作业与 HDInsight 配合使用](hdinsight-use-mapreduce.md)



<!--HONumber=Feb17_HO1-->



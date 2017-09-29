---
title: "从基于 Windows 的 HDInsight 迁移到基于 Linux 的 HDInsight - Azure | Microsoft Docs"
description: "了解如何从基于 Windows 的 HDInsight 群集迁移到基于 Linux 的 HDInsight 群集。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/12/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 4ea38d3b47ff4b50446f4ffdc3dc544fdcf938e7
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>从基于 Windows 的 HDInsight 群集迁移到基于 Linux 的群集

本文档提供 Windows 和 Linux 上 HDInsight 差异的详细信息，以及如何将现有工作负荷迁移到基于 Linux 的群集的指导。

尽管通过基于 Windows 的 HDInsight 可以轻松地在云中使用 Hadoop，但是可能还是需要迁移到基于 Linux 的群集。 例如，为了充分利用解决方案所需的基于 Linux 的工具和技术。 Hadoop 生态系统中的许多功能都是基于 Linux 的系统开发的，因此某些功能可能无法用于基于 Windows 的 HDInsight。 除此之外，许多针对 Hadoop 的书籍、视频及其他培训材料都假设使用的是 Linux 系统。

> [!NOTE]
> HDInsight 群集使用 Ubuntu 长期支持 (LTS) 作为群集中节点的操作系统。 有关可用于 HDInsight 的 Ubuntu 的版本信息，以及其他组件版本控制信息，请参阅 [HDInsight 组件版本](hdinsight-component-versioning.md)。

## <a name="migration-tasks"></a>迁移任务

以下是一般迁移工作流。

![迁移工作流示意图](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. 请阅读本文档的每个部分，以了解将现有工作流和作业等迁移到基于 Linux 的群集时，可能需要进行的更改。

2. 创建基于 Linux 的群集作为测试/质量保证环境。 有关创建基于 Linux 的群集的详细信息，请参阅 [在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-provision-linux-clusters.md)。

3. 将现有作业、数据源及接收器复制到新环境。

4. 执行验证测试，以确保作业在新群集上按预期工作。

验证一切都按预期工作后，请为迁移安排停机时间。 在停机期间，请执行以下操作：

1. 备份所有存储在本地群集节点上的暂时性数据。 例如，如果数据直接存储在头节点上。

2. 删除基于 Windows 的群集。

3. 使用与基于 Windows 的群集使用的相同默认数据存储，创建基于 Linux 的群集。 基于 Linux 的群集可根据现有生产数据继续运行。

4. 导入任何已备份的暂时性数据。

5. 使用新群集启动作业/继续处理。

### <a name="copy-data-to-the-test-environment"></a>将数据复制到测试环境

复制数据和作业的方法有很多，不过，本部分所述的两种方法是将文件直接移到测试群集的最简单方法。

#### <a name="hdfs-copy"></a>HDFS 副本

使用以下步骤将数据从生产群集复制到测试群集。 这些步骤使用 HDInsight 附带的 `hdfs dfs` 实用程序。

1. 查找现有群集的存储帐户和默认容器信息。 以下示例使用 PowerShell 来检索此信息：

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. 按照“在 HDInsight 中创建基于 Linux 的群集”文档中的步骤创建测试环境。 在创建群集之前停止，并改为选择“可选配置”。

3. 从“可选配置”边栏选项卡中，选择“链接的存储帐户”。

4. 选择“添加存储密钥”，并在出现提示时选择步骤 1 中由 PowerShell 脚本返回的存储帐户。 在每个边栏选项卡上单击“选择”。 最后，创建群集。

5. 创建群集后，使用 **SSH** 连接到该群集。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

6. 从 SSH 会话中，使用以下命令来将文件从链接的存储帐户复制到新的默认存储帐户。 将 CONTAINER 替换为 PowerShell 返回的容器信息。 将 __ACCOUNT__ 替换为帐户名称。 将数据的路径替换为数据文件的路径。

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]
    > 如果包含数据的目录结构不在测试环境中，可以使用以下命令创建它：

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    `-p` 开关可控制在路径中创建所有目录。

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>在 Azure 存储的 blob 之间直接复制

或者，可能想要使用 `Start-AzureStorageBlobCopy` Azure PowerShell cmdlet 在 HDInsight 以外的存储帐户之间复制 Blob。 有关详细信息，请参阅“Using Azure PowerShell with Azure Storage”（在 Azure 存储中使用 Azure PowerShell）一文中的“How to manage Azure Blobs”（如何管理 Azure Blob）部分。

## <a name="client-side-technologies"></a>客户端技术

诸如 [Azure PowerShell cmdlet](/powershell/azureps-cmdlets-docs)、[Azure CLI](../cli-install-nodejs.md) 或 [.NET SDK for Hadoop](https://hadoopsdk.codeplex.com/) 等客户端技术将继续处理基于 Linux 的群集。 这些技术依赖于在两种群集操作系统类型上都一致的 REST API。

## <a name="server-side-technologies"></a>服务器端技术

下表提供了迁移特定于 Windows 的服务器端组件的指南。

| 如果使用此技术... | 请执行此操作... |
| --- | --- |
| **PowerShell**（服务器端脚本，包含群集创建期间使用的脚本操作） |重新编写为 Bash 脚本。 有关脚本操作的信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)和[针对基于 Linux 的 HDInsight 的脚本操作开发](hdinsight-hadoop-script-actions-linux.md)。 |
| **Azure CLI**（服务器端脚本） |尽管 Azure CLI 可在 Linux 上使用，但它并没有预先安装在 HDInsight 群集头节点上。 有关安装 Azure CLI 的详细信息，请参阅 [Azure CLI 2.0 入门](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)。 |
| **.NET 组件** |.NET 在基于 Linux 的 HDInsight 上通过 [Mono](https://mono-project.com) 受支持。 有关详细信息，请参阅[将 .NET 解决方案迁移到基于 Linux 的 HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)。 |
| **Win32 组件或其他仅限 Windows 的技术** |指南因组件或技术而异。 可以找到与 Linux 兼容的版本，或者可能需要查找替代解决方案或重新编写此组件。 |

> [!IMPORTANT]
> HDInsight 管理 SDK 与 Mono 完全不兼容。 此时它不应用作部署到 HDInsight 群集的解决方案的一部分。

## <a name="cluster-creation"></a>群集创建

本部分提供群集创建的差异的信息。

### <a name="ssh-user"></a>SSH 用户

基于 Linux 的 HDInsight 群集使用**安全 Shell (SSH)** 协议为群集节点提供远程访问功能。 与基于 Windows 的群集的远程桌面不同，大多数 SSH 客户端不提供图形用户体验。 相反，SSH 客户端提供支持在群集上运行命令的命令行。 某些客户端（例如 [MobaXterm](http://mobaxterm.mobatek.net/)）除了提供远程命令行以外，还提供图形化文件系统浏览器。

在群集创建期间，必须提供 SSH 用户，以及**密码**或**公钥证书**进行身份验证。

我们建议使用公钥证书，因为它比密码更安全。 证书身份验证将生成已签名的公钥/私钥对，然后在创建群集时提供公钥。 使用 SSH 连接到服务器时，客户端上的私钥会为连接提供身份验证。

有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

### <a name="cluster-customization"></a>群集自定义

与基于 Linux 的群集配合使用的**脚本操作**必须以 Bash 脚本编写。 尽管脚本操作可在群集创建期间使用，它们也可以用于在基于 Linux 的群集已启动并开始运行之后进行自定义。 有关详细信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight](hdinsight-hadoop-customize-cluster-linux.md) 和[针对基于 Linux 的 HDInsight 的脚本操作开发](hdinsight-hadoop-script-actions-linux.md)。

另一个自定义功能是 **bootstrap**。 对于 Windows 群集，使用此功能可指定其他配合 Hive 使用的库的位置。 在创建群集后，这些库可自动配合 Hive 查询使用，而无需使用 `ADD JAR`。

基于 Linux 的群集的 Bootstrap 不具备此功能。 请改用[在创建群集期间添加 Hive 库](hdinsight-hadoop-add-hive-libraries.md)中所述的脚本操作。

### <a name="virtual-networks"></a>虚拟网络

基于 Windows 的 HDInsight 仅支持经典虚拟网络，而基于 Linux 的 HDInsight 则需要 Resource Manager 虚拟网络。 如果资源位于基于 Linux 的 HDInsight 群集必须连接到的经典虚拟网络中，请参阅[将经典虚拟网络连接到 Resource Manager 虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

有关将 Azure 虚拟网络与 HDInsight 配合使用时所要满足的配置要求的详细信息，请参阅[使用 Azure 虚拟网络扩展 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。

## <a name="management-and-monitoring"></a>监视和管理

与基于 Windows 的 HDInsight 配合使用的许多 Web UI（例如作业历史记录或 Yarn UI）均可通过 Ambari 使用。 此外，Ambari Hive 视图提供使用 Web 浏览器运行 Hive 查询的方法。 基于 Linux 的群集可从以下位置获得 Ambari Web UI：https://CLUSTERNAME.azurehdinsight.net。

有关使用 Ambari 的详细信息，请参阅以下文档：

* [Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari 警报

Ambari 提供能够通知群集潜在问题的警报系统。 警报以红色或黄色条目出现在 Ambari Web UI 中，也可以通过 REST API 检索警报。

> [!IMPORTANT]
> Ambari 警报表示可能有问题，而不表示已发生问题。 例如，可能会收到无法访问 HiveServer2 的警报，但实际上仍可以正常访问它。
>
> 许多警报都是针对某项服务实现为基于间隔的查询，并预期在特定的时间范围内收到响应。 因此警报本身并不代表服务已关闭，而只是单纯表示该服务没有在预期时间范围内返回结果。

应该先评估某个警报是否已长时间持续发生，或者反映了已报告的用户问题，然后对它采取措施。

## <a name="file-system-locations"></a>文件系统位置

Linux 群集文件系统的布局与基于 Windows 的 HDInsight 群集不同。 使用下表来查找常用的文件。

| 我需要查找... | 文件位于... |
| --- | --- |
| 配置 |`/etc`。 例如 `/etc/hadoop/conf/core-site.xml` |
| 日志文件 |`/var/logs` |
| Hortonworks 数据平台 (HDP) |`/usr/hdp`。此处有两个目录，一个是当前 HDP 版本，另一个是 `current`。 `current` 目录包含位于版本号目录中的文件和目录的符号链接。 由于版本号随 HDP 版本的更新而更改，因此可将 `current` 目录作为访问 HDP 文件的便利方式。 |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

一般而言，如果知道文件的名称，则可以从 SSH 会话使用以下命令来查找文件路径：

    find / -name FILENAME 2>/dev/null

也可以对文件名使用通配符。 例如，`find / -name *streaming*.jar 2>/dev/null` 返回文件名包含“streaming”的任何 Jar 文件的路径。

## <a name="hive-pig-and-mapreduce"></a>Hive、Pig 和 MapReduce

Pig 和 MapReduce 工作负荷在基于 Linux 的群集上很相似。 但是，基于 Linux 的 HDInsight 群集可使用较新版本的 Hadoop、Hive 和 Pig 创建。 这些版本差异可能改变现有解决方案的运行方式。 有关包含在 HDInsight 中的组件版本的详细信息，请参阅 [HDInsight 组件版本控制](hdinsight-component-versioning.md)）。

基于 Linux 的 HDInsight 不提供远程桌面功能。 可以改用 SSH 远程连接到群集头节点。 有关详细信息，请参阅以下文档：

* [将 Hive 与 SSH 配合使用](hdinsight-hadoop-use-hive-ssh.md)
* [将 Pig 与 SSH 配合使用](hdinsight-hadoop-use-pig-ssh.md)
* [将 MapReduce 与 SSH 配合使用](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]
> 如果使用外部 Hive 元存储，应先备份元存储，再将它与基于 Linux 的 HDInsight 配合使用。 基于 Linux 的 HDInsight 在较新版本的 Hive 中提供，但可能与由较早版本创建的元存储不兼容。

下图提供了迁移 Hive 工作负荷的指导。

| 对于基于 Windows 的群集，我使用... | 对于基于 Linux 的群集... |
| --- | --- |
| **Hive 编辑器** |[Ambari 中的 Hive 视图](hdinsight-hadoop-use-hive-ambari-view.md) |
| 运行 `set hive.execution.engine=tez;` 以启用 Tez |Tez 是基于 Linux 的群集的默认执行引擎，因此不再需要 set 语句。 |
| C# 用户定义函数 | 有关通过基于 Linux 的 HDInsight 验证 C# 组件的信息，请参阅[将 .NET 解决方案迁移到基于 Linux 的 HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| 服务器上的 CMD 文件或脚本作为 Hive 作业的一部分调用 |使用 Bash 脚本 |
| 从远程桌面运行 `hive` 命令 |使用 [Beeline](hdinsight-hadoop-use-hive-beeline.md)，或者[从 SSH 会话使用 Hive](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| 对于基于 Windows 的群集，我使用... | 对于基于 Linux 的群集... |
| --- | --- |
| C# 用户定义函数 | 有关通过基于 Linux 的 HDInsight 验证 C# 组件的信息，请参阅[将 .NET 解决方案迁移到基于 Linux 的 HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| 服务器上的 CMD 文件或脚本作为 Pig 作业的一部分调用 |使用 Bash 脚本 |

### <a name="mapreduce"></a>MapReduce

| 对于基于 Windows 的群集，我使用... | 对于基于 Linux 的群集... |
| --- | --- |
| C# 映射器和化简器组件 | 有关通过基于 Linux 的 HDInsight 验证 C# 组件的信息，请参阅[将 .NET 解决方案迁移到基于 Linux 的 HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| 服务器上的 CMD 文件或脚本作为 Hive 作业的一部分调用 |使用 Bash 脚本 |

## <a name="oozie"></a>Oozie

> [!IMPORTANT]
> 如果使用外部 Oozie 元存储，应先备份元存储，再将它与基于 Linux 的 HDInsight 配合使用。 基于 Linux 的 HDInsight 在较新版本的 Oozie 中提供，但可能与由较早版本创建的元存储不兼容。

Oozie 工作流支持 shell 操作。 shell 操作将默认 shell 用于操作系统，以运行命令行命令。 如果现有 Oozie 工作流依赖于 Windows shell，则必须重写工作流以依赖于 Linux shell 环境 (Bash)。 有关将 shell 操作与 Oozie 配合使用的详细信息，请参阅 [Oozie shell 操作扩展](http://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html)。

如果现有 Oozie 工作流依赖于通过 shell 操作调用的 C# 应用程序，则必须在 Linux 环境中验证这些应用程序。 有关详细信息，请参阅[将 .NET 解决方案迁移到基于 Linux 的 HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)。

## <a name="storm"></a>Storm

| 对于基于 Windows 的群集，我使用... | 对于基于 Linux 的群集... |
| --- | --- |
| Storm 仪表板 |Storm 仪表板不可用。 请参阅[在基于 Linux 的 HDInsight 上部署和管理 Storm 拓扑](hdinsight-storm-deploy-monitor-topology-linux.md)，了解提交拓扑的方法 |
| Storm UI |可在 https://CLUSTERNAME.azurehdinsight.net/stormui 获得 Storm UI |
| 使用 Visual Studio 创建、部署和管理 C# 或混合拓扑 |Visual Studio 可以用于创建、部署和管理在 2016 年 10 月 28 日后创建的基于 Linux 的 Storm on HDInsight 群集上的 C# (SCP.NET) 或混合拓扑。 |

## <a name="hbase"></a>HBase

在基于 Linux 的群集上，HBase 的 znode 父级为 `/hbase-unsecure`。 在使用本机 HBase Java API 的任何 Java 客户端应用程序的配置中设置此值。

有关用于设置此值的示例客户端，请参阅[构建基于 Java 的 HBase 应用程序](hdinsight-hbase-build-java-maven.md)。

## <a name="spark"></a>Spark

在预览期间，Spark 群集可用于 Windows 群集。 Spark GA 仅可用于基于 Linux 的群集。 基于 Windows 的 Spark 预览群集与基于 Linux 的 Spark 正式版群集之间没有迁移路径。

## <a name="known-issues"></a>已知问题

### <a name="azure-data-factory-custom-net-activities"></a>Azure 数据工厂自定义 .NET 活动

Azure 数据工厂自定义 .NET 活动目前不受基于 Linux 的 HDInsight 群集支持。 应改用以下方法之一，将自定义活动实现为 ADF 管道的一部分。

* 在 Azure Batch 池上执行 .NET 活动。 请参阅[在 Azure 数据工厂管道中使用自定义活动](../data-factory/transform-data-using-dotnet-custom-activity.md)的“使用 Azure Batch 链接服务”部分
* 将活动实现为 MapReduce 活动。 有关详细信息，请参阅[从数据工厂调用 MapReduce 程序](../data-factory/transform-data-using-hadoop-map-reduce.md)。

### <a name="line-endings"></a>行尾

一般情况下，基于 Windows 的系统上的行尾使用 CRLF，而基于 Linux 的系统使用 LF。 如果生成的数据带有 CRLF 行尾或者预期会出现这种行尾，可能需要修改生成器或使用方才能处理 LF 行尾。

例如，使用 Azure PowerShell 在基于 Windows 的群集上查询 HDInsight，将返回带有 CRLF 的数据。 在基于 Linux 的群集上使用相同的查询将返回 LF。 在迁移到基于 Linux 的群集之前应进行测试，以查看行尾是否会导致解决方案出现问题。

如果有直接在 Linux 群集节点上执行的脚本，应始终使用 LF 作为行结束符。 如果使用 CRLF，可能会在基于 Linux 的群集上运行脚本时遇到错误。

如果知道脚本中不存在内嵌 CR 字符的字符串，可以使用以下方法之一来批量更改行尾：

* **在上传到群集前**：在将脚本上传到群集前，使用以下 PowerShell 语句将行尾从 CRLF 更改为 LF。

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **在上传到群集后**：使用基于 Linux 的群集中 SSH 会话的以下命令修改脚本。

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>后续步骤

* [了解如何创建基于 Linux 的 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)
* [使用 SSH 连接到 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [使用 Ambari 管理基于 Linux 的群集](hdinsight-hadoop-manage-ambari.md)


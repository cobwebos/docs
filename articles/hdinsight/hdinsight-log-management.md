---
title: 管理 HDInsight 群集的日志 - Azure HDInsight | Microsoft Docs
description: 确定 HDInsight 活动日志文件的类型、大小和保留策略。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: d3ca9983eee4db09a68bf772b80c9ef841117872
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160999"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>管理 HDInsight 群集的日志

HDInsight 群集生成各种日志文件。 例如，Apache Hadoop 和相关服务（如 Apache Spark）生成详细的作业执行日志。 日志文件管理是使 HDInsight 群集保持正常状态的一部分工作。 此外，在日志存档方面可能存在相应的法规要求。  由于日志文件数量和大小方面的原因，优化日志存储和存档能够对服务成本管理带来帮助。

HDInsight 群集日志管理包括保留有关群集环境各个方面的信息。 此信息包括所有关联的 Azure 服务日志、群集配置、作业执行信息、任何错误状态，以及所需的其他数据。

HDInsight 日志管理的典型步骤如下：

* 步骤 1：确定日志保留策略
* 步骤 2：管理群集服务版本配置日志
* 步骤 3：管理群集作业执行日志文件
* 步骤 4：预测日志卷存储大小和成本
* 步骤 5：确定日志存档策略和过程

## <a name="step-1-determine-log-retention-policies"></a>步骤 1：确定日志保留策略

创建 HDInsight 群集日志管理策略的第一步是收集有关业务方案的信息以及作业执行历史记录存储要求。

### <a name="cluster-details"></a>群集详细信息

以下群集详细信息可以帮助收集日志管理策略中的信息。 从特定 Azure 帐户中创建的所有 HDInsight 群集收集此信息。

* 群集名称
* 群集区域和 Azure 可用性区域
* 群集状态，包括上次状态更改的详细信息
* 为主节点、核心节点和任务节点指定的 HDInsight 实例的类型和数量

可以使用 Azure 门户获取其中的大多数顶级信息。  或者，可以使用 Azure CLI 获取有关 HDInsight 群集的信息：

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```

也可以使用 PowerShell 查看此信息。  有关详细信息，请参阅[使用 Azure PowerShell 在 HDInsight 中管理 Hadoop 群集](hdinsight-administer-use-powershell.md)。

### <a name="understand-the-workloads-running-on-your-clusters"></a>了解群集上运行的工作负荷

必须了解 HDInsight 群集上运行的工作负荷类型才能为每种类型设计适当的日志记录策略。

* 工作负荷是试验性的（例如用于开发或测试）还是生产级的？
* 生产级工作负荷的正常运行频率如何？
* 是否有任何工作负荷是资源密集型的和/或长时间运行的？
* 是否有任何工作负荷使用一组复杂的 Hadoop 服务，需要为其生成多种类型的日志？
* 是否有任何工作负荷具有关联的法规执行沿袭要求？

### <a name="example-log-retention-patterns-and-practices"></a>示例日志保留模式和实践

* 考虑向每个日志项添加一个标识符或通过其他方法，来保持数据沿袭跟踪。 这样，便可以追溯数据和操作的起源，并通过每个阶段跟踪数据，以了解其一致性和有效性。

* 考虑如何从一个或多个群集收集日志并为其创建排序规则，以进行审核、监视、规划和警报等活动。 可能使用自定义解决方案定期访问和下载日志文件，然后对其进行合并和分析，以提供仪表板视图。 还可以添加其他功能用于发出安全警报或执行故障检测。 可以使用 PowerShell、HDInsight SDK 或可以访问 Azure 经典部署模型的代码生成这些实用工具。

* 考虑监视解决方案或服务是否能够带来好处。 Microsoft System Center 提供了 [HDInsight 管理包](https://www.microsoft.com/download/details.aspx?id=42521)。 可以使用 Chukwa 和 Ganglia 等第三方工具收集和集中处理日志。 许多公司提供了用于监视基于 Hadoop 的大数据解决方案的服务，例如 Centerity、Compuware APM、Sematext SPM 和 Zettaset Orchestrator。

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>步骤 2：管理群集服务版本和查看脚本操作日志

典型的 HDInsight 群集使用多个服务和开源软件包（例如 Apache HBase、Apache Spark 等）。 对于某些工作负荷（例如生物信息学应用），除了作业执行日志以外，可能还需要保留服务配置日志历史记录。

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>使用 Ambari UI 查看群集配置设置

Apache Ambari 提供 Web UI 和 REST API 来简化 HDInsight 群集的管理、配置和监视。 基于 Linux 的 HDInsight 群集上已随附 Ambari。 在 Azure 门户的“HDInsight”页上选择“群集仪表板”窗格，打开“群集仪表板”链接页。  接下来，选择“HDInsight 群集仪表板”窗格打开 Ambari UI。  系统会提示输入群集登录凭据。

若要打开服务视图列表，请在 Azure 门户页上选择 HDInsight 对应的“Ambari 视图”窗格。  此列表的内容根据安装的库而异。  例如，可能会显示“YARN 队列管理器”、“Hive 视图”和“Tez 视图”。  选择任一服务链接以查看配置和服务信息。  Ambari UI 中的“堆栈和版本”页提供有关群集服务配置和服务版本历史记录的信息。 若要导航到 Ambari UI 的此部分，请选择“管理”菜单，然后选择“堆栈和版本”。  选择“版本”选项卡查看服务版本信息。

![堆栈和版本](./media/hdinsight-log-management/stack-versions.png)

使用 Ambari UI 可以下载群集中特定主机（或节点）上运行的任一（或所有）服务的配置。  选择“主机”菜单，然后选择所需主机的链接。 在该主机的页面上，依次选择“主机操作”按钮和“下载客户端配置”。 

![主机客户端配置](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>查看脚本操作日志

使用 HDInsight [脚本操作](hdinsight-hadoop-customize-cluster-linux.md)可以手动或者根据指定在群集上运行脚本。 例如，可以使用脚本操作在群集上安装其他软件，或者更改配置设置的默认值。 在脚本操作日志中可以深入了解设置群集过程中发生的错误，以及可能影响群集性能和可用性的配置设置更改。  若要查看脚本操作的状态，请在 Ambari UI 中选择“操作”按钮，或访问默认存储帐户中的状态日志。 存储日志位于 `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`。

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>步骤 3：管理群集作业执行日志文件

下一步是查看各种服务的作业执行日志文件。  服务可能包括 Apache HBase、Apache Spark 等等。 Hadoop 群集生成大量的详细日志，因此，确定有用（以及无用）的日志可能很耗时。  了解日志记录系统对于有针对性的日志文件管理非常重要。  下面是一个示例日志文件。

![HDInsight 日志文件示例](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>访问 Hadoop 日志文件

HDInsight 将其日志文件同时存储在群集文件系统和 Azure 存储中。 若要检查群集中的日志文件，可与群集建立 SSH 连接并浏览文件系统，或者在远程头节点服务器上使用 Hadoop YARN 状态门户。 使用可以访问和下载 Azure 存储中的数据的任何工具，即可检查 Azure 存储中的日志文件。 这些工具包括 AZCopy、CloudXplorer 和 Visual Studio 服务器资源管理器。 此外，可以使用 PowerShell 和 Azure 存储客户端库或 Azure.NET SDK 访问 Azure Blob 存储中的数据。

Hadoop 在群集中的各个节点上以“任务尝试”的形式运行作业。 HDInsight 可以发起推理任务尝试，并终止一开始就无法完成的其他任何任务尝试。 这会即时生成大量的活动并将其记录到控制器、stderr 和 syslog 日志文件。 此外，多个任务尝试会同时运行，但日志文件只能以线性方式显示结果。

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>写入 Azure Blob 存储的 HDInsight 日志

对于使用 Azure PowerShell cmdlet 或 .NET 作业提交 API 提交的任何作业，HDInsight 群集配置为将任务日志写入 Azure Blob 存储帐户。  如果通过群集的 SSH 连接提交作业，则会将执行日志记录信息存储在上一部分所述的 Azure 表中。

除了 HDInsight 生成的核心日志文件以外，安装的服务（例如 YARN）也会生成作业执行日志文件。  日志文件的类型和数量取决于安装的服务。  常见的服务包括 Apache HBase、Apache Spark ，等等。  调查每个服务的作业日志执行文件可以了解群集上提供的各种日志记录文件。  每个服务使用自身独特的方法进行日志记录，并为日志文件提供独特的存储位置。  下一部分通过一个示例详细介绍了如何访问最常见的服务日志文件（通过 YARN）。

### <a name="hdinsight-logs-generated-by-yarn"></a>YARN 生成的 HDInsight 日志

YARN 聚合工作节点上所有容器的日志，并将每个工作节点的这些日志存储为一个聚合日志文件。 应用程序完成运行后，该日志将存储在默认文件系统中。 应用程序可能使用数百或数千个容器，但在单个工作节点上运行的所有容器的日志始终聚合成单个文件。 因此，在每个工作节点上，应用程序只使用一个日志。 在 HDInsight 群集版本 3.0 和更高版本上，日志聚合默认已启用。 聚合日志位于群集的默认存储中。

```
    /app-logs/<user>/logs/<applicationId>
```

无法直接读取聚合日志，因为它们是以容器编制索引的 TFile 二进制格式编写的。 使用 YARN ResourceManager 日志或 CLI 工具以纯文本的形式查看感兴趣的应用程序或容器的日志。

#### <a name="yarn-cli-tools"></a>YARN CLI 工具

若要使用 YARN CLI 工具，则必须首先使用 SSH 连接到 HDInsight 群集。 运行这些命令时，请指定 `<applicationId>`、`<user-who-started-the-application>`、`<containerId>` 和 `<worker-node-address>` 信息。 可使用以下命令之一以纯文本格式查看日志：

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI 在群集头节点上运行，可通过 Ambari Web UI 访问它。 使用以下步骤查看 YARN 日志：

1. 在 Web 浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net`。 将 CLUSTERNAME 替换为 HDInsight 群集的名称。
2. 在左侧的服务列表中选择“YARN”。
3. 在“快速链接”下拉列表中选择一个群集头节点，然后选择“ResourceManager 日志”。 将显示 YARN 日志的链接列表。

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>步骤 4：预测日志卷存储大小和成本

完成前面的步骤后，便了解了 HDInsight 群集生成的日志文件的类型和数量。

接下来，请分析一段时间内关键日志存储位置中的日志数据量。 例如，可以分析 30-60-90 天内的数据量和增长率。  在电子表格中或使用其他工具（例如 Visual Studio、Azure 存储资源管理器或 Power Query for Excel）记录此信息。 有关详细信息，请参阅[分析 HDInsight 日志](hdinsight-debug-jobs.md)。  

现在，我们已获得足够的信息来为关键日志创建日志管理策略。  使用电子表格（或所选的工具）预测日志大小增长率，以及后续的 Azure 服务日志存储费用。  另请考虑所要检查的日志集的任何日志保留要求。  确定可以删除哪些日志文件（如果有）和应该保留和存档哪些日志后，可以重新预测将来的日志存储成本，以降低昂贵的 Azure 存储费用。

## <a name="step-5-determine-log-archive-policies-and-processes"></a>步骤 5：确定日志存档策略和过程

确定可以删除哪些日志文件后，可以调整许多 Hadoop 服务上的日志记录参数，以便在指定的时间段后自动删除日志文件。

对于某些日志文件，可以使用价格较低的日志文件存档方法。 对于 Azure 资源管理器活动日志，可以浏览使用 Azure 门户来探索此方法。  在 Azure 门户中选择 HDInsight 实例对应的“活动日志”链接，设置 ARM 日志的存档。  在“活动日志”搜索页面顶部，选择“导出”菜单项打开“导出活动日志”窗格。  填写订阅、区域、是否导出到存储帐户，以及日志的保留天数。 在同一窗格中，还可以指定是否导出到事件中心。 

![导出日志文件](./media/hdinsight-log-management/archive.png)

或者，可以使用 PowerShell 编写日志存档的脚本。  有关示例 PowerShell 脚本，请参阅[将 Azure 自动化日志存档到 Azure Blob 存储](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8)。

### <a name="accessing-azure-storage-metrics"></a>访问 Azure 存储指标

可将 Azure 存储配置为记录存储操作和访问。 可以使用这些非常详细的日志进行容量监视和规划，以及审核存储请求。 记录的信息包括延迟详细信息，用于监视和微调解决方案的性能。
可以使用用于 Hadoop 的 .NET SDK 检查针对保存 HDInsight 群集数据的 Azure 存储生成的日志文件。

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>控制旧日志文件的备份索引大小和数量

若要控制保留的日志文件的大小和数量，请设置 `RollingFileAppender` 的以下属性：

* `maxFileSize` 是文件的临界大小，超过该大小会轮换该文件。 默认值为 10 MB。
* `maxBackupIndex` 指定要创建的备份文件数，默认值为 1。

### <a name="other-log-management-techniques"></a>其他日志管理方法

为了避免磁盘空间不足，可以使用一些 OS 工具（例如 `logrotate`）来管理日志文件的处理。 可将 `logrotate` 配置为每日运行，压缩日志文件和删除旧文件。 所用的方法取决于不同的要求，例如，要在本地节点上保留日志文件多久。 

还可以检查是否为一个或多个服务启用了调试日志记录，这会明显增大输出日志的大小。 

若要将所有节点中的日志收集到一个中心位置，可以创建数据流，例如，将所有日志项引入 Solr。

## <a name="next-steps"></a>后续步骤

* [适用于 HDInsight 的监视和日志记录做法](https://msdn.microsoft.com/library/dn749790.aspx)
* [在基于 Linux 的 HDInsight 上访问 YARN 应用程序日志](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [如何控制各种 Hadoop 组件的日志文件大小](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)

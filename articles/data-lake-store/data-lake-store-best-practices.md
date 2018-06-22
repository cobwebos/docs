---
title: 使用 Azure Data Lake Store 的最佳做法 | Microsoft Docs
description: 了解与 Azure Data Lake Store 使用相关的数据引入、日期安全性和性能的最佳做法
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: sachins
ms.openlocfilehash: 9fd6b72a7d09f85f7a6e60e5af4035ffc3862d2c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625332"
---
# <a name="best-practices-for-using-azure-data-lake-store"></a>使用 Azure Data Lake Store 的最佳做法
本文介绍 Azure Data Lake Store 使用方面的最佳做法和注意事项。 本文介绍 Data Lake Store 的安全性、性能、复原和监视。 在 Data Lake Store 出现之前，在 Azure HDInsight 之类的服务中使用真正大型的数据是很复杂的事情。 必须将数据在多个 Blob 存储帐户中分片，才能实现 PB 级的存储以及在该规模下的性能优化。 使用 Data Lake Store 时，大部分针对大小和性能的硬性限制都会去除。 但是，若要充分利用 Data Lake Store 的性能，仍有一些需要本文讨论的注意事项。 

## <a name="security-considerations"></a>安全注意事项

Azure Data Lake Store 为 Azure Active Directory (Azure AD) 用户、组和服务主体提供 POSIX 访问控制和详细的审核。 可以对现有文件和文件夹设置这些访问控制。 也可使用访问控制来创建可以应用到新文件或文件夹的默认设置。 设置针对现有文件夹和子对象的权限时，需在每个对象上以递归方式传播这些权限。 如果有大量的文件，传播这些权限可能需要很长时间。 所需时间取决于传播速度，而传播速度则在每秒 30-50 个处理对象这一范围内。 因此，请对文件夹结构和用户组进行相应的规划。 否则，在使用数据时，可能导致意外的延迟和问题。 

假设文件夹有 100,000 个子对象。 以每秒处理 30 个对象的速度下限为例，更新整个文件夹的权限可能需要一小时。 有关 Data Lake Store ACL 的更多详细信息，请参阅 [Azure Data Lake Store 中的访问控制](data-lake-store-access-control.md)。 若要改进以递归方式分配 ACL 的性能，可以使用 Azure Data Lake 命令行工具。 此工具可以创建多个线程和递归导航逻辑，快速地向数百万文件应用 ACL。 此工具适用于 Linux 和 Windows，其[文档](https://github.com/Azure/data-lake-adlstool)和[下载包](http://aka.ms/adlstool-download)在 GitHub 上提供。 可以通过以 Data Lake Store [.NET](data-lake-store-data-operations-net-sdk.md) 和 [Java](data-lake-store-get-started-java-sdk.md) SDK 编写的自己的工具来实现相同的性能改进。

### <a name="use-security-groups-versus-individual-users"></a>安全组和单个用户的使用比较 

在 Data Lake Store 中处理大数据时，大多数情况下会使用服务主体，以便使用 Azure HDInsight 之类的服务来处理数据。 但是，在某些情况下，单个用户也需要访问数据。 在这种情况下，必须使用 Azure Active Directory [安全组](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory)，而不是将单个用户分配给文件夹和文件。 

为安全组分配权限以后，在组中添加或删除用户就不需要对 Data Lake Store 进行任何更新。 这还有助于确保不超出 [32 访问和默认 ACL](../azure-subscription-service-limits.md#data-lake-store-limits)（这包括始终与每个文件和文件夹关联的四个 POSIX 样式的 ACL：[负责人用户](data-lake-store-access-control.md#the-owning-user)、[负责人组](data-lake-store-access-control.md#the-owning-group)、[掩码](data-lake-store-access-control.md#the-mask-and-effective-permissions)和其他）。

### <a name="security-for-groups"></a>组的安全性 

如上所述，当用户需要访问 Data Lake Store 时，最好是使用 Azure Active Directory 安全组。 一开始时，建议对帐户的根目录使用 **ReadOnlyUsers**、**WriteAccessUsers** 和 **FullAccessUsers** 组，甚至可以对关键的子文件夹使用单独的安全组。 如果还有其他预期会在以后添加但目前尚未确定的用户组，可以考虑创建能够访问特定文件夹的虚拟安全组。 使用安全组可确保在以后不需很长的处理时间就可以将新权限分配给数千文件。 

### <a name="security-for-service-principals"></a>服务主体的安全性 

Azure Active Directory 服务主体通常可供 Azure HDInsight 之类的服务用来访问 Data Lake Store 中的数据。 可能需要根据多个工作负荷的访问要求进行一些考虑，确保组织内外的安全性。 对许多客户来说，单个 Azure Active Directory 服务主体可能已经足够，该主体可以拥有 Data Lake Store 根目录的完全权限。 其他客户可能需要多个包含不同服务主体的群集，让一个群集拥有数据的完全访问权限，另一个群集拥有只读访问权限。 至于安全组，可以考虑在创建 Data Lake Store 帐户以后，针对每个预期的方案创建具有读、写或完全访问权限的服务主体。 

### <a name="enable-the-data-lake-store-firewall-with-azure-service-access"></a>启用 Data Lake Store 防火墙，允许 Azure 服务访问 

Data Lake Store 支持启用防火墙并仅限 Azure 服务进行访问的选项。如果需要减少来自外部入侵者的攻击途径，建议使用这一选项。 可以通过“防火墙” > “启用防火墙(启用)” > “允许 Azure 服务访问”选项在 Azure 门户的 Data Lake Store 帐户上启用防火墙。  

![Data Lake Store 中的防火墙设置](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Data Lake Store 中的防火墙设置")

启用防火墙以后，只有 Azure 服务（例如 HDInsight、数据工厂、SQL 数据仓库等）能够访问 Data Lake Store。 由于 Azure 使用内部网络地址转换，因此 Data Lake Store 防火墙不支持通过 IP 限制特定的服务，只能限制 Azure 外部的终结点，例如本地终结点。 

## <a name="performance-and-scale-considerations"></a>性能和规模注意事项

Data Lake Store 最强大的一项功能是去除了对数据吞吐量的硬性限制。 去除该限制后，客户就可以提高其数据大小和相应的性能要求，不需将数据分片。 关于如何优化 Data Lake Store 性能，最重要的一项考量是：是否在启用并行处理的情况下性能最佳。

### <a name="improve-throughput-with-parallelism"></a>通过并行处理提高吞吐量 

考虑为每个核心提供 8-12 个线程，以实现读/写吞吐量的最优化。 这是由于阻止了单个线程上的读/写，而线程增多可以提高 VM 上的并发度。 请务必监视 VM 的 CPU 使用率，确保这些指标正常，可以提高并行度。   

### <a name="avoid-small-file-sizes"></a>避免文件过小

在 Data Lake Store 中启用 POSIX 权限和审核会带来开销，该开销在处理大量小文件时会变得很明显。 按照最佳做法，必须将数据批量导入到较大的文件中，而不是将数千或数百万小文件写入 Data Lake Store。 避免使用小文件可以有多项好处，例如：

* 减少对多个文件进行身份验证检查的次数
* 减少打开的文件连接数
* 加快复制速度
* 减少更新 Data Lake Store POSIX 权限时需要处理的文件数 

具体取决于哪些服务和工作负载正在使用数据，文件的大小最好为 256 MB 或更大。 如果文件无法在放置到 Data Lake Store 中时合并，可以通过单独的压缩作业将这些文件组合成较大的文件。 有关 Data Lake Store 中文件大小和数据组织的详细信息和建议，请参阅[调整数据集结构](data-lake-store-performance-tuning-guidance.md#structure-your-data-set)。

### <a name="large-file-sizes-and-potential-performance-impact"></a>大文件及其可能的性能影响

虽然 Data Lake Store 支持大文件（最大可以达 PB 级），但考虑到性能优化以及数据读取过程，最好不要让文件的平均大小超出 2 GB。 例如，使用 **Distcp** 在不同位置或存储帐户之间复制数据时，会根据文件（最小粒度级别）来确定映射任务。 因此，如果复制 10 个文件，每个文件的大小为 1 TB，则最多可以分配 10 个映射器。 另外，如果有许多分配了映射器的文件，这些映射器一开始会通过并行工作来移动大文件。 但是，随着作业的进行，处于已分配状态的映射器越来越少，最终出现只有一个映射器分配到一个大文件的情况。 Microsoft 已提交了对 Distcp 的改进，将在未来的 Hadoop 版本中解决此问题。  

另一个需要考虑的示例是在将 Azure Data Lake Analytics 与 Data Lake Store 配合使用时出现的情况。 某些不能拆分的文件（例如 XML 和 JSON 文件）在超过 2 GB 时，其性能会受影响，具体取决于提取程序所进行的处理。 如果文件（例如 CSV 文件）可以通过提取程序来拆分，则首选大型文件。

### <a name="capacity-plan-for-your-workload"></a>针对工作负荷的容量计划 

Azure Data Lake Store 去除了对 Blob 存储帐户的硬性 IO 限制。 但是，仍然存在需要考虑的软性限制。 默认的入口/出口限制符合大多数方案的需求。 如果你的工作负荷需要提高这些限制，请与 Microsoft 支持部门联系。 另外，请在概念证明阶段查看这些限制，确保不会在生产阶段达到 IO 限制。 如果达到了限制，则可能需要等待 Microsoft 工程团队手动提高限制。 如果出现 IO 限制，Azure Data Lake Store 会返回错误代码 429，此时最好应使用适当的指数回退策略重试。 

### <a name="optimize-writes-with-the-data-lake-store-driver-buffer"></a>通过 Data Lake Store 驱动程序缓冲区优化“写入” 

为了优化从 Hadoop 写入到 Data Lake Store 时的性能并降低 IOPS，请在执行写入操作时，尽可能让其大小接近 Data Lake Store 驱动程序缓冲区大小。 在刷新之前，例如在使用 Apache Storm 或 Spark 流式处理工作负荷进行流式处理时，尽量不要超出缓冲区大小。 从 HDInsight/Hadoop 写入到 Data Lake Store 时，必须确保 Data Lake Store 的驱动程序有一个 4 MB 的缓冲区。 与许多文件系统驱动程序一样，此缓冲区可以在达到 4 MB 大小之前手动刷新。 否则，它会在下一个写入超过缓冲区的最大大小的情况下立即将内容刷新到存储中。 在可能的情况下，在按计数或时间窗口同步/刷新策略时，必须避免缓冲区的资料溢出或严重不足。

## <a name="resiliency-considerations"></a>复原注意事项 

通过 Data Lake Store 或任何云服务构建系统时，必须考虑到可用性要求以及如何响应可能出现的服务中断情况。 问题可能局限于特定的实例，也可能是区域性的，因此必须对二者都有计划。 可以选择较激进或不太激进的策略来确保高可用性和灾难恢复，具体取决于工作负荷的**恢复时间目标**和**恢复点目标** SLA。

### <a name="high-availability-and-disaster-recovery"></a>高可用性和灾难恢复 

高可用性 (HA) 和灾难恢复 (DR) 有时可以组合在一起，虽然每个的策略稍有不同，尤其是在涉及到数据的时候。 Data Lake Store 已经可以在后台处理 3 个副本形式的复制，应对局部硬件故障。 不过，由于跨区域复制不是内置的功能，必须自行管理这种情况。 在制定计划以确保 HA 时，应考虑到在发生服务中断的情况下，工作负荷需尽快切换到在本地或新区域中单独复制的实例，以便访问最新数据。  

在 DR 策略中，为了因应某个区域发生灾难性故障这种不太可能的事件，必须将数据复制到其他区域，这也很重要。 该数据在一开始可能与已复制的 HA 数据相同。 但是，还必须考虑到在出现数据损坏这样的极端例子时的要求，因此需要创建可供回退的定期快照。 根据数据的重要性和大小，可以考虑每隔 1 小时、6 小时、24 小时在本地存储和/或辅助存储中创建滚动性的增量快照，具体取决于风险承受能力。 

考虑到 Data Lake Store 的数据复原能力，建议将数据异地复制到某个单独的区域，复制频率取决于 HA/DR 要求，最好是每小时一次。 这种频率的复制可以将大规模数据移动的次数降至最低，从而缓解与主系统之间对吞吐量的竞争，改进恢复点目标 (RPO)。 另外，还应考虑通过各种方式让使用 Data Lake Store 的应用程序能够自动故障转移到辅助帐户：可以监视触发器或失败尝试的时长，或者至少应向管理员发送通知，让其进行人工干预。 请注意，是进行故障转移，还是等待服务重新联机？这需要进行慎重的权衡。 如果数据尚未完成复制，则故障转移可能导致数据丢失、不一致或情况复杂的合并。 

下面是最适合在 Data Lake Store 帐户之间协调复制操作的三个建议选项，以及这些选项之间的主要差异。


|  |Distcp  |Azure 数据工厂  |AdlCopy  |
|---------|---------|---------|---------|
|**规模限制**     | 受辅助角色节点数限制        | 受最大云数据移动单位数限制        | 受分析单位数限制        |
|**支持复制增量数据**     |   是      | 否         | 否         |
|**内置业务流程**     |  否（使用 Oozie Airflow 或 cron 作业）       | 是        | 否（使用 Azure 自动化或 Windows 任务计划程序）         |
|**支持的文件系统**     | ADL、HDFS、WASB、S3、GS、CFS        |很多，请参阅[连接器](../data-factory/connector-azure-blob-storage.md)。         | ADL 到 ADL、WASB 到 ADL（仅限同一区域）        |
|**OS 支持**     |任何运行 Hadoop 的 OS         | 不适用          | Windows 10         |
   

### <a name="use-distcp-for-data-movement-between-two-locations"></a>使用 Distcp 在两个位置之间进行数据移动 

Distcp 是 distributed copy（分布式复制）的简称，是 Hadoop 随附的一项 Linux 命令行工具，适用于在两个位置之间进行分布式数据移动。 这两个位置可以是 Data Lake Store、HDFS、WASB 或 S3。 此工具使用 Hadoop 群集（例如 HDInsight）上的 MapReduce 作业在所有节点上进行横向扩展。 Distcp 被认为是在没有特殊网络压缩设备的情况下移动大数据的最快方式。 Distcp 还提供了在两个位置之间仅更新增量数据的选项，可以处理自动重试，并且可以对计算进行动态缩放。 如果需要复制 Hive/Spark 表之类的内容（在单个目录中有许多大型文件），而且只需要对修改的数据进行复制，则此方法相当有效。 由于这些原因，在大数据存储之间复制数据时，最建议使用的工具是 Distcp。 

复制作业可以通过使用频率或数据触发器的 Apache Oozie 工作流触发，也可以通过 Linux cron 作业触发。 对于密集型复制作业，建议启动一个单独的 HDInsight Hadoop 群集，该群集可以专门针对复制作业进行调整和缩放。 这样可确保复制作业不会干扰关键作业。 如果运行复制的频率足够宽，甚至可以在每次作业之间关闭群集。 如果故障转移到次要区域，请确保在次要区域也启动另一群集，以便在主要的 Data Lake Store 帐户恢复后将新数据复制回该帐户。 有关如何使用 Distcp 的示例，请参阅[使用 Distcp 在 Azure 存储 Blob 和 Data Lake Store 之间复制数据](data-lake-store-copy-data-wasb-distcp.md)。

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>使用 Azure 数据工厂来计划复制作业 

Azure 数据工厂还可以用来通过**复制活动**对复制作业进行计划，甚至可以通过**复制向导**设置一个频率。 请记住，Azure 数据工厂的云数据移动单位 (DMU) 有限，因此最终会对大数据工作负荷的吞吐量/计算进行限制。 另外，Azure 数据工厂目前不提供在 Data Lake Store 帐户之间进行增量更新的功能，因此 Hive 表之类的文件夹需要获得完整的副本才能进行复制。 请参阅[复制活动优化指南](../data-factory/v1/data-factory-copy-activity-performance.md)，详细了解如何使用数据工厂进行复制。 

### <a name="adlcopy"></a>AdlCopy

AdlCopy 是一项 Windows 命令行工具，用于在两个 Data Lake Store 帐户之间复制数据，但仅限同一区域。 AdlCopy 工具提供了单独的运行复制作业的选项，也提供了使用 Azure Data Lake Analytics 帐户进行该操作的选项。 虽然此工具原本是为按需复制构建的，不是一种强力的复制工具，但在需要在同一区域的不同 Data Lake Store 帐户之间进行分布式复制的情况下，也不失为一种可选的工具。 建议对生产型工作负荷使用高级 Data Lake Analytics 选项，以确保可靠性。 独立版本可能会返回“忙”响应，其缩放和监视功能有限。 

与 Distcp 一样，AdlCopy 需要通过 Azure 自动化或 Windows 任务计划程序之类的工具进行协调。 AdlCopy 不允许只复制更新的文件，但允许反复复制和覆盖现有的文件，就像使用数据工厂一样。 有关如何使用 AdlCopy 的详细信息和示例，请参阅[从 Azure 存储 Blob 复制数据到 Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)。

## <a name="monitoring-considerations"></a>监视注意事项 

Data Lake Store 提供详细的诊断日志和审核。 Data Lake Store 提供了一些基本的指标。这些指标可以在 Azure 门户的 Data Lake Store 帐户中使用，也可以在 Azure Monitor 中使用。 Data Lake Store 的可用性显示在 Azure 门户中。 但是，此指标每七分钟刷新一次，不能通过公开 API 进行查询。 若要获取 Data Lake Store 帐户的最新可用性，必须运行你自己的综合性测试来验证可用性。 其他指标（例如总存储使用率、读/写请求数、入口/出口）可能需要长达 24 小时的时间进行刷新。 因此，其他更新指标必须通过 Hadoop 命令行工具手动进行计算，或者通过聚合日志信息来计算。 若要获取最新的存储使用率，最快速的方法是从 Hadoop 群集节点（例如，头节点）运行以下 HDFS 命令：   

    hdfs dfs -du -s -h adl://<adls_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-store-diagnostics"></a>导出 Data Lake Store 诊断 

若要从 Data Lake Store 访问可搜索的日志，最快速的一个方法是在 Data Lake Store 帐户的“诊断”边栏选项卡下启用到 **Log Analytics** 的日志传送。 这样就可以使用时间和内容筛选器立即访问传入日志，并且可以使用在 15 分钟时间间隔内触发的警报选项（电子邮件/Webhook）。 有关说明，请参阅[访问 Azure Data Lake Store 的诊断日志](data-lake-store-diagnostic-logs.md)。 

如需日志放置位置的更多实时警报并对放置位置进行更多的控制，可以考虑将日志导出到 Azure EventHub，以便对内容单独进行分析或按时间窗口进行分析，这样就可以向队列提交实时通知。 然后可以通过单独的应用程序（例如[逻辑应用](../connectors/connectors-create-api-azure-event-hubs.md)）使用这些警报并将其传送到适当的通道，以及向监视工具（例如 NewRelic、Datadog 或 AppDynamics）提交指标。 或者，如果使用的是 ElasticSearch 之类的第三方工具，则可将日志导出到 Blob 存储，然后通过 [Azure Logstash 插件](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)将数据用到 Elasticsearch、Kibana 和 Logstash (ELK) 堆栈中。

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>在 HDInsight 中启用调试级日志记录 

如果 Data Lake Store 日志传送未启用，Azure HDInsight 还会提供一种启用 [Data Lake Store 客户端日志记录](data-lake-store-performance-tuning-mapreduce.md)的方式，即 log4j。 必须在“Ambari” > “YARN” > “配置” > “高级 yarn-log4j 配置”中设置以下属性： 

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG 

设置属性且节点重启后，Data Lake Store 诊断就会写入到节点上的 YARN 日志 (/tmp/<user>/yarn.log)，然后就可以监视各种重要的详细信息，例如错误或限制（HTTP 429 错误代码）。 也可在 [Data Lake Store ](data-lake-store-diagnostic-logs.md)帐户的“诊断”边栏选项卡的 Log Analytics（或日志传送到的任何位置）中监视这些相同的信息。 若要启用操作可见性并方便调试，建议至少启用 Data Lake Store 的客户端日志记录或利用其日志传送选项。

### <a name="run-synthetic-transactions"></a>运行综合事务 

目前，Data Lake Store 在 Azure 门户中的服务可用性指标每 7 分钟会刷新一次。 另外，该指标不能使用公开 API 进行查询。 因此，建议生成一个可以对 Data Lake Store 执行综合事务的基本应用程序，以便获取最新可用性。 例如，可以创建 WebJob、逻辑应用或 Azure Function 应用，对 Data Lake Store 执行读取、创建和更新操作，然后将结果发送到监视解决方案。 这些操作可以在临时文件夹中执行，在测试完成后删除，该测试按要求每 30-60 秒运行一次。

## <a name="directory-layout-considerations"></a>目录布局注意事项

将数据放置到 Data Lake 中时，必须对数据结构进行预先规划，以便有效地利用安全、分区和处理方面的措施。 下述许多建议都可以使用，不管是适用于 Azure Data Lake Store、Blob 存储还是 HDFS。 每个工作负荷对数据使用方式的要求各不相同，不过下面是一些在使用 IoT 和批处理方案时值得考虑的常见布局。

### <a name="iot-structure"></a>IoT 结构 

在 IoT 工作负荷的数据存储中，可能会放置大量来自多个产品、设备、组织和客户的数据。 必须预先规划目录布局，以便对下游使用者的数据进行组织，确保其安全性并提高处理效率。 考虑使用的常规模板可能是以下布局： 

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/ 

例如，在英国，飞机引擎的着陆遥测数据可能看起来像以下结构： 

    UK/Planes/BA1293/Engine1/2017/08/11/12/ 

将日期置于文件夹结构的末尾有一个重要原因。 如果需要将特定区域或主题限定给特定用户/组访问，可以使用 POSIX 权限轻松完成该操作。 否则，在只能允许特定的安全组查看英国的数据或特定飞机的情况下，如果将日期结构置于前面，则需要针对每个小时文件夹下的很多文件夹设置单独的权限。 另外，如果将日期结构前置，则随着时间的推移，文件夹的数目会呈指数增长。

### <a name="batch-jobs-structure"></a>批处理作业结构 

概括地讲，在批处理中，常用的方法是先将数据置于“in”文件夹中。 然后，当数据处理完以后，再将新数据置于“out”文件夹中，供下游进程使用。 有的作业需要对单个文件进行处理，但可能不需要对大型数据集进行大规模并行处理。对于这种作业，有时候会使用此目录结构。 与上面建议的 IoT 结构一样，好的目录结构会设置父级文件夹，用于存储区域和主题之类的内容（例如，组织、产品/制造者）。 此结构有助于确保组织数据的安全，并可更好地管理工作负荷中的数据。 另外，可以考虑在结构中添加日期和时间，以便更好地在处理过程中进行组织和筛选式搜索，增强安全性并提高自动化程度。 日期结构的粒度级别取决于上传或处理数据的时间间隔，例如每小时、每天甚至每月。 

有时候，数据损坏或格式异常会导致文件处理失败。 在这种情况下，可以将这些文件移到 **/bad** 文件夹中进一步进行检查，这样的目录结构更有效。 还可以通过批处理作业将这些损坏的文件报告或通知给相关人员，要求其进行人工干预。 请考虑以下模板结构： 

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/ 

例如，一家市场营销公司每天从其北美的客户端提取客户更新数据。 该数据在处理前和处理后可能看起来像以下片段： 

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv 
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv 
 
在最常见的情况下，批量数据在处理后会直接进入 Hive 之类的数据库或传统的 SQL 数据库，不需要 **/in** 或 **/out** 文件夹，因为输出已经进入一个适用于 Hive 表或外部数据库的单独文件夹中。 例如，每日从客户处提取的数据会置于各自的文件夹中。在经过 Azure 数据工厂、Apache Oozie 或 Apache Airflow 之类工具的协调后，会触发一个每日 Hive 作业或 Spark 作业来处理数据，然后将数据写入 Hive 表中。

## <a name="next-steps"></a>后续步骤     

* [Overview of Azure Data Lake Store](data-lake-store-overview.md) 
* [Azure Data Lake Store 中的访问控制](data-lake-store-access-control.md) 
* [Azure Data Lake Store 中的安全](data-lake-store-security-overview.md)
* [优化 Azure Data Lake Store 性能](data-lake-store-performance-tuning-guidance.md)
* [将 HDInsight Spark 与 Azure Data Lake Store 配合使用的性能优化指南](data-lake-store-performance-tuning-spark.md)
* [将 HDInsight Hive 与 Azure Data Lake Store 配合使用的性能优化指南](data-lake-store-performance-tuning-hive.md)
* [Data Orchestration using Azure Data Factory for Azure Data Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka)
* [创建包含 Data Lake Store 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md) 

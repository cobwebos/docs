---
title: 使用 Azure Data Lake Storage Gen2 的最佳做法 | Microsoft Docs
description: 了解与使用 Azure Data Lake Storage Gen2（以前称为 Azure Data Lake Store）相关的数据引入、日期安全和性能的最佳做法
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 79c4f051318113ebe0c7e0085539d2f24405b4f9
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857887"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>使用 Azure Data Lake Storage Gen2 的最佳做法

在本文中，你将了解 Azure Data Lake Storage Gen2 使用方面的最佳做法和注意事项。 本文介绍 Data Lake Storage Gen2 的安全性、性能、复原能力和监视。 在 Data Lake Storage Gen2 出现之前，在 Azure HDInsight 之类的服务中使用真正大型的数据是很复杂的事情。 必须将数据在多个 Blob 存储帐户中分片，才能实现 PB 级的存储以及在该规模下的性能优化。 Data Lake Storage Gen2 支持高达 5TB 的单个文件大小，并且对性能的大多数硬限制都已消除。 但是，若要充分利用 Data Lake Storage Gen2 的性能，仍有一些需要本文讨论的注意事项。

## <a name="security-considerations"></a>安全注意事项

Azure Data Lake Storage Gen2 为 Azure Active Directory (Azure AD) 用户、组和服务主体提供 POSIX 访问控制。 可以对现有文件和目录设置这些访问控制。 也可使用访问控制来创建可自动应用于新文件或目录的默认权限。 有关 Data Lake Storage Gen2 ACL 的更多详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](storage-data-lake-storage-access-control.md)。

### <a name="use-security-groups-versus-individual-users"></a>安全组和单个用户的使用比较

在 Data Lake Storage Gen2 中处理大数据时，可能会使用服务主体，以便使用 Azure HDInsight 之类的服务来处理数据。 但是，在某些情况下，单个用户也需要访问数据。 无论在什么情况下，都务必考虑使用 Azure Active Directory [安全组](../common/storage-auth-aad.md)，而不是将单个用户分配给目录和文件。

为安全组分配权限以后，在组中添加或删除用户就不需要对 Data Lake Storage Gen2 进行任何更新。 这也有助于确保不超出每个访问控制列表 (ACL) 的最大访问控制条目数。 目前，该数目为 32（包括始终与每个文件和目录关联的四个 POSIX 样式的 ACL）：拥有用户、拥有组、掩码和其他。 每个目录可以有两种类型的 ACL（访问 ACL 和默认 ACL），总共 64 个访问控制条目。 有关这些 ACL 的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="security-for-groups"></a>组的安全性

当你或你的用户需要访问启用了分层命名空间的存储帐户中的数据时，最好是使用 Azure Active Directory 安全组。 一开始时，建议对容器的根目录使用 **ReadOnlyUsers**、**WriteAccessUsers** 和 **FullAccessUsers** 组，甚至可以对关键的子目录使用单独的安全组。 如果还有其他预期会在以后添加但目前尚未确定的用户组，可以考虑创建能够访问特定文件夹的虚拟安全组。 使用安全组可确保在为数千个文件分配新权限时不需要很长的处理时间。

### <a name="security-for-service-principals"></a>服务主体的安全性

Azure Active Directory 服务主体通常可供 Azure Databricks 之类的服务用来访问 Data Lake Storage Gen2 中的数据。 对许多客户来说，单个 Azure Active Directory 服务主体可能已经足够，该主体可以拥有 Data Lake Storage Gen2 容器根目录的完全权限。 其他客户可能需要多个包含不同服务主体的群集，让一个群集拥有数据的完全访问权限，另一个群集拥有只读访问权限。 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>启用 Data Lake Storage Gen2 防火墙，允许 Azure 服务访问

Data Lake Storage Gen2 支持启用防火墙并仅限 Azure 服务进行访问的选项。如果需要限制外部攻击途径，建议使用这一选项。 可以通过**防火墙** > **Enable firewall （on）** > **允许访问 Azure 服务**选项，在 Azure 门户中的存储帐户上启用防火墙。

若要从 Azure Databricks 访问你的存储帐户，请将 Azure Databricks 部署到你的虚拟网络，然后将该虚拟网络添加到防火墙。 请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

## <a name="resiliency-considerations"></a>复原注意事项

通过 Data Lake Storage Gen2 或任何云服务构建系统时，必须考虑到可用性要求以及如何响应可能出现的服务中断情况。 问题可能局限于特定的实例，也可能是区域性的，因此必须对二者都有计划。 可以选择较激进或不太激进的策略来确保高可用性和灾难恢复，具体取决于工作负荷的恢复时间目标和恢复点目标 SLA。

### <a name="high-availability-and-disaster-recovery"></a>高可用性和灾难恢复

高可用性 (HA) 和灾难恢复 (DR) 有时可以组合在一起，虽然每个的策略稍有不同，尤其是在涉及到数据的时候。 Data Lake Storage Gen2 已经可以在后台处理 3 个副本形式的复制，来应对局部硬件故障。 另外，其他复制选项（如 ZRS 或 GZRS）提高了 HA，而 GRS & RA-GRS 改善了 DR。 在制定计划以确保 HA 时，应考虑到在发生服务中断的情况下，工作负荷需尽快切换到在本地或新区域中单独复制的实例，以便访问最新数据。

在 DR 策略中，为了应对某个区域发生灾难性故障这种不太可能的事件，必须使用 GRS 或 RA-GRS 复制选项将数据复制到其他区域，这也很重要。 此外，还必须考虑到在出现数据损坏这样的极端例子时的要求，因此需要创建可供回退的定期快照。 根据数据的重要性和大小，可以考虑每隔 1 小时、6 小时、24 小时创建滚动性的增量快照，具体取决于风险承受能力。

考虑到 Data Lake Storage Gen2 的数据复原能力，建议通过符合 HA/DR 要求的 GRS 或 RA-GRS 异地复制数据。 另外，还应考虑通过各种方式让使用 Data Lake Storage Gen2 的应用程序能够自动故障转移到次要区域：可以监视触发器或失败尝试的时长，或者至少应向管理员发送通知，让其进行人工干预。 请注意，是进行故障转移，还是等待服务重新联机？这需要进行慎重的权衡。

### <a name="use-distcp-for-data-movement-between-two-locations"></a>使用 Distcp 在两个位置之间进行数据移动

DistCp 是 distributed copy（分布式复制）的简称，是 Hadoop 随附的一个 Linux 命令行工具，适用于在两个位置之间进行分布式数据移动。 这两个位置可以是 Data Lake Storage Gen2、HDFS 或 S3。 此工具使用 Hadoop 群集（例如 HDInsight）上的 MapReduce 作业在所有节点上进行横向扩展。 Distcp 被认为是在没有特殊网络压缩设备的情况下移动大数据的最快方式。 Distcp 还提供了在两个位置之间仅更新增量数据的选项，可以处理自动重试，并且可以对计算进行动态缩放。 如果需要复制 Hive/Spark 表之类的内容（在单个目录中有许多大型文件），而且只需要对修改的数据进行复制，则此方法相当有效。 由于这些原因，在大数据存储之间复制数据时，最建议使用的工具是 Distcp。

复制作业可以通过使用频率或数据触发器的 Apache Oozie 工作流触发，也可以通过 Linux cron 作业触发。 对于密集型复制作业，建议启动一个单独的 HDInsight Hadoop 群集，该群集可以专门针对复制作业进行调整和缩放。 这样可确保复制作业不会干扰关键作业。 如果运行复制的频率足够宽，甚至可以在每次作业之间关闭群集。 如果故障转移到次要区域，请确保在次要区域也启动另一群集，以便在主要的 Data Lake Storage Gen2 帐户恢复后将新数据复制回该帐户。 有关如何使用 Distcp 的示例，请参阅[使用 Distcp 在 Azure 存储 Blob 和 Data Lake Storage Gen2 之间复制数据](../blobs/data-lake-storage-use-distcp.md)。

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>使用 Azure 数据工厂来计划复制作业

Azure 数据工厂还可以用来通过复制活动对复制作业进行计划，甚至可以通过复制向导设置一个频率。 请记住，Azure 数据工厂的云数据移动单位 (DMU) 有限，因此最终会对大数据工作负荷的吞吐量/计算进行限制。 另外，Azure 数据工厂目前不提供在 Data Lake Storage Gen2 帐户之间进行增量更新的功能，因此 Hive 表之类的目录需要获得完整的副本才能进行复制。 有关如何使用数据工厂进行复制的详细信息，请参阅[数据工厂文章](../../data-factory/load-azure-data-lake-storage-gen2.md)。

## <a name="monitoring-considerations"></a>监视注意事项

Data Lake Storage Gen2 提供了一些指标。这些指标可以在 Azure 门户的 Data Lake Storage Gen2 帐户中使用，也可以在 Azure Monitor 中使用。 Data Lake Storage Gen2 的可用性显示在 Azure 门户中。 若要获取 Data Lake Storage Gen2 帐户的最新可用性，必须运行你自己的综合性测试来验证可用性。 其他指标（例如总存储使用率、读/写请求数、入口/出口）可供监视应用程序使用，还可以在超出阈值（例如平均延迟时间或每分钟错误数）时触发警报。

## <a name="directory-layout-considerations"></a>目录布局注意事项

将数据放置到 Data Lake 中时，必须对数据结构进行预先规划，以便有效地利用安全、分区和处理方面的措施。 以下许多建议适用于所有大数据工作负荷。 每个工作负荷对数据使用方式的要求各不相同，不过下面是一些在使用 IoT 和批处理方案时值得考虑的常见布局。

### <a name="iot-structure"></a>IoT 结构

在 IoT 工作负荷的数据存储中，可能会放置大量来自多个产品、设备、组织和客户的数据。 必须预先规划目录布局，以便对下游使用者的数据进行组织，确保其安全性并提高处理效率。 考虑使用的常规模板可能是以下布局：

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

例如，在英国，飞机引擎的着陆遥测数据可能看起来像以下结构：

    UK/Planes/BA1293/Engine1/2017/08/11/12/

将日期置于目录结构的末尾有一个重要原因。 如果需要将特定区域或主题限定给特定用户/组访问，可以使用 POSIX 权限轻松完成该操作。 否则，在只能允许特定的安全组查看英国的数据或特定飞机的情况下，如果将日期结构置于前面，则需要针对每个小时目录下的很多目录设置单独的权限。 另外，如果将日期结构前置，则随着时间的推移，目录的数目会呈指数增长。

### <a name="batch-jobs-structure"></a>批处理作业结构

概括地讲，在批处理中，常用的方法是先将数据置于“in”目录中。 然后，当数据处理完以后，再将新数据置于“out”目录中，供下游进程使用。 有的作业需要对单个文件进行处理，但可能不需要对大型数据集进行大规模并行处理。对于这种作业，有时候会使用此目录结构。 与上面建议的 IoT 结构一样，好的目录结构会设置父级目录，用于存储区域和主题之类的内容（例如，组织、产品/制造者）。 此结构有助于确保组织数据的安全，并可更好地管理工作负荷中的数据。 另外，可以考虑在结构中添加日期和时间，以便更好地在处理过程中进行组织和筛选式搜索，增强安全性并提高自动化程度。 日期结构的粒度级别取决于上传或处理数据的时间间隔，例如每小时、每天甚至每月。

有时候，数据损坏或格式异常会导致文件处理失败。 在这种情况下，可以将这些文件移到 **/bad** 文件夹中进一步进行检查，这样的目录结构更有效。 还可以通过批处理作业将这些损坏的文件报告或通知给相关人员，要求其进行人工干预。** 请考虑以下模板结构：

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

例如，一家市场营销公司每天从其北美的客户端提取客户更新数据。 该数据在处理前和处理后可能看起来像以下片段：

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

在最常见的情况下，批量数据在处理后会直接进入 Hive 之类的数据库或传统的 SQL 数据库，不需要 **/in** 或 **/out** 文件夹，因为输出已经进入一个适用于 Hive 表或外部数据库的单独文件夹中。 例如，每日从客户处提取的数据会置于各自的文件夹中。在经过 Azure 数据工厂、Apache Oozie 或 Apache Airflow 之类工具的协调后，会触发一个每日 Hive 作业或 Spark 作业来处理数据，然后将数据写入 Hive 表中。

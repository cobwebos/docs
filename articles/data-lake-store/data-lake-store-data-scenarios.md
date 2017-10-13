---
title: "有关 Data Lake Store 的数据方案 | Microsoft Docs"
description: "了解 Data Lake Store 中可进行数据引入、处理、下载和可视化的不同方案和工具"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 37409a71-a563-4bb7-bc46-2cbd426a2ece
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 5c3e4a4d98922533bcf901108d58329cb951b01c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>使用 Azure Data Lake Store 满足大数据要求
大数据处理的四个主要阶段：

* 实时或批量引入大量数据到数据存储
* 处理数据
* 下载数据
* 可视化数据

本文介绍有关 Azure Data Lake Store 的这三个阶段，使用户了解可用于满足大数据需求的选项和工具。

## <a name="ingest-data-into-data-lake-store"></a>引入数据到 Data Lake Store
本部分重点介绍不同的数据源和引入数据到 Data Lake Store 帐户的不同方式。

![将数据引入 Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "将数据引入 Data Lake Store")

### <a name="ad-hoc-data"></a>临时数据
这表示可用于形成大数据应用程序原型的较小数据集。 存在数种不同的引入临时数据的方式，具体取决于数据源。

| 数据源 | 引入方式 |
| --- | --- |
| 本地计算机 |<ul> <li>[Azure 门户](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure 跨平台 CLI 2.0](data-lake-store-get-started-cli-2.0.md)</li> <li>[使用适用于 Visual Studio 的 Data Lake 工具](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure 存储 Blob |<ul> <li>[Azure 数据工厂](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy 工具](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight 群集上运行的 DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>流数据
这表示可由应用程序、设备、传感器等多种源生成的数据。此数据可通过各种工具引入到 Data Lake Store。 这些工具通常实时逐事件捕获和处理数据，并批量将事件写入到 Data Lake Store，以便可使这些事件得到进一步处理。

可用工具如下：

* [Azure 流分析](../stream-analytics/stream-analytics-data-lake-output.md) - 可使用 Azure Data Lake Store 输出将引入事件中心的事件写入到 Azure Data Lake。
* [Azure HDInsight Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md) - 可直接将数据从 Storm 群集写入到 Data Lake Store。
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – 可接收事件中心内的事件，然后使用 [Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md) 将其写入到 Data Lake Store。

### <a name="relational-data"></a>关系数据
也可从关系数据库中获得数据。 在一个时间段期间，关系数据库会收集大量数据，这些数据如果通过大数据管道处理，可提供重要见解。 可使用以下工具将这类数据移动到 Data Lake Store。

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure 数据工厂](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web 服务器日志数据（使用自定义应用程序上传）
由于分析 web 服务器日志数据是大数据应用程序的常见用途，且需要上传大量日志文件到 Data Lake Store，因此会明确调用此类数据集。 可使用以下任何工具编写自己的脚本或应用程序来上传此类数据。

* [Azure 跨平台 CLI 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure 数据工厂](../data-factory/copy-activity-overview.md)

对于上传 web 服务器日志数据和上传其他类型的数据（例如社会情绪数据），编写自定义脚本/应用程序是一个非常不错的方式，因为这可灵活地将数据上传组件作为更大的大数据应用程序的一部分而包含在内。 某些情况下，此代码的形式可能是脚本或简单的命令行实用工具。 其他情况下，此代码可用于将大数据处理集成到业务应用程序或解决方案中。

### <a name="data-associated-with-azure-hdinsight-clusters"></a>与 Azure HDInsight 群集关联的数据
大多数 HDInsight 群集类型（Hadoop、HBase、Storm）支持将 Data Lake Store 作为数据存储库。 HDInsight 群集从 Azure 存储 Blob (WASB)访问数据。 要提高性能，可从 WASB 将数据复制到关联此群集的 Data Lake Store 帐户。 可使用以下工具复制数据。

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy 服务](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure 数据工厂](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>存储在本地或 IaaS Hadoop 群集的数据
使用 HDFS 可在本地计算机上的现有 Hadoop 群集中存储大量数据。 Hadoop 群集可能在本地部署或 Azure 上的 IaaS 群集中。 将此类数据以一次性或重复方式复制到 Azure Data Lake Store 可能存在一些要求。 实现此目的有多种可用选项。 以下是替代项和相关折衷方案的列表。

| 方法 | 详细信息 | 优点 | 注意事项 |
| --- | --- | --- | --- |
| 使用 Azure 数据工厂 (ADF) 直接将数据从 Hadoop 群集复制到 Azure Data Lake Store |[ADF 支持将 HDFS 作为数据源](../data-factory/connector-hdfs.md) |ADF 提供 HDFS 开箱即用支持和一流的端到端管理和监视 |要求在本地或 IaaS 群集中部署数据管理网关 |
| 从 Hadoop 将数据以文件形式导出。 然后使用适当机制将文件复制到 Azure Data Lake Store。 |可使用如下应用将文件复制到 Azure Data Lake Store： <ul><li>[适用于 Windows 操作系统的 Azure PowerShell](data-lake-store-get-started-powershell.md)</li><li>[适用于非 Windows 操作系统的 Azure 跨平台 CLI 2.0](data-lake-store-get-started-cli-2.0.md)</li><li>使用任何 Data Lake Store SDK 自定义应用</li></ul> |快速入门。 可执行自定义上传 |涉及多种技术的多步处理。 由于工具的自定义性质，随着时间推移，管理和监视会逐渐变得具有挑战性 |
| 使用 Distcp 将数据从 Hadoop 复制到 Azure 存储。 然后使用适当机制将数据从 Azure 存储复制到 Data Lake Store。 |从 Azure 存储复制数据到 Data Lake Store 可使用以下工具： <ul><li>[Azure 数据工厂](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy 工具](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight 群集上运行的 Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |可使用开放源代码工具。 |涉及多种技术的多步处理 |

### <a name="really-large-datasets"></a>大型数据集
对于上传兆兆字节范围内的数据集，使用上述方法可能有时速度慢且成本高。 这种情况下，可使用以下选项。

* **使用 Azure ExpressRoute**。 Azure ExpressRoute 可允许在 Azure 数据中心与本地中的基础结构之间创建专有连接。 这对传输大量数据提供了可靠的选项。 有关详细信息，请参阅[ Azure ExpressRoute 文档](../expressroute/expressroute-introduction.md)。
* **数据“离线上传”**。 如果由于任何原因而导致使用 Azure ExpressRoute 不可行，可使用 [Azure 导入/导出服务](../storage/common/storage-import-export-service.md)将包含数据的硬盘驱动器发送到 Azure 数据中心。 数据会首先上传到 Azure 存储 Blob。 然后可使用 [Azure 数据工厂](../data-factory/connector-azure-data-lake-store.md)或 [AdlCopy 工具](data-lake-store-copy-data-azure-storage-blob.md)从 Azure 存储 Blob 复制数据到 Data Lake Store。

  > [!NOTE]
  > 使用此导入/导出服务时，发送到 Azure 数据中心的磁盘上的文件大小不可大于 195 GB。
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>处理存储在 Data Lake Store 中的数据
数据在 Data Lake Store 中可用后，可使用支持的大数据应用程序在此数据上运行分析。 目前，可使用 Azure HDInsight 和 Azure Data Lake Analytics 在存储在 Data Lake Store 中的数据上运行数据分析。

![分析 Data Lake Store 中的数据](./media/data-lake-store-data-scenarios/analyze-data.png "分析 Data Lake Store 中的数据")

可查看以下示例。

* [创建包含 Data Lake Store（作为存储）的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>从 Data Lake Store 下载数据
用户可能还希望对一些方案从 Azure Data Lake Store 下载或移动数据，例如：

* 将数据移动到其他存储库以便连接现有数据处理管道。 例如，用户可能希望从 Data Lake Store 将数据移动到 SQL 数据库 或本地 SQL 服务器。
* 构建应用程序原型时，下载数据到本地计算机以在 IDE 中进行处理。

![从 Data Lake Store 传出数据](./media/data-lake-store-data-scenarios/egress-data.png "从 Data Lake Store 传出数据")

这种情况下，可使用以下任何选项：

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure 数据工厂](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

也可使用以下方法自己编写脚本/应用程序来从 Data Lake Store 下载数据。

* [Azure 跨平台 CLI 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>可视化 Data Lake Store 中的数据
可使用混合服务创建 Data Lake Store 中存储的数据的可视化表示形式。

![可视化 Data Lake Store 中的数据](./media/data-lake-store-data-scenarios/visualize-data.png "可视化 Data Lake Store 中的数据")

* 首先通过使用 [Azure 数据工厂从 Data Lake Store 将数据移动到 Azure SQL 数据仓库](../data-factory/copy-activity-overview.md)
* 之后，可[集成 Power BI 和 Azure SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md)来创建数据的可视化表示形式。

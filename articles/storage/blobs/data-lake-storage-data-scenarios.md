---
title: 涉及 Azure Data Lake Storage Gen2 的数据方案 | Microsoft Docs
description: 了解可在 Data Lake Storage Gen2（以前称为 Azure Data Lake Store）中进行数据引入、处理、下载和可视化的不同方案和工具
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.openlocfilehash: 1c50a6e14955b2c31222ff1317aa99ad28866ec8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864721"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>使用 Azure Data Lake Storage Gen2 满足大数据需求

大数据处理的四个主要阶段：

* 实时或批量引入大量数据到数据存储
* 处理数据
* 下载数据
* 可视化数据

本文介绍有关 Azure Data Lake Storage Gen2 的这些阶段，帮助用户了解可用于满足大数据需求的选项和工具。

## <a name="ingest-data-into-data-lake-storage-gen2"></a>将数据引入 Data Lake Storage Gen2
本部分重点介绍不同的数据源和将数据引入 Data Lake Storage Gen2 帐户的不同方式。

![将数据引入 Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingest data into Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>临时数据
这表示可用于形成大数据应用程序原型的较小数据集。 存在数种不同的引入临时数据的方式，具体取决于数据源。

| 数据源 | 引入方式 |
| --- | --- |
| 本地计算机 |<ul> <li>[存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)</ul> |
| Azure 存储 Blob |<ul> <li>[Azure 数据工厂](../../data-factory/connector-azure-data-lake-store.md)</li> <li>[AzCopy 工具](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight 群集上运行的 DistCp](data-lake-storage-use-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>流数据
这表示可由应用程序、设备、传感器等多种源生成的数据。此数据可通过各种工具引入 Data Lake Storage Gen2。 这些工具通常实时逐事件捕获和处理数据，并随后批量将事件写入 Data Lake Storage Gen2，以便这些事件可以得到进一步处理。

可用工具如下：

* [Azure HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md) - 可直接将数据从 Storm 群集写入 Data Lake Storage Gen2。

### <a name="relational-data"></a>关系数据
也可从关系数据库中获得数据。 在一个时间段期间，关系数据库会收集大量数据，这些数据如果通过大数据管道处理，可提供重要见解。 可使用以下工具将此类数据移入 Data Lake Storage Gen2。

* [Azure 数据工厂](../../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web 服务器日志数据（使用自定义应用程序上传）
由于分析 Web 服务器日志数据是大数据应用程序的常见用途，且需要上传大量日志文件到 Data Lake Storage Gen2，将明确调用此类数据集。 可使用以下任何工具编写自己的脚本或应用程序来上传此类数据。

* [Azure 数据工厂](../../data-factory/copy-activity-overview.md)

对于上传 web 服务器日志数据和上传其他类型的数据（例如社会情绪数据），编写自定义脚本/应用程序是一个非常不错的方式，因为这可灵活地将数据上传组件作为更大的大数据应用程序的一部分而包含在内。 某些情况下，此代码的形式可能是脚本或简单的命令行实用工具。 其他情况下，此代码可用于将大数据处理集成到业务应用程序或解决方案中。

### <a name="data-associated-with-azure-hdinsight-clusters"></a>与 Azure HDInsight 群集关联的数据
大多数 HDInsight 群集类型（Hadoop、HBase、Storm）支持将 Data Lake Storage Gen2 用作数据存储库。 HDInsight 群集从 Azure 存储 Blob (WASB)访问数据。 要提高性能，可从 WASB 将数据复制到与此群集关联的 Data Lake Storage Gen2 帐户。 可使用以下工具复制数据。

* [Apache DistCp](data-lake-storage-use-distcp.md)
* [AzCopy 工具](../common/storage-use-azcopy-v10.md)
* [Azure 数据工厂](../../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>存储在本地或 IaaS Hadoop 群集的数据
使用 HDFS 可在本地计算机上的现有 Hadoop 群集中存储大量数据。 Hadoop 群集可能在本地部署或 Azure 上的 IaaS 群集中。 通过一次性方法或重复方式将此类数据复制到 Azure Data Lake Storage Gen2 可能存在一些要求。 实现此目的有多种可用选项。 以下是替代项和相关折衷方案的列表。

| 方法 | 详细信息 | 优点 | 注意事项 |
| --- | --- | --- | --- |
| 使用 Azure 数据工厂 (ADF) 直接将数据从 Hadoop 群集复制到 Azure Data Lake Storage Gen2 |[ADF 支持将 HDFS 作为数据源](../../data-factory/connector-hdfs.md) |ADF 提供 HDFS 开箱即用支持和一流的端到端管理和监视 |要求在本地或 IaaS 群集中部署数据管理网关 |
| 使用 Distcp 将数据从 Hadoop 复制到 Azure 存储。 然后使用适当机制将数据从 Azure 存储复制到 Data Lake Storage Gen2。 |可使用以下工具将数据从 Azure 存储复制到 Data Lake Storage Gen2： <ul><li>[Azure 数据工厂](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy 工具](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight 群集上运行的 Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |可使用开放源代码工具。 |涉及多种技术的多步处理 |

### <a name="really-large-datasets"></a>大型数据集
对于上传兆兆字节范围内的数据集，使用上述方法可能有时速度慢且成本高。 这种情况下，可使用以下选项。

* **使用 Azure ExpressRoute**。 Azure ExpressRoute 可允许在 Azure 数据中心与本地中的基础结构之间创建专有连接。 这对传输大量数据提供了可靠的选项。 有关详细信息，请参阅[ Azure ExpressRoute 文档](../../expressroute/expressroute-introduction.md)。

## <a name="process-data-stored-in-data-lake-storage-gen2"></a>处理存储在 Data Lake Storage Gen2 中的数据
数据在 Data Lake Storage Gen2 中可用后，可使用支持的大数据应用程序对此数据运行分析。 目前，可使用 Azure HDInsight 和 Azure Databricks 对存储在 Data Lake Storage Gen2 中的数据运行数据分析。

![分析 Data Lake Storage Gen2 中的数据](./media/data-lake-storage-data-scenarios/analyze-data.png "Analyze data in Data Lake Storage Gen2")

有关示例，请参阅[配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)。


## <a name="download-data-from-data-lake-storage-gen2"></a>从 Data Lake Storage Gen2 下载数据
用户可能还希望为一些方案从 Azure Data Lake Storage Gen2 下载或移动数据，例如：

* 将数据移动到其他存储库以便连接现有数据处理管道。 例如，用户可能希望从 Data Lake Storage Gen2 将数据移动到 Azure SQL 数据库或本地 SQL 服务器。
* 构建应用程序原型时，下载数据到本地计算机以在 IDE 中进行处理。

![从 Data Lake Storage Gen2 流出数据](./media/data-lake-storage-data-scenarios/egress-data.png "Egress data from Data Lake Storage Gen2")

这种情况下，可使用以下任何选项：

* [Azure 数据工厂](../../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-storage-use-distcp.md)

## <a name="visualize-data-in-data-lake-storage-gen2"></a>可视化 Data Lake Storage Gen2 中的数据
可混合使用服务来创建 Data Lake Storage Gen2 中存储的数据的可视化表示形式。

![可视化 Data Lake Storage Gen2 中的数据](./media/data-lake-storage-data-scenarios/visualize-data.png "Visualize data in Data Lake Storage Gen2")

* 首先使用 [Azure 数据工厂将数据从 Data Lake Storage Gen2 迁移到 Azure SQL 数据仓库](../../data-factory/copy-activity-overview.md)
* 之后，可[集成 Power BI 和 Azure SQL 数据仓库](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md)来创建数据的可视化表示形式。

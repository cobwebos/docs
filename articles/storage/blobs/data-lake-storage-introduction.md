---
title: Azure Data Lake Storage Gen2 简介
description: 提供 Azure Data Lake Storage Gen2 的概述
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: b25a47e929ef540ad3a74a86d431962d37cde434
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009428"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 简介

‎Azure Data Lake Storage Gen2 是一组专用于大数据分析的功能，以 [Azure Blob 存储](storage-blobs-introduction.md)为基础而构建。 Data Lake Storage Gen2 是将我们现有的两个存储服务（Azure Blob 存储和 Azure Data Lake Storage Gen1）的功能进行聚合得到的结果。 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) 的功能（例如文件系统语义、目录、文件级安全性和规模）与 [Azure Blob 存储](storage-blobs-introduction.md)中的低成本分层存储、高可用性/灾难恢复功能进行了组合。

## <a name="designed-for-enterprise-big-data-analytics"></a>专为企业大数据分析而设计

Data Lake Storage Gen2 使 Azure 存储成为在 Azure 上构建企业 Data Lake 的基础。 Data Lake Storage Gen2 从一开始就设计为存储数千万亿字节的信息，同时保持数百千兆位的吞吐量，允许你轻松管理大量数据。

Data Lake Storage Gen2 的一个基本部分是向 Blob 存储添加[分层命名空间](data-lake-storage-namespace.md)。 分层命名空间将对象/文件组织到目录层次结构中，以便进行有效的数据访问。 常见的对象存储命名约定在名称中使用斜杠来模拟分层目录结构。 这种结构在 Data Lake Storage Gen2 中得以真正实现。 诸如重命名或删除目录之类的操作在目录上成为单个原子元数据操作，而不是枚举或处理共享目录名称前缀的所有对象。

过去，基于云的分析必须在性能、管理和安全性方面做出妥协。 Data Lake Storage Gen2 通过以下方式解决这些方面中的每个问题：

-   优化了性能，因为你不需要将复制或转换数据作为分析的先决条件。 分层命名空间极大地提高了目录管理操作的性能，从而提高了整体作业性能。

-   管理更为容易，因为你可以通过目录和子目录来组织和操作文件。

-   安全性是可以强制实施的，因为可以在目录或单个文件上定义 POSIX 权限。

-   由于 Data Lake Storage Gen2 基于低成本的 [Azure Blob 存储](storage-blobs-introduction.md)而构建，因此，可以实现成本效益。 这些新增功能进一步降低了在 Azure 上运行大数据分析的总拥有成本。

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2 的主要功能

-   Hadoop 兼容访问：使用 Data Lake Storage Gen2，可以像使用 [Hadoop 分布式文件系统 (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) 一样管理和访问数据。 新的 [ABFS 驱动程序](data-lake-storage-abfs-driver.md)可在所有 Apache Hadoop 环境中使用（包括 [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)、[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index) 和 [SQL 数据仓库](https://docs.microsoft.com/azure/sql-data-warehouse/)）以访问 Data Lake Storage Gen2 中存储的数据。

-   POSIX 权限的超集：Data Lake Gen2 的安全模型支持 ACL 和 POSIX 权限，以及特定于 Data Lake Storage Gen2 的一些额外粒度。 可以通过存储资源管理器或 Hive 和 Spark 等框架来配置设置。

-   经济高效：Data Lake Storage Gen2 提供低成本的存储容量和事务。 随着数据在其整个生命周期中的转换，记帐费率变化通过诸如 [Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)的内置功能使成本保持在最低水平。

-   已优化的驱动程序：ABFS 驱动程序专门针对大数据分析进行了[优化](data-lake-storage-abfs-driver.md)。 相应的 REST API 通过 dfs 终结点 dfs.core.windows.net 进行显示。

### <a name="scalability"></a>可伸缩性

按照设计，无论是通过 Data Lake Storage Gen2 还是 Blob 存储接口进行访问，Azure 存储都可自如缩放。 它可以存储和处理许多百亿亿字节的数据。 这种存储量可用于在每秒高级别的输入/输出操作 (IOPS) 下以每秒千兆位 (Gbps) 的速度测量的吞吐量。 除持久性之外，以几乎恒定的每个请求延迟执行处理，这些延迟是在服务、帐户和文件级别上测量的。

### <a name="cost-effectiveness"></a>成本效益

基于 Azure Blob 存储生成 Data Lake Storage Gen2 的多个好处之一是存储容量和事务的低成本。 与其他云存储服务不同，在执行分析之前不需要移动或转换存储在 Data Lake Storage Gen2 中的数据。 有关定价的详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage)。

此外，[分层命名空间](data-lake-storage-namespace.md)等功能可显著提高许多分析作业的整体性能。 这一性能方面的提升意味着你需要较少的计算能力来处理相同数量的数据，从而降低端到端分析作业的总拥有成本 (TCO)。

### <a name="one-service-multiple-concepts"></a>一个服务，多个概念

Data Lake Storage Gen2 是用于大数据分析的附加功能，基于 Azure Blob 存储而构建。 虽然利用 Blob 的现有平台组件来创建和操作数据库进行分析有很多好处，但它确实导致了描述相同共享内容的多个概念。

以下是不同概念所描述的等效实体。 除非另有说明，否则这些实体是直接同义词：

| 概念                                | 顶级组织 | 较低级别的组织                                            | 数据容器 |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blob - 常规用途对象存储 | 容器              | 虚拟目录（仅限 SDK - 不提供原子操作） | Blob           |
| ADLS Gen2 - 分析存储          | 文件系统             | Directory                                                           | 文件           |

## <a name="supported-open-source-platforms"></a>支持的开源平台

多个开源平台支持 Data Lake Storage Gen2。 这些平台显示在下表中。

> [!NOTE]
> 仅支持此表中显示的版本。

| 平台 |  支持的版本 | 更多信息 |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6+ | [HDInsight 提供了哪些 Apache Hadoop 组件和版本？](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2+ | [Apache Hadoop 版本存档](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1+ | [Cloudera Enterprise 6.x 发行说明](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1+ | [Databricks 运行时版本](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[HortonWorks](https://hortonworks.com/)| 3.1.x++ | [配置云数据访问](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="next-steps"></a>后续步骤

以下文章介绍 Data Lake Storage Gen2 的一些主要概念，并详细介绍如何存储、访问、管理数据以及从数据中获取见解：

-   [分层命名空间](data-lake-storage-namespace.md)
-   [创建存储帐户](data-lake-storage-quickstart-create-account.md)
-   [在 Azure Databricks 中使用 Data Lake Storage Gen2 帐户](data-lake-storage-quickstart-create-databricks-account.md)

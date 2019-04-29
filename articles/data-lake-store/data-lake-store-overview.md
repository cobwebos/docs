---
title: 什么是 Azure 数据湖存储 Gen1？ | Microsoft Docs
description: 数据湖存储 Gen1 （以前称为 Azure Data Lake Store） 和其他数据存储相比它提供的价值概述
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 518c129aedf3161ab761d09139e0c4d988dd2cbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60885544"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>什么是 Azure 数据湖存储 Gen1？

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 是一个企业范围的超大规模存储库，适用于大数据分析工作负载。 使用 Azure Data Lake 可以在单个位置捕获任何大小、类型和引入速度的数据进行操作和探索分析。

使用与 WebHDFS 兼容的 REST API，可以从 Hadoop（HDInsight 群集提供）访问 Data Lake Storage Gen1。 它是为了让上存储的数据的分析并优化了针对数据分析方案的性能。 数据湖存储 Gen1 包括所有的企业级功能： 安全性、 可管理性、 可伸缩性、 可靠性和可用性。

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>关键功能

下面介绍了 Data Lake Storage Gen1 的一些重要功能。

### <a name="built-for-hadoop"></a>专为 Hadoop 而构建

数据湖存储 Gen1 是一个 Apache Hadoop 文件系统，是与 Hadoop 分布式文件系统 (HDFS) 兼容，，适用于 Hadoop 生态系统。 采用 WebHDFS API 的现有 HDInsight 应用程序或服务可以轻松与 Data Lake Storage Gen1 集成。 Data Lake Storage Gen1 还为应用程序公开了 WebHDFS 兼容的 REST 接口。

你可轻松分析数据存储在数据湖存储 Gen1 中使用 Hadoop 分析框架，例如 MapReduce 或 Hive。 可以预配 Azure HDInsight 群集，并将其配置为直接访问数据湖存储 Gen1 中存储的数据。

### <a name="unlimited-storage-petabyte-files"></a>无限存储空间，PB 量级的文件

数据湖存储 Gen1 提供无限的存储空间，并且可以存储各种分析数据。 它不会施加任何限制帐户大小、 文件大小或 data lake 中可存储的数据量。 单个文件可以从千字节到数千兆字节的大小。 存储数据，通过创建多个副本。 为其数据可以存储在 data lake 中的时间的持续时间没有限制。

### <a name="performance-tuned-for-big-data-analytics"></a>针对大数据分析优化了性能

数据湖存储 Gen1 是专为运行需要超大吞吐量以查询和分析大量数据的大规模分析系统。 Data Lake 将文件的各个部分散在大量独立的存储服务器中。 这可改善执行数据分析时并行读取文件的吞吐量。

### <a name="enterprise-ready-highly-available-and-secure"></a>企业级应用：高度可用且安全

Data Lake Storage Gen1 提供符合行业标准的可用性和可靠性。 数据资产可通过创建冗余副本来长期存储，防范任何意外的故障。

Data Lake Storage Gen1 还为存储的数据提供企业级安全性。 有关详细信息，请参阅 [保护 Azure Data Lake Storage Gen1 中的数据](#DataLakeStoreSecurity)。

### <a name="all-data"></a>所有数据

数据湖存储 Gen1 可以以其本机格式存储的任何数据，而无需事先经过转换。 加载数据之前，Data Lake Storage Gen1 不需要定义架构，而是等待独立的分析框架在分析时解释数据和定义架构。 存储任意大小和格式的文件的功能使数据湖存储 Gen1 来处理结构化、 半结构化和非结构化数据。

Data Lake Storage Gen1 的数据容器本质上是文件夹和文件。 只对存储的数据使用 Sdk、 Azure 门户和 Azure Powershell 执行的操作。 如果将数据放入使用这些接口在存储区，并使用适当的容器，可以存储任何类型的数据。 Data Lake Storage Gen1 不会根据其存储的数据类型对数据执行任何特殊处理。

## <a name="DataLakeStoreSecurity"></a>保护数据

Data Lake 存储 Gen1 使用 Azure Active Directory (Azure AD) 身份验证和访问控制列表 (Acl) 来管理访问对你的数据。

| Feature | 描述 |
| --- | --- |
| Authentication |数据湖存储 Gen1 集成与 Azure AD 标识和访问管理的所有数据湖存储 Gen1 中存储的数据。 集成，由于数据湖存储 Gen1 受益于所有 Azure AD 功能，例如多重身份验证、 条件性访问、 基于角色的访问控制、 应用程序使用情况监视、 安全监视和警报，等等。 Data Lake Storage Gen1 支持在 REST 接口中使用 OAuth 2.0 协议进行身份验证。 请参阅[身份验证数据湖存储 Gen1](data-lakes-store-authentication-using-azure-active-directory.md)。|
| 访问控制 |Data Lake Storage Gen1 通过支持 WebHDFS 协议公开的 POSIX 样式权限来提供访问控制。 你可以启用 Acl 的根文件夹、 子文件夹和单个文件。 有关 Acl 的数据湖存储 Gen1 上下文中的工作原理的详细信息，请参阅[中的数据湖存储 Gen1 的访问控制](data-lake-store-access-control.md)。 |
| 加密 |数据湖存储 Gen1 还提供了存储帐户中的数据加密。 创建 Data Lake Storage Gen1 帐户时可以指定加密设置。 可以选择加密或不加密数据。 有关详细信息，请参阅 [Data Lake Storage Gen1 中的加密](data-lake-store-encryption.md)。 有关如何提供与加密相关的配置的说明，请参阅[开始使用数据湖存储 Gen1 使用 Azure 门户](data-lake-store-get-started-portal.md)。 |

有关如何保护 Data Lake Storage Gen1 中的数据的说明，请参阅[保护 Azure Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)。

## <a name="application-compatibility"></a>应用程序兼容性

Data Lake Storage Gen1 与 Hadoop 生态系统中的大多数开源组件兼容。 它还集成其他 Azure 服务。 若要了解有关如何使用数据湖存储 Gen1 与开放源代码组件的详细信息和其他 Azure 服务，请使用以下链接：

- 有关可与 Data Lake Storage Gen1 互操作的开源应用程序列表，请参阅[与 Azure Data Lake Storage Gen1 兼容的应用程序和服务](data-lake-store-compatible-oss-other-applications.md)。
- 请参阅[与其他 Azure 服务集成](data-lake-store-integrate-with-other-services.md)若要了解如何使用数据湖存储 Gen1 与其他 Azure 服务启用更多的情况。
- 请参阅[使用 Data Lake Storage Gen1 的方案](data-lake-store-data-scenarios.md)，了解如何在引入数据、处理数据、下载数据和可视化数据等方案中使用 Data Lake Storage Gen1。

## <a name="data-lake-storage-gen1-file-system"></a>数据湖存储 Gen1 文件系统

可以通过文件系统 AzureDataLakeFilesystem 访问数据湖存储 Gen1 (adl: / /) 在 Hadoop 环境 （适用于 HDInsight 群集）。 应用程序和服务使用 adl: / / 可以充分利用进一步目前不可在 WebHDFS 中的性能优化。 数据湖存储 Gen1 提供您的灵活性使结果是，利用使用 adl:// 的建议选项获得最佳性能: / / 或继续直接使用 WebHDFS API 维护现有代码。 Azure HDInsight 充分使用 AzureDataLakeFilesystem 来提供 Data Lake Storage Gen1 的最佳性能。

可以使用 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` 访问 Data Lake Storage Gen1 中的数据。 有关如何访问数据湖存储 Gen1 中的数据的详细信息，请参阅[查看存储的数据属性](data-lake-store-get-started-portal.md#properties)。

## <a name="next-steps"></a>后续步骤

- [开始使用数据湖存储 Gen1 使用 Azure 门户](data-lake-store-get-started-portal.md)
- [开始使用数据湖存储 Gen1 使用.NET SDK](data-lake-store-get-started-net-sdk.md)
- [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)
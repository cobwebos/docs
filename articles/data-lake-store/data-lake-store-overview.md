---
title: 什么是 Azure 数据存储第 1 代？ | Microsoft Docs
description: 数据湖存储第 1 代（以前称为 Azure 数据存储）的概述，以及它在其他数据存储上提供的值
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67118804"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>什么是 Azure 数据存储第 1 代？

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 是一个企业范围的超大规模存储库，适用于大数据分析工作负载。 使用 Azure Data Lake 可以在单个位置捕获任何大小、类型和引入速度的数据进行操作和探索分析。

使用与 WebHDFS 兼容的 REST API，可以从 Hadoop（HDInsight 群集提供）访问 Data Lake Storage Gen1。 它旨在启用对存储的数据的分析，并针对数据分析方案进行性能调整。 数据存储单元 1 包括所有企业级功能：安全性、可管理性、可扩展性、可靠性和可用性。

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>关键功能

下面介绍了 Data Lake Storage Gen1 的一些重要功能。

### <a name="built-for-hadoop"></a>专为 Hadoop 而构建

数据存储湖存储 Gen1 是一个 Apache Hadoop 文件系统，与 Hadoop 分布式文件系统 （HDFS） 兼容，并与 Hadoop 生态系统配合使用。 采用 WebHDFS API 的现有 HDInsight 应用程序或服务可以轻松与 Data Lake Storage Gen1 集成。 Data Lake Storage Gen1 还为应用程序公开了 WebHDFS 兼容的 REST 接口。

您可以使用 Hadoop 分析框架（如 MapReduce 或 Hive）轻松分析数据存储在数据存储第 1 代中的数据。 您可以预配 Azure HDInsight 群集，并将其配置为直接访问数据存储在数据存储 Gen1 中的数据。

### <a name="unlimited-storage-petabyte-files"></a>无限存储空间，PB 量级的文件

数据存储库 Gen1 提供无限的存储，可以存储各种数据进行分析。 它不对帐户大小、文件大小或可存储在数据湖中的数据量施加任何限制。 单个文件的大小可以从千字节到 PB 不等。 通过进行多个副本来持久存储数据。 数据存储在数据湖中的持续时间没有限制。

### <a name="performance-tuned-for-big-data-analytics"></a>针对大数据分析优化了性能

数据存储库 Gen1 专为运行需要大量吞吐量以查询和分析大量数据的大型分析系统而设计。 Data Lake 将文件的各个部分散在大量独立的存储服务器中。 这可改善执行数据分析时并行读取文件的吞吐量。

### <a name="enterprise-ready-highly-available-and-secure"></a>企业就绪：高度可用且安全

Data Lake Storage Gen1 提供符合行业标准的可用性和可靠性。 数据资产可通过创建冗余副本来长期存储，防范任何意外的故障。

Data Lake Storage Gen1 还为存储的数据提供企业级安全性。 有关详细信息，请参阅 [保护 Azure Data Lake Storage Gen1 中的数据](#DataLakeStoreSecurity)。

### <a name="all-data"></a>所有数据

数据存储湖存储 Gen1 可以存储任何数据的原生格式，而无需任何以前的转换。 加载数据之前，Data Lake Storage Gen1 不需要定义架构，而是等待独立的分析框架在分析时解释数据和定义架构。 通过存储任意大小和格式的文件，Data Lake 存储 Gen1 能够处理结构化、半结构化和非结构化数据。

Data Lake Storage Gen1 的数据容器本质上是文件夹和文件。 使用 SDK、Azure 门户和 Azure 电源外壳对存储的数据进行操作。 如果使用这些接口并使用适当的容器将数据放入存储中，则可以存储任何类型的数据。 Data Lake Storage Gen1 不会根据其存储的数据类型对数据执行任何特殊处理。

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>保护数据

数据存储第 1 代使用 Azure 活动目录 （Azure AD） 进行身份验证，访问控制列表 （ACL） 管理对数据的访问。

| Feature | 描述 |
| --- | --- |
| 身份验证 |数据存储库 Gen1 与 Azure AD 集成，用于对数据存储在数据存储第 1 代中的所有数据进行标识和访问管理。 由于集成，Data Lake 存储 Gen1 受益于所有 Azure AD 功能，如多重身份验证、条件访问、基于角色的访问控制、应用程序使用情况监视、安全监视和警报等。 Data Lake Storage Gen1 支持在 REST 接口中使用 OAuth 2.0 协议进行身份验证。 请参阅[数据湖存储第 1 代身份验证](data-lakes-store-authentication-using-azure-active-directory.md)。|
| 访问控制 |Data Lake Storage Gen1 通过支持 WebHDFS 协议公开的 POSIX 样式权限来提供访问控制。 您可以在根文件夹、子文件夹和单个文件上启用 ACL。 有关 ACL 在数据存储第 1 代的上下文中如何工作的详细信息，请参阅[数据湖存储 Gen1 中的访问控制](data-lake-store-access-control.md)。 |
| 加密 |数据存储库 Gen1 还为帐户中存储的数据提供加密。 创建 Data Lake Storage Gen1 帐户时可以指定加密设置。 可以选择加密或不加密数据。 有关详细信息，请参阅 [Data Lake Storage Gen1 中的加密](data-lake-store-encryption.md)。 有关如何提供加密相关配置的说明，请参阅[使用 Azure 门户开始使用数据存储第 1 代](data-lake-store-get-started-portal.md)。 |

有关如何保护 Data Lake Storage Gen1 中的数据的说明，请参阅[保护 Azure Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)。

## <a name="application-compatibility"></a>应用程序兼容性

Data Lake Storage Gen1 与 Hadoop 生态系统中的大多数开源组件兼容。 它还与其他 Azure 服务很好地集成。 要了解有关如何使用数据湖存储 Gen1 与开源组件和其他 Azure 服务有关，请使用以下链接：

- 有关可与 Data Lake Storage Gen1 互操作的开源应用程序列表，请参阅[与 Azure Data Lake Storage Gen1 兼容的应用程序和服务](data-lake-store-compatible-oss-other-applications.md)。
- 请参阅[与其他 Azure 服务集成](data-lake-store-integrate-with-other-services.md)，了解如何将数据存储库 Gen1 与其他 Azure 服务一起启用更广泛的方案。
- 请参阅[使用 Data Lake Storage Gen1 的方案](data-lake-store-data-scenarios.md)，了解如何在引入数据、处理数据、下载数据和可视化数据等方案中使用 Data Lake Storage Gen1。

## <a name="data-lake-storage-gen1-file-system"></a>数据存储湖存储第一代文件系统

数据湖存储第 1 代可通过文件系统 AzureDataLakeFile 系统 （adl://）在 Hadoop 环境中访问（可通过 HDInsight 群集提供）。 使用adl://的应用程序和服务可以利用 WebHDFS 中当前不可用的进一步性能优化。 因此，Data Lake 存储 Gen1 使您能够灵活地使用最佳性能，并推荐使用adl://，或者通过继续直接使用 WebHDFS API 来维护现有代码。 Azure HDInsight 充分使用 AzureDataLakeFilesystem 来提供 Data Lake Storage Gen1 的最佳性能。

可以使用 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` 访问 Data Lake Storage Gen1 中的数据。 有关如何访问数据存储 Gen1 中的数据的详细信息，请参阅[查看存储数据的属性](data-lake-store-get-started-portal.md#properties)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户开始使用数据存储第 1 代](data-lake-store-get-started-portal.md)
- [使用 .NET SDK 开始使用数据存储第 1 代](data-lake-store-get-started-net-sdk.md)
- [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)
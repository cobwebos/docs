---
title: Azure Data Lake Storage Gen1 概述 | Microsoft Docs
description: 了解什么是 Data Lake Storage Gen1（以前称为 Azure Data Lake Store），及其与其他数据存储相比它有哪些优势
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 438eab091fac103b66f0789beca0098b87ee44cd
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885649"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 概述

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 是一个企业范围的超大规模存储库，适用于大数据分析工作负载。 使用 Azure Data Lake 可以在单个位置捕获任何大小、类型和引入速度的数据进行操作和探索分析。

> [!TIP]
> 使用 [Data Lake Storage Gen1 学习路径](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/)开始探索 Data Lake Storage Gen1 服务。
> 
> 

使用与 WebHDFS 兼容的 REST API，可以从 Hadoop（HDInsight 群集提供）访问 Data Lake Storage Gen1。 该服务专为存储数据分析而设计，并已针对数据分析方案优化了性能。 它现成地包含了现实企业用例不可或缺的所有企业级功能：安全性、可管理性、伸缩性、可靠性和可用性。

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

下面介绍了 Data Lake Storage Gen1 的一些重要功能。

### <a name="built-for-hadoop"></a>专为 Hadoop 而构建
Data Lake Storage Gen1 是一个 Apache Hadoop 文件系统，该系统与 Hadoop 分布式文件系统 (HDFS) 兼容并与 Hadoop 生态系统相互协作。  采用 WebHDFS API 的现有 HDInsight 应用程序或服务可以轻松与 Data Lake Storage Gen1 集成。 Data Lake Storage Gen1 还为应用程序公开了 WebHDFS 兼容的 REST 接口

使用 Hadoop 分析框架（例如 MapReduce 或 Hive），可以轻松分析 Data Lake Storage Gen1 中存储的数据。 可将 Microsoft Azure HDInsight 群集预配和配置为直接访问 Data Lake Storage Gen1 中存储的数据。

### <a name="unlimited-storage-petabyte-files"></a>无限存储空间，PB 量级的文件
Data Lake Storage Gen1 提供无限存储空间，适合用于存储各种分析数据。 帐户大小、文件大小或 Data Lake 中可存储的数据量均无任何限制。 支持 KB 到 PB 量级的单个文件大小，非常适合用于存储任何类型的数据。 通过创建多个副本来长期存储数据，数据在 Data Lake 中的存储持续时间没有限制。

### <a name="performance-tuned-for-big-data-analytics"></a>针对大数据分析优化了性能
Data Lake Storage Gen1 旨在运行需要利用超大吞吐量查询和分析海量数据的大规模分析系统。 Data Lake 将文件的各个部分散在大量独立的存储服务器中。 这可改善执行数据分析时并行读取文件的吞吐量。

### <a name="enterprise-ready-highly-available-and-secure"></a>企业就绪：高度可用且安全
Data Lake Storage Gen1 提供符合行业标准的可用性和可靠性。 数据资产可通过创建冗余副本来长期存储，防范任何意外的故障。 企业可以在其解决方案中使用 Data Lake Storage Gen1 作为现有数据平台的重要组成部分。

Data Lake Storage Gen1 还为存储的数据提供企业级安全性。 有关详细信息，请参阅 [保护 Azure Data Lake Storage Gen1 中的数据](#DataLakeStoreSecurity)。

### <a name="all-data"></a>所有数据
Data Lake Storage Gen1 可按本机格式（原样）存储任何数据，不需要事先经过转换。 加载数据之前，Data Lake Storage Gen1 不需要定义架构，而是等待独立的分析框架在分析时解释数据和定义架构。 Data Lake Storage Gen1 能够存储任意大小和格式的文件，因此可以处理结构化、半结构化和非结构化数据。

Data Lake Storage Gen1 的数据容器本质上是文件夹和文件。 可以使用 SDK、Azure 门户和 Azure Powershell 来处理存储的数据。 只要使用这些接口和相应容器将数据放入存储，就能存储任何类型的数据。 Data Lake Storage Gen1 不会根据其存储的数据类型对数据执行任何特殊处理。

## <a name="DataLakeStoreSecurity"></a>保护 Data Lake Storage Gen1 中的数据
Data Lake Storage Gen1 使用 Azure Active Directory 进行身份验证，使用访问控制列表 (ACL) 管理对数据的访问。

| Feature | 描述 |
| --- | --- |
| Authentication |Data Lake Storage Gen1 与 Azure Active Directory (AAD) 集成，可对 Data Lake Storage Gen1 中存储的所有数据进行标识与访问管理。 因为这种集成，Data Lake Storage Gen1 可受益于所有 AAD 功能，包括多重身份验证、条件访问、基于角色的访问控制、应用程序使用情况监视、安全监视和警报等。Data Lake Storage Gen1 支持在 REST 接口中使用 OAuth 2.0 协议进行身份验证。 请参阅 [Data Lake Storage Gen1 身份验证](data-lakes-store-authentication-using-azure-active-directory.md)|
| 访问控制 |Data Lake Storage Gen1 通过支持 WebHDFS 协议公开的 POSIX 样式权限来提供访问控制。 可对根文件夹、子文件夹和单个文件启用 ACL。 有关 ACL 在 Data Lake Storage Gen1 上下文中的工作原理的详细信息，请参阅 [Data Lake Storage Gen1 中的访问控制](data-lake-store-access-control.md)。 |
| 加密 |Data Lake Storage Gen1 还针对帐户中存储的数据提供加密。 创建 Data Lake Storage Gen1 帐户时可以指定加密设置。 可以选择加密或不加密数据。 有关详细信息，请参阅 [Data Lake Storage Gen1 中的加密](data-lake-store-encryption.md)。 有关如何提供加密相关配置的说明，请参阅[通过 Azure 门户开始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)。 |

想要详细了解如何在 Data Lake Storage Gen1 中保护数据吗？ 请访问以下链接。

* 有关如何保护 Data Lake Storage Gen1 中的数据的说明，请参阅[保护 Azure Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)。
* 更喜欢视频？ [观看这段视频](https://mix.office.com/watch/1q2mgzh9nn5lx)，了解如何保护 Data Lake Storage Gen1 中存储的数据。

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>与 Data Lake Storage Gen1 兼容的应用程序
Data Lake Storage Gen1 与 Hadoop 生态系统中的大多数开源组件兼容。 此外，还与其他 Azure 服务完美集成。 于是，它成为满足数据存储需求的绝佳选择。 请访问以下链接，详细了解 Data Lake Storage Gen1 如何配合开源组件及其他 Azure 服务。

* 有关可与 Data Lake Storage Gen1 互操作的开源应用程序列表，请参阅[与 Azure Data Lake Storage Gen1 兼容的应用程序和服务](data-lake-store-compatible-oss-other-applications.md)。
* 请参阅[与其他 Azure 服务集成](data-lake-store-integrate-with-other-services.md)，了解 Data Lake Storage Gen1 如何与其他 Azure 服务配合提供更多方案。
* 请参阅[使用 Data Lake Storage Gen1 的方案](data-lake-store-data-scenarios.md)，了解如何在引入数据、处理数据、下载数据和可视化数据等方案中使用 Data Lake Storage Gen1。

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>什么是 Data Lake Storage Gen1 文件系统 (adl://)？
可以在 Hadoop 环境（在 HDInsight 群集上提供）中通过新文件系统 AzureDataLakeFilesystem (adl://) 访问 Data Lake Storage Gen1。 使用 adl:// 的应用程序和服务可以使用目前无法在 WebHDFS 中使用的其他性能优化功能。 因此，Data Lake Storage Gen1 可以提供更大的弹性：使用 adl:// 的建议选项获得最佳性能，或继续直接使用 WebHDFS API 维护现有代码。 Azure HDInsight 充分使用 AzureDataLakeFilesystem 来提供 Data Lake Storage Gen1 的最佳性能。

可以使用 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` 访问 Data Lake Storage Gen1 中的数据。 有关如何访问 Data Lake Storage Gen1 中的数据的详细信息，请参阅[查看存储的数据的属性](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>后续步骤

* [开始使用数据湖存储 Gen1 使用 Azure 门户](data-lake-store-get-started-portal.md)
* [开始使用 Azure 数据湖存储 Gen1 使用.NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure HDInsight 中使用数据湖存储 Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
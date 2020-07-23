---
title: HDInsight 中的 Azure Data Lake Storage Gen1 概述
description: HDInsight 中的 Data Lake Storage Gen1 的概述。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 947dd125cf9c5f5874eed380b3d69cff11509e31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82187239"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>HDInsight 中的 Azure Data Lake Storage Gen1 概述

Azure Data Lake Storage Gen1 是一个企业范围的超大规模存储库，适用于大数据分析工作负载。 使用 Azure Data Lake，可以捕获任何大小、类型和引入速度的数据。 在一个位置进行操作和探索分析。

使用与 WebHDFS 兼容的 REST API，可以从 Hadoop（HDInsight 群集提供）访问 Data Lake Storage Gen1。 Data Lake Storage Gen1 专为分析存储数据而设计，并已针对数据分析方案的性能做了优化。 Gen1 包括对实际企业用例至关重要的功能。 这些功能包括安全性、可管理性、适应性、可靠性和可用性。

有关 Azure Data Lake Storage Gen1 的详细信息，请参阅 [Azure Data Lake Storage Gen1 概述](../data-lake-store/data-lake-store-overview.md)。

下面介绍了 Data Lake Storage Gen1 的重要功能。

## <a name="compatibility-with-hadoop"></a>与 Hadoop 兼容

Data Lake Storage Gen1 是与 HDFS 和 Hadoop 环境兼容的 Apache Hadoop 文件系统。  使用 WebHDFS API 的 HDInsight 应用程序或服务可以轻松地与 Data Lake Storage Gen1 集成。 Data Lake Storage Gen1 还为应用程序公开了 WebHDFS 兼容的 REST 接口。

使用 Hadoop 分析框架可以轻松分析 Data Lake Storage Gen1 中存储的数据。 MapReduce 或 Hive 等框架。 可将 Azure HDInsight 群集预配和配置为直接访问 Data Lake Storage Gen1 中存储的数据。

## <a name="unlimited-storage-petabyte-files"></a>无限存储空间，PB 量级的文件

Data Lake Storage Gen1 提供无限制的存储，适用于存储不同种类的数据进行分析。 它不会对帐户大小或文件大小施加限制。 或可以存储在 data lake 中的数据量。 单个文件的大小范围是从 kb 到 pb，这 Data Lake Storage Gen1 是存储任何类型数据的最佳选择。 通过创建多个副本来持久存储数据。 而且，数据在 data lake 中可存储的时间长度没有限制。

## <a name="performance-tuning-for-big-data-analytics"></a>针对大数据分析优化了性能

Data Lake Storage Gen1 是为分析系统设计的。 需要大量吞吐量才能查询和分析大量数据的系统。 Data Lake 将文件的各个部分散在多个独立的存储服务器中。 分数数据时，此设置可以改善并行读取文件时的读取吞吐量。

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>企业就绪状态：高度可用和安全

Data Lake Storage Gen1 提供符合行业标准的可用性和可靠性。 数据资产可通过创建冗余副本来长期存储，防范意外的故障。 企业可以在其解决方案中使用 Data Lake Storage Gen1 作为现有数据平台的重要组成部分。

Data Lake Storage Gen1 还为存储的数据提供企业级安全性。 有关详细信息，请参阅 [保护 Azure Data Lake Storage Gen1 中的数据](#data-security-in-data-lake-storage-gen1)。

## <a name="flexible-data-structures"></a>弹性数据结构

Data Lake Storage Gen1 可按本机格式（原样）存储任何数据，不需要事先经过转换。 加载数据之前，Data Lake Storage Gen1 不需要定义架构。 独立的分析框架在分析时会解释数据并定义架构。 Data Lake Storage Gen1 可以处理结构化数据。 和半结构化以及非结构化数据。

Data Lake Storage Gen1 的数据容器本质上是文件夹和文件。 可以使用 SDK、Azure 门户和 Azure PowerShell 来操作存储的数据。 通过这些接口和容器放入存储区中的数据可以存储任何数据类型。 Data Lake Storage Gen1 不会根据数据类型对数据进行任何特殊处理。

## <a name="data-security-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 中的数据安全性

Data Lake Storage Gen1 使用 Azure Active Directory 进行身份验证，使用访问控制列表 (ACL) 管理对数据的访问。

| **功能** | **说明** |
| --- | --- |
| 身份验证 |Data Lake Storage Gen1 与 Azure Active Directory (Azure AD) 集成，可对 Data Lake Storage Gen1 中存储的所有数据进行标识与访问管理。 因为这种集成，Data Lake Storage Gen1 可受益于所有 Azure AD 功能。 这些功能包括：多重身份验证、条件性访问和基于角色的访问控制。 此外，还可以使用应用程序监视、安全监视和警报等。 Data Lake Storage Gen1 支持在 REST 接口中使用 OAuth 2.0 协议进行身份验证。 参阅[使用 Azure Active Directory 进行 Azure Data Lake Storage Gen1 身份验证](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)。|
| 访问控制 |Data Lake Storage Gen1 通过支持 WebHDFS 协议公开的 POSIX 样式权限来提供访问控制。 可对根文件夹、子文件夹和单个文件启用 ACL。 有关 ACL 在 Data Lake Storage Gen1 上下文中的工作原理的详细信息，请参阅 [Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)。 |
| 加密 |Data Lake Storage Gen1 还针对帐户中存储的数据提供加密。 创建 Data Lake Storage Gen1 帐户时可以指定加密设置。 可以选择加密或不加密数据。 有关详细信息，请参阅 [Data Lake Storage Gen1 中的加密](../data-lake-store/data-lake-store-encryption.md)。 有关如何提供加密相关配置的说明，请参阅[通过 Azure 门户开始使用 Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md)。 |

若要详细了解如何保护 Data Lake Storage Gen1 中的数据，请参阅[保护 Azure Data Lake Storage Gen1 中存储的数据](../data-lake-store/data-lake-store-secure-data.md)。

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>与 Data Lake Storage Gen1 兼容的应用程序

Data Lake Storage Gen1 与 Hadoop 环境中大多数开源组件兼容。 此外，还与其他 Azure 服务完美集成。  请访问以下链接，了解有关如何将 Data Lake Storage Gen1 与开源组件和其他 Azure 服务一起使用的详细信息。

* 请参阅[与 Azure Data Lake Storage Gen1 一起使用的开源大数据应用程序](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)。
* 参阅[将 Azure Data Lake Storage Gen1 与其他 Azure 服务集成](../data-lake-store/data-lake-store-integrate-with-other-services.md)，了解如何将 Data Lake Storage Gen1 与其他 Azure 服务配合使用，以实现更多的方案。
* [有关大数据要求，请参阅使用 Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md)。

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 文件系统 (adl://)

在 Hadoop 环境中，可以通过新文件系统 AzureDataLakeFilesystem （adl://）访问 Data Lake Storage Gen1。 使用的应用程序和服务的性能 `adl://` 可以按照当前在 WebHDFS 中不可用的方式进行优化。 因此，你可以通过使用推荐的 adl://，灵活地获取最佳性能。 或通过继续直接使用 WebHDFS API 来维护现有代码。 Azure HDInsight 充分使用 AzureDataLakeFilesystem 来提供 Data Lake Storage Gen1 的最佳性能。

使用以下 URI 访问 Data Lake Storage Gen1 中的数据：

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

有关如何访问 Data Lake Storage Gen1 中的数据的详细信息，请参阅[可针对存储的数据执行的操作](../data-lake-store/data-lake-store-get-started-portal.md#properties)。

## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 存储简介](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen2 概述](./overview-data-lake-storage-gen2.md)
---
title: Azure Data Lake Storage Gen2 预览版的 Azure Blob Filesystem 驱动程序
description: ABFS Hadoop Filesystem 驱动程序
services: storage
keywords: ''
author: jamesbak
manager: jahogg
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: a726779e731be2534e457ba595d93fe51c023601
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034311"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Azure Blob Filesystem 驱动程序 (ABFS)：专用于 Hadoop 的 Azure 存储驱动程序

要访问 Azure Data Lake Storage Gen2 预览版中的数据，一种主要方式是通过 [Hadoop FileSystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html)。 Azure Data Lake Storage Gen2 具有相关的驱动程序：Azure Blob 文件系统驱动程序或 `ABFS`。 ABFS 是 Apache Hadoop 的一部分，Hadoop 的许多商业分发均带有此程序。 借助此驱动程序，许多应用程序和框架无需显式引用 Data Lake Storage Gen2 的服务的任何代码，即可访问 Data Lake Storage Gen2 中的数据。

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>以前的功能：Windows Azure 存储 Blob 驱动程序

Windows Azure 存储 Blob 驱动程序或 [WASB 驱动程序](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)提供对 Azure 存储 Blob 的原始支持。 此驱动程序执行复杂任务，即将文件系统语义（根据 Hadoop FileSystem 接口的要求）映射到 Azure Blob 存储公开的对象存储样式接口的语义中。 此驱动程序继续支持此模型，提供对 Blob 中存储的数据的高性能访问，但包含大量执行此映射的代码，因此很难维护。 此外，由于对象存储缺少对目录的支持，某些操作（如 [FileSystem.rename()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) 和 [FileSystem.delete()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive)）在应用到目录时需要驱动程序执行大量操作，这通常导致性能下降。

因此，为了解决 WASB 固有的设计缺陷，利用来自新版 ABFS 驱动程序的支持实现新的 Azure Data Lake Storage 服务。

## <a name="the-azure-blob-file-system-driver"></a>Azure Blob 文件系统驱动程序

[Azure Data Lake Storage REST 接口](https://docs.microsoft.com/en-us/rest/api/datalakestorage/)旨在支持 Azure Blob 存储的文件系统语义。 考虑到 Hadoop FileSystem 的目的也是支持这些语义，因此无需在驱动程序中进行复杂的映射。 这样，Azure Blob 文件系统驱动程序 (ABFS) 仅作为 REST API 的客户端填充码。

但是，驱动程序仍然必须执行一些功能：

### <a name="uri-scheme-to-reference-data"></a>引用数据的 URI 方案

与 Hadoop 中的其他 FileSystem 实现一样，ABFS 驱动程序自行定义 URI 方案，让资源（目录和文件）能够得到明确处理。 如需了解 URI 方案，请参阅[使用 Azure Data Lake Storage Gen2 URI](./introduction-abfs-uri.md)。 URI 的结构是 `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

可按上述 URI 格式使用标准 Hadoop 工具和框架引用这些资源：

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

ABFS 驱动程序在内部将 URI 中指定的资源转换为文件和目录，并使用这些引用调用 Azure Data Lake Storage REST API。

### <a name="authentication"></a>身份验证

ABFS 驱动程序目前支持共享密钥身份验证，因此 Hadoop 应用程序可安全地访问 Data Lake Storage Gen2 中所含的资源。 密钥被加密并存储在 Hadoop 配置中。

### <a name="configuration"></a>配置

ABFS 驱动程序的所有配置均存储在 <code>core-site.xml</code> 配置文件中。 在带有 [Ambari](http://ambari.apache.org/) 的 Hadoop 分发上，还可使用 Web 门户或 Ambari REST API 管理配置。

要详细了解所有受支持的配置条目，请参阅[官方 Hadoop 文档](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)。

### <a name="hadoop-documentation"></a>Hadoop 文档

要完整了解 ABFS 驱动程序，请参阅[官方 Hadoop 文档](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>后续步骤

- [设置 HDInsight 群集](./quickstart-create-connect-hdi-cluster.md)
- [创建 Azure Databricks 群集](./quickstart-create-databricks-account.md)
- [使用 Azure Data Lake Storage Gen2 URI](./introduction-abfs-uri.md)
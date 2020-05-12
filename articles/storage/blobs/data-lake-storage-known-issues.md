---
title: Azure Data Lake Storage Gen2 的已知问题 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知问题。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: e80d1a05765d224dc4682c6f64faccc8c81f8ebd
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007477"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知问题

本文介绍 Azure Data Lake Storage Gen2 的限制和已知问题。

## <a name="supported-blob-storage-features"></a>支持的 Blob 存储功能

已增加的 Blob 存储功能数现在使用具有分层命名空间的帐户。 有关完整列表，请参阅[Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支持的 Azure 服务集成

Azure Data Lake Storage Gen2 支持多个可用于引入数据、执行分析和创建可视化表示形式的 Azure 服务。 有关支持的 Azure 服务的列表，请参阅[支持 Azure Data Lake Storage Gen2 的 azure 服务](data-lake-storage-supported-azure-services.md)。

请参阅[支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)。

## <a name="supported-open-source-platforms"></a>支持的开源平台

多个开源平台支持 Data Lake Storage Gen2。 有关完整列表，请参阅[支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

请参阅[支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

## <a name="blob-storage-apis"></a>Blob 存储 API

Blob API 和 Data Lake Storage Gen2 API 可以对相同的数据执行操作。

本部分介绍使用 Blob API 和 Data Lake Storage Gen2 API 对相同的数据执行操作时存在的问题和限制。

* 不能同时使用 Blob Api 和 Data Lake Storage Api 写入文件的同一个实例。 如果使用 Data Lake Storage Gen2 API 向某个文件写入数据，则在调用[获取块列表](https://docs.microsoft.com/rest/api/storageservices/get-block-list) Blob API 时，该文件的块将不可见。 覆盖某个文件时，可以使用 Data Lake Storage Gen2 API 或 Blob API。 这不会影响文件属性。

* 如果在使用[列出 Blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 操作时不指定分隔符，则结果会包含目录和 Blob。 如果选择使用分隔符，请只使用正斜杠 (`/`)。 这是唯一支持的分隔符。

* 如果使用[删除 Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API 来删除目录，则只能在该目录为空的情况下将其删除。 这意味着，不能使用 Blob API 以递归方式删除目录。

这些 Blob REST API 不受支持：

* [放置 Blob（页）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [放置页](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [获取页面范围](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [增量复制 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [从 URL 放置页](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [放置 Blob（追加）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [追加块](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [从 URL 追加块](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

在有分层命名空间的帐户中，非托管 VM 磁盘不受支持。 若要在存储帐户中启用分层命名空间，请将非托管 VM 磁盘放到未启用分层命名空间功能的存储帐户中。

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks-powershell-and-azure-cli"></a>Sdk、PowerShell 和 Azure CLI 中的文件系统支持

- 获取和设置 ACL 的操作当前不是递归的。
- [Azure CLI](data-lake-storage-directory-file-acl-cli.md)支持提供公共预览版。


## <a name="lifecycle-management-policies"></a>生命周期管理策略

尚不支持删除 Blob 快照的功能。 

## <a name="archive-tier"></a>存档层

当前有一个影响存档访问层的 bug。

## <a name="blobfuse"></a>Blobfuse

不支持 Blobfuse。

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

仅使用最新版本的 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。不支持早期版本的 AzCopy，例如 AzCopy v8.1。

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure 存储资源管理器

仅使用  `1.6.0`   或更高版本。

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Azure 门户中的存储资源管理器

尚不支持 ACL。

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>第三方应用程序

如果将使用 REST Api 的第三方应用程序用于调用 Blob Api 的 Data Lake Storage Gen2 应用程序，则该应用程序将继续工作。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>访问控制列表（ACL）和匿名读取访问

如果已将[匿名读取访问权限](storage-manage-access-to-resources.md)授予某个容器，则 acl 不会影响该容器或该容器中的文件。

## <a name="premium-performance-block-blob-storage-accounts"></a>高级-性能块 blob 存储帐户

### <a name="diagnostic-logs"></a>诊断日志

尚未使用 Azure 门户启用诊断日志。 你可以使用 PowerShell 启用它们。 例如：

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

### <a name="lifecycle-management-policies"></a>生命周期管理策略

- 高级块 blob 存储帐户中尚不支持生命周期管理策略。 

- 不能将数据从 "高级" 层移到更低的层级。 

- 当前不支持**删除 Blob**操作。 

### <a name="hdinsight-support"></a>HDInsight 支持

创建 n HDInsight 群集时，还无法选择在其上启用了分层命名空间功能的块 blob 存储帐户。 但是，在创建群集后，可以将该帐户附加到该群集。

### <a name="dremio-support"></a>Dremio 支持

Dremio 尚未连接到在其上启用了分层命名空间功能的块 blob 存储帐户。 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure 存储 Blob （WASB）驱动程序（不受 Data Lake Storage Gen2 支持）

目前，WASB 驱动程序已设计为仅用于 Blob API，在一些常见情况下遇到问题。 具体而言，它是客户端到已启用分层命名空间的存储帐户。 Data Lake Storage 上的多协议访问不会缓解这些问题。 

对于（并且很可能是可预见的将来），我们不支持将 WASB driver 作为客户端的客户使用已启用分层命名空间的存储帐户。 相反，我们建议在 Hadoop 环境中选择使用[Azure Blob 文件系统（ABFS）](data-lake-storage-abfs-driver.md)驱动程序。 如果要尝试迁移到 Hadoop branch 之前版本的本地 Hadoop 环境，请打开 Azure 支持票证，以便我们能够与你联系，为你和你的组织提供正确的路径。

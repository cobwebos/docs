---
title: Azure Data Lake Storage Gen2 的已知问题 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知问题。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: c1e9e3d63e8a4f7fe461e2d33603da91d3d9bec6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91441780"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知问题

本文介绍 Azure Data Lake Storage Gen2 的限制和已知问题。

## <a name="supported-blob-storage-features"></a>支持的 Blob 存储功能

现在，越来越多的 Blob 存储功能开始使用具有分层命名空间的帐户。 有关完整列表，请参阅 [Azure Data Lake storage Gen2 中提供的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支持的 Azure 服务集成

Azure Data Lake Storage Gen2 支持多个可用于引入数据、执行分析和创建可视化表示形式的 Azure 服务。 有关受支持的 Azure 服务的列表，请参阅[支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)。

请参阅[支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)。

## <a name="supported-open-source-platforms"></a>支持的开源平台

多个开源平台支持 Data Lake Storage Gen2。 有关完整列表，请参阅[支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

请参阅[支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

## <a name="blob-storage-apis"></a>Blob 存储 API

Blob API 和 Data Lake Storage Gen2 API 可以对相同的数据执行操作。

本部分介绍了同时使用 blob API 和 Data Lake Storage Gen2 API 对相同的数据执行操作时的问题和限制。

* 不能同时使用 Blob API 和 Data Lake Storage API 写入到文件的同一实例。 如果使用 Data Lake Storage Gen2 API 向某个文件进行写入，则调用[获取 Block 列表](https://docs.microsoft.com/rest/api/storageservices/get-block-list) blob API 时看不到该文件的块。 覆盖某个文件时，可以使用 Data Lake Storage Gen2 API 或 Blob API。 这不会影响文件属性。

* 如果在使用[列出 Blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 操作时不指定分隔符，则结果会包含目录和 Blob。 如果选择使用分隔符，请只使用正斜杠 (`/`)。 这是唯一支持的分隔符。

* 如果使用[删除 Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API 来删除目录，则只能在该目录为空的情况下将其删除。 这意味着，不能使用 Blob API 以递归方式删除目录。

这些 Blob REST API 不受支持：

* [放置 Blob（页）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [放置页](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [获取页面范围](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [增量复制 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [从 URL 放置页](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [通过 URL 追加块](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

具有分层命名空间的帐户不支持非托管 VM 磁盘。 若要在存储帐户中启用分层命名空间，请将非托管 VM 磁盘置于未启用分层命名空间功能的存储帐户中。

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>支持以递归方式设置访问控制列表 (ACL)

在[公共预览版](recursive-access-control-lists.md)中，能够将 ACL 更改以递归方式从父目录应用到子项。 在此功能的当前版本中，你可以使用 PowerShell、.NET SDK 和 Python SDK 应用 ACL 更改。 尚不支持 Java SDK、Azure CLI、Azure 门户或 Azure 存储资源管理器。

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

请仅使用最新版本的 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。 不支持早期版本的 AzCopy，例如 AzCopy v8.1。

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure 存储资源管理器

请仅使用 `1.6.0` 或更高版本。

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Azure 门户中的存储资源管理器

目前不支持 ACL。

<a id="third-party-apps"></a>

## <a name="thirdpartyapplications"></a>第三方应用程序

对于使用 REST API 来工作的第三方应用程序，如果你将它们与调用可能会工作的 Blob API 的 Data Lake Storage Gen2 应用程序一起使用，则这些第三方应用程序会继续工作。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>访问控制列表 (ACL) 和匿名读取访问

如果已授予对某个容器的[匿名读取访问权限](storage-manage-access-to-resources.md)，则 ACL 对该容器或该容器中的文件没有影响。

## <a name="premium-performance-blockblobstorage-storage-accounts"></a>高性能 BlockBlobStorage 存储帐户

### <a name="diagnostic-logs"></a>诊断日志

目前还无法使用 Azure 门户启用诊断日志。 可以使用 PowerShell 启用它们。 例如：

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

尚不支持保留天数设置，但你可以使用任何支持的工具（例如 Azure 存储资源管理器、REST 或 SDK）手动删除日志。

### <a name="lifecycle-management-policies"></a>生命周期管理策略

- 只支持常规用途 v2 帐户的生命周期管理策略。 高级 BlockBlobStorage 存储帐户目前尚不支持。
- 不能将数据从高级层级移到较低的层级。


### <a name="hdinsight-support"></a>HDInsight 支持

创建 HDInsight 群集时，尚无法选择启用了分层命名空间功能的 BlockBlobStorage 帐户。 但是，创建群集后，可以将该帐户附加到该群集。

### <a name="dremio-support"></a>Dremio 支持

Dremio 尚且不能连接到其上启用了分层命名空间功能的 BlockBlobStorage 帐户。 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure Storage Blob (WASB) 驱动程序（不支持用于 Data Lake Storage Gen2）

目前，WASB 驱动程序设计为仅用于 Blob API，在一些常见场景中会遇到问题。 具体而言，当它是启用了分层命名空间的存储帐户的客户端时会遇到问题。 Data Lake Storage 上的多协议访问不会缓解这些问题。 

目前我们不支持客户使用 WASB 驱动程序作为启用了分层命名空间的存储帐户的客户端（在可预见的将来也很有可能是这种情况）。 因此，建议你选择在 Hadoop 环境中使用 [Azure Blob 文件系统 (ABFS)](data-lake-storage-abfs-driver.md) 驱动程序。 如果你在尝试迁移出采用 Hadoop branch-3 之前的版本的本地 Hadoop 环境，请创建一个 Azure 支持票证，这样我们就可以与你联系，确定适合你和你的组织的正确前进道路。

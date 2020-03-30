---
title: Azure Data Lake Storage Gen2 的已知问题 | Microsoft Docs
description: 了解 Azure 数据存储第 2 代的限制和已知问题。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 4f8fae6580272ed53b8d440ba3e74c6a1ed1e61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061512"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知问题

本文介绍了 Azure 数据存储第 2 代的限制和已知问题。

## <a name="supported-blob-storage-features"></a>支持的 Blob 存储功能

越来越多的 Blob 存储功能现在使用具有分层命名空间的帐户。 有关完整列表，请参阅[Azure 数据湖存储第 2 代 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支持的 Azure 服务集成

Data Lake Storage gen2 支持多个可用于引入数据、执行分析和创建可视化表示形式的 Azure 服务。 有关受支持的 Azure 服务的列表，请参阅[支持 Azure 数据存储湖存储 Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)。

请参阅[支持 Azure 数据存储第 2 代](data-lake-storage-supported-azure-services.md)的 Azure 服务。

## <a name="supported-open-source-platforms"></a>支持的开源平台

多个开源平台支持 Data Lake Storage Gen2。 有关完整列表，请参阅[支持 Azure 数据存储第 2 代 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

请参阅[支持 Azure 数据存储第 2 代 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

## <a name="blob-storage-apis"></a>Blob 存储 API

Blob API 和 Data Lake Storage Gen2 API 可以对相同的数据执行操作。

本部分介绍使用 Blob API 和 Data Lake Storage Gen2 API 对相同的数据执行操作时存在的问题和限制。

* 不能同时使用 Blob API 和 Data Lake Storage API 向文件的同一实例写入数据。 如果使用 Data Lake Storage Gen2 API 向某个文件写入数据，则在调用[获取块列表](https://docs.microsoft.com/rest/api/storageservices/get-block-list) Blob API 时，该文件的块将不可见。 覆盖某个文件时，可以使用 Data Lake Storage Gen2 API 或 Blob API。 这不会影响文件属性。

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
* [URL 中的追加块](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

在有分层命名空间的帐户中，非托管 VM 磁盘不受支持。 若要在存储帐户中启用分层命名空间，请将非托管 VM 磁盘放到未启用分层命名空间功能的存储帐户中。

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>SDK 中的文件系统支持

获取和设置 ACL 的操作当前不是递归的。

## <a name="file-system-support-in-powershell-and-azure-cli"></a>PowerShell 和 Azure CLI 中的文件系统支持

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md)和[Azure CLI](data-lake-storage-directory-file-acl-cli.md)支持处于公共预览版中。
- 获取和设置 ACL 的操作当前不是递归的。

## <a name="lifecycle-management-policies"></a>生命周期管理策略

* 尚不支持删除 Blob 快照的功能。  

## <a name="archive-tier"></a>存档层

当前存在影响存档访问层的错误。


## <a name="blobfuse"></a>Blobfuse

不支持 Blobfuse。

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

仅使用最新版本的 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。不支持早期版本的 AzCopy，例如 AzCopy v8.1。

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure 存储资源管理器

仅使用版本 `1.6.0` 或更高版本。

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Azure 门户中的存储资源管理器

尚不支持 ACL。

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>第三方应用程序

如果使用 REST API 工作的第三方应用程序，如果将其与调用 Blob API 的数据存储存储 Gen2 应用程序一起使用，则它们可能会正常工作。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>访问控制列表 （ACL） 和匿名读取访问

如果已授予对容器的[匿名读取访问权限](storage-manage-access-to-resources.md)，则 ACL 对该容器或该容器中的文件没有影响。

## <a name="windows-azure-storage-blob-wasb-driver"></a>Windows Azure 存储 Blob （WASB） 驱动程序

目前，与使用 WASB 驱动程序以及具有分层命名空间的帐户相关的几个问题。 我们建议您在工作负荷中使用[Azure Blob 文件系统 （ABFS）](data-lake-storage-abfs-driver.md)驱动程序。 






---
title: Azure Data Lake Storage Gen2 的已知问题 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知问题。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7d637c2fb3f4a4d5f8deac9cd99c0a44af6568e6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359743"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知问题

本文介绍 Azure Data Lake Storage Gen2 的限制和已知问题。

## <a name="supported-blob-storage-features"></a>支持的 Blob 存储功能

已增加的 Blob 存储功能数现在使用具有分层命名空间的帐户。 有关完整列表，请参阅[Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支持的 Azure 服务集成

Data Lake Storage gen2 支持多个可用于引入数据、执行分析和创建可视化表示形式的 Azure 服务。 有关支持的 Azure 服务的列表，请参阅[支持 Azure Data Lake Storage Gen2 的 azure 服务](data-lake-storage-supported-azure-services.md)。

请参阅[支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)。

## <a name="supported-open-source-platforms"></a>支持的开源平台

多个开源平台支持 Data Lake Storage Gen2。 有关完整列表，请参阅[支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

请参阅[支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

## <a name="blob-storage-apis"></a>Blob 存储 API

Blob Api 和 Data Lake Storage Gen2 Api 可以对相同的数据执行操作。

本部分介绍使用 blob Api 和 Data Lake Storage Gen2 Api 对相同数据进行操作时的问题和限制。

* 不能同时使用 Blob Api 和 Data Lake Storage Api 写入文件的同一个实例。 如果使用 Data Lake Storage Gen2 Api 写入文件，则调用[获取块列表](https://docs.microsoft.com/rest/api/storageservices/get-block-list)blob API 时，将看不到该文件的块。 可以使用 Data Lake Storage Gen2 Api 或 Blob Api 覆盖文件。 这不会影响文件属性。

* 如果在不指定分隔符的情况下使用 "[列出 blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) " 操作，结果将包含目录和 blob。 如果选择使用分隔符，则只使用正斜杠（`/`）。 这是唯一受支持的分隔符。

* 如果使用[删除 Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API 来删除目录，则仅当该目录为空时才会将其删除。 这意味着不能以递归方式使用 Blob API 删除目录。

不支持这些 Blob REST Api：

* [放置 Blob （页）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [放置页](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [获取页面范围](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [增量复制 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [从 URL 放置页面](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [放置 Blob （附加）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [追加块](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [将块追加到 URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

具有分层命名空间的帐户不支持非托管 VM 磁盘。 如果要在存储帐户上启用分层命名空间，请将非托管 VM 磁盘置于未启用分层命名空间功能的存储帐户中。

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Sdk 中的文件系统支持

- [.Net](data-lake-storage-directory-file-acl-dotnet.md)、 [Java](data-lake-storage-directory-file-acl-java.md)和[Python](data-lake-storage-directory-file-acl-python.md)以及[JavaScript](data-lake-storage-directory-file-acl-javascript.md)和支持都是公开预览版。 当前不支持其他 Sdk。
- Get 和 set ACL 操作当前不是递归的。

## <a name="file-system-support-in-powershell-and-azure-cli"></a>PowerShell 和 Azure CLI 中的文件系统支持

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md)和[Azure CLI](data-lake-storage-directory-file-acl-cli.md)支持公共预览版。
- Get 和 set ACL 操作当前不是递归的。

## <a name="lifecycle-management-policies"></a>生命周期管理策略

* 尚不支持删除 blob 快照。  

* 当前有一些影响生命周期管理策略和存档访问层的 bug。 

## <a name="diagnostic-logs"></a>诊断日志

Azure 存储资源管理器 1.10. x 不能用于查看诊断日志。 若要查看日志，请使用 AzCopy 或 Sdk。

## <a name="blobfuse"></a>Blobfuse

不支持 Blobfuse。



<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

仅使用最新版本的 AzCopy （[AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)）。 不支持早期版本的 AzCopy （如 AzCopy）。

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure 存储资源管理器

仅使用  或更高版本 `1.6.0`。当前存在影响版本 `1.11.0` 的存储 bug，这可能会导致某些情况下出现身份验证错误。 即将推出存储 bug 的修补程序，但作为一种解决方法，我们建议你使用版本 `1.10.x` 可 [免费下载](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes)。 `1.10.x` 不受存储 bug 的影响。

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>在 Azure 门户中存储资源管理器

目前尚不支持 Acl。

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>第三方应用程序

如果将使用 REST Api 的第三方应用程序用于调用 Blob Api 的 Data Lake Storage Gen2 应用程序，则该应用程序将继续工作。






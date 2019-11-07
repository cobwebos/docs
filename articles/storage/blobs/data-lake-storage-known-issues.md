---
title: Azure Data Lake Storage Gen2 的已知问题 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知问题
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 95f2dbdbb34ff349d14be430b4e5a4fa84df0f5a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581490"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知问题

本文列出了使用分层命名空间的存储帐户 (Azure Data Lake Storage Gen2) 尚不支持或者仅部分支持的功能与工具。

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>使用 Blob Api 的问题和限制

Blob Api 和 Data Lake Storage Gen2 Api 可以对相同的数据执行操作。

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
* [从 URL 追加块](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

在有分层命名空间的帐户中，非托管 VM 磁盘不受支持。 若要在存储帐户中启用分层命名空间，请将非托管 VM 磁盘放到未启用分层命名空间功能的存储帐户中。

## <a name="support-for-other-blob-storage-features"></a>支持其他 Blob 存储功能

下表列出了使用分层命名空间的存储帐户 (Azure Data Lake Storage Gen2) 尚不支持或者仅部分支持的所有其他功能与工具。

| 功能/工具    | 详细信息    |
|--------|-----------|
| **Data Lake Storage Gen2 Api** | 部分支持 <br><br>在当前版本中，你可以使用 Data Lake Storage Gen2 **REST** api 与目录交互和设置访问控制列表（acl），但没有其他 sdk （例如： .Net、Java 或 Python）来执行这些任务。 若要执行其他任务，如上传和下载文件，你可以使用 Blob Sdk。  |
| **AzCopy** | 特定于版本的支持 <br><br>仅使用最新版本的 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。 不支持早期版本的 AzCopy，例如 AzCopy v8.1。|
| **Azure Blob 存储生命周期管理策略** | 支持所有访问层。 存档访问层目前处于预览阶段。 尚不支持删除 Blob 快照的功能。 |
| **Azure 内容分发网络（CDN）** | 尚不支持|
| **Azure 搜索** |支持（预览）|
| **Azure 存储资源管理器** | 特定于版本的支持 <br><br>仅使用版本 `1.6.0` 或更高版本。 <br>版本 `1.6.0` 可[免费下载](https://azure.microsoft.com/features/storage-explorer/)。|
| **Blob 容器 ACL** |尚不支持|
| **Blobfuse** |尚不支持|
| **自定义域** |尚不支持|
| **Azure 门户中的存储资源管理器** | 有限支持。 目前尚不支持 Acl。 |
| **诊断日志记录** |支持诊断日志（预览）。<br><br>目前不支持在 Azure 门户中启用日志。 以下示例演示如何使用 PowerShell 来启用日志。 <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`。 <br><br>请务必将 `Blob` 指定为 `-ServiceType` 参数的值，如此示例所示。 <br><br>目前，Azure 存储资源管理器不能用于查看诊断日志。 若要查看日志，请使用 AzCopy 或 SDK。
| **不可变存储** |尚不支持 <br><br>使用不可变存储可以 [WORM（一次写入，多次读取）](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)状态存储数据。|
| **对象级层** |支持冷层和存档层。 存档层处于预览阶段。 所有其他访问层目前均不受支持。|
| **Powershell 和 CLI 支持** | 受限功能 <br><br>支持 Blob 操作。 处理目录以及设置访问控制列表 (ACL) 的功能尚不受支持。 |
| **静态网站** |尚不支持 <br><br>具体而言，是将文件提供给[静态网站](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)的功能。|
| **第三方应用程序** | 有限支持 <br><br>对于使用 REST API 保持正常运行的第三方应用程序，如果在 Data Lake Storage Gen2 中使用这些应用程序，则它们可继续正常运行。 <br>调用 Blob Api 的应用程序可能会起作用。|
|**软删除** |尚不支持|
| **版本控制功能** |尚不支持 <br><br>这包括[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)和其他版本控制功能，例如[快照](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)。|



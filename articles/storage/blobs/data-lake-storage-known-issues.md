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
ms.openlocfilehash: 3ea77eb5dd8a03f877164179e3accc3a6f6d0aef
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74548317"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知问题

本文列出了尚不支持的功能和工具，或仅部分支持具有分层命名空间（Azure Data Lake Storage Gen2）的存储帐户。

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>使用 Blob Api 的问题和限制

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

## <a name="api-scope-for-data-lake-storage-client-library-for-sdk-powershell-and-cli"></a>用于 SDK、Powershell 和 CLI 的 Data Lake Storage 客户端库的 API 范围

### <a name="filesystem-support-in-sdks"></a>Sdk 中的文件系统支持

- .NET、Java 和 Python 均为公共预览版。 当前不支持其他 Sdk。
- Get 和 Set Acl 操作当前不是递归的。

### <a name="filesystem-support-in-powershell-and-azure-cli"></a>PowerShell 和 Azure CLI 中的文件系统支持

Get 和 Set Acl 操作当前不是递归的。

## <a name="support-for-other-blob-storage-features"></a>支持其他 Blob 存储功能

下表列出了所有其他不受支持的功能和工具，或仅部分支持具有分层命名空间（Azure Data Lake Storage Gen2）的存储帐户。

| 功能/工具    | 详细信息    |
|--------|-----------|
| **AzCopy** | 特定于版本的支持 <br><br>仅使用最新版本的 AzCopy （[AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)）。 不支持早期版本的 AzCopy （如 AzCopy）。|
| **Azure Blob 存储生命周期管理策略** | 支持生命周期管理策略（预览版）。  支持所有访问层。 存档访问层目前处于预览阶段。 尚不支持删除 blob 快照。 <br><br> 当前有一些影响生命周期管理策略和存档访问层的 bug。  在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)注册生命周期管理策略的预览并存档访问层。   |
| **Azure 内容分发网络（CDN）** | 尚不支持|
| **Azure 搜索** |支持（预览）|
| **Azure 存储空间资源管理器** | 特定于版本的支持。 <br><br>仅使用 `1.6.0` 或更高版本的版本。 <br> 当前存在影响版本 `1.11.0` 的存储 bug，在某些情况下可能会导致身份验证错误。 即将推出存储 bug 的修补程序，但作为一种解决方法，我们建议你使用可[免费下载](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes)的版本 `1.10.x`。 `1.10.x` 不受存储 bug 的影响。|
| **Blob 容器 Acl** |尚不支持|
| **Blobfuse** |尚不支持|
| **自定义域** |尚不支持|
| **Azure 门户中的存储资源管理器** | 有限支持。 目前尚不支持 Acl。 |
| **诊断日志记录** |支持诊断日志（预览）。<br><br>当前不支持在 Azure 门户中启用日志。 下面是如何使用 PowerShell 启用日志的示例。 <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`。 <br><br>请确保指定 `Blob` 作为 `-ServiceType` 参数的值，如本示例中所示。 <br><br>目前，Azure 存储资源管理器不能用于查看诊断日志。 若要查看日志，请使用 AzCopy 或 Sdk。
| **不可变存储** |尚不支持 <br><br>不可变的存储提供了将数据存储在[蠕虫（写入一次，读取多个）](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)状态的能力。|
| **对象级层** |支持冷层和存档层。 存档层处于预览阶段。 目前尚不支持所有其他访问层。 <br><br> 当前有一些影响存档访问层的 bug。  在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)注册存档访问层的预览。|
| **Powershell 和 CLI 支持** | 功能受限 <br><br>支持 Blob 操作。 尚不支持使用目录和设置访问控制列表（Acl）。 |
| **静态网站** |尚不支持 <br><br>具体而言，是将文件提供给[静态网站](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)的功能。|
| **第三方应用程序** | 有限支持 <br><br>如果将使用 REST Api 的第三方应用程序与 Data Lake Storage Gen2 一起使用，则该应用程序将继续正常工作。 <br>调用 Blob Api 的应用程序可能会起作用。|
|**软删除** |尚不支持|
| **版本控制功能** |尚不支持 <br><br>这包括[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)和其他版本控制功能，如[快照](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)。|



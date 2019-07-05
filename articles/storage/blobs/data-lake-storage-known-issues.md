---
title: Azure Data Lake Storage Gen2 的已知问题 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知问题
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: daf9199104047f714d568bd2796490b836243952
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443239"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知问题

本文列出了使用分层命名空间的存储帐户 (Azure Data Lake Storage Gen2) 尚不支持或者仅部分支持的功能与工具。

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Blob 存储 API

Blob 存储 Api 已禁用以阻止功能可操作性出现问题，因为 Blob 存储 Api 尚不可使用 Azure Data Lake 第 2 代 Api 可互操作。

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>对于现有工具、应用程序和服务要采取的措施

如果上述任何组件使用 Blob API，并且你希望使用它们处理上传到帐户的所有内容，则在 Blob API 与 Azure Data Lake Gen2 API 实现互操作之前，请勿在 Blob 存储帐户中启用分层命名空间。

使用未启用分层命名空间的存储帐户意味着无法访问 Data Lake Storage Gen2 特定的功能，例如目录和文件系统访问控制列表。

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>对于非托管虚拟机 (VM) 磁盘要采取的措施

这些组件依赖于被禁用的 Blob 存储 API，因此，若要在存储帐户中启用分层命名空间，请考虑将其放到未启用分层命名空间功能的存储帐户中。

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>如果在禁用 Blob API 之前已使用 Blob API 上传了数据，该怎么办

如果你在这些 API 被禁用之前使用了它们来加载数据，并且生产环境需要访问该数据，请联系 Microsoft 支持部门并提供以下信息：

> [!div class="checklist"]
> * 订阅 ID（GUID，不是名称）。
> * 存储帐户名称。
> * 在生产环境中是否频繁受到影响，如果是，针对的是哪些存储帐户？
> * 即使在生产环境中未频繁受到影响，也请告诉我们你是否出于某种原因需要将该数据复制到另一个存储帐户，如果是，为什么？

在这些情况下，我们可以在有限的时间段内恢复对 Blob API 的访问权限，以便你可以将此数据复制到未启用分层命名空间功能的存储帐户。

## <a name="all-other-features-and-tools"></a>所有其他功能和工具

下表列出了使用分层命名空间的存储帐户 (Azure Data Lake Storage Gen2) 尚不支持或者仅部分支持的所有其他功能与工具。

| 功能/工具    | 详细信息    |
|--------|-----------|
| **适用于 Data Lake Storage Gen2 存储帐户的 API** | 部分支持 <br><br>可以使用 Data Lake Storage Gen2 **REST** API，但其他 Blob SDK（例如 .NET、Java、Python SDK）中的 API 尚不可用。|
| **AzCopy** | 特定于版本的支持 <br><br>仅使用最新版本的 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。 不支持早期版本的 AzCopy，例如 AzCopy v8.1。|
| **Azure Blob 存储生命周期管理策略** | 尚不支持 |
| **Azure 内容分发网络 (CDN)** | 尚不支持|
| **Azure 搜索** |尚不支持|
| **Azure 存储资源管理器** | 特定于版本的支持 <br><br>仅使用版本 `1.6.0` 或更高版本。 <br>版本 `1.6.0` 可[免费下载](https://azure.microsoft.com/features/storage-explorer/)。|
| **Blob 容器 ACL** |尚不支持|
| **Blobfuse** |尚不支持|
| **自定义域** |尚不支持|
| **诊断日志** |尚不支持|
| **文件系统资源管理器** | 有限支持 |
| **不可变存储** |尚不支持 <br><br>使用不可变存储可以 [WORM（一次写入，多次读取）](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)状态存储数据。|
| **对象级层** |尚不支持 <br><br>例如：Premium、 热、 冷和存档层。|
| **Powershell 和 CLI 支持** | 受限功能 <br><br>可以使用 Powershell 或 CLI 创建帐户。 无法针对文件系统、目录和文件执行操作或设置访问控制列表。|
| **静态网站** |尚不支持 <br><br>具体而言，能够满足文件复制到[静态网站](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)。|
| **第三方应用程序** | 有限支持 <br><br>对于使用 REST API 保持正常运行的第三方应用程序，如果在 Data Lake Storage Gen2 中使用这些应用程序，则它们可继续正常运行。 <br>如果应用程序使用 Blob API，在 Data Lake Storage Gen2 中使用该应用程序很可能会出现问题。 有关详细信息，请参阅本文的[为 Data Lake Storage Gen2 存储帐户禁用了 Blob 存储 API](#blob-apis-disabled) 部分。|
| **版本控制功能** |尚不支持 <br><br>这包括[快照](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)和[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)。|
|


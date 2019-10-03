---
title: 使用 .NET 的 Azure 存储示例 | Microsoft Docs
description: 查看、下载和运行 Azure 存储的示例代码和应用程序 使用 .NET 存储客户端库发现 Blob、队列、表和文件的入门示例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 57249bd2fa4d3c8aefe19a85ec9a2b6b584b00d2
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743956"
---
# <a name="azure-storage-samples-using-net"></a>使用 .NET 的 Azure 存储示例

下表概述了示例存储库和每个示例中涉及的场景。 单击链接可查看 GitHub 中相应的示例代码。

## <a name="blob-samples"></a>Blob 示例

| **方案** | **示例代码** |
|--------------|-----------------|
| 追加 Blob | [Blob 入门](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| 块 blob | [Azure Blob Storage Photo Gallery Web Application](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs)（Azure Blob 存储照片库 Web 应用程序） |
| 客户端加密 | [Blob 加密示例](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| 复制 Blob | [Blob 入门](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 创建容器 | [Azure Blob Storage Photo Gallery Web Application](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs)（Azure Blob 存储照片库 Web 应用程序） |
| 删除 Blob | [Azure Blob Storage Photo Gallery Web Application](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs)（Azure Blob 存储照片库 Web 应用程序） |
| 删除容器 | [Blob 入门](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Blob 元数据/属性/统计信息 | [Blob 入门](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 容器 ACL/元数据/属性 | [Azure Blob Storage Photo Gallery Web Application](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs)（Azure Blob 存储照片库 Web 应用程序） |
| 获取页面范围 | [Blob 入门](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 租赁 Blob/容器 | [Blob 入门](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 列出 Blob/容器 | [Blob 入门](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| 页 blob | [Blob 入门](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [Blob 入门](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 服务属性 | [Blob 入门](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 快照 Blob | [使用增量快照备份 Azure 虚拟机磁盘](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples"></a>文件示例

| **方案** | **示例代码** |
|--------------|-----------------|
| 创建共享/目录/文件 | [Azure 存储 .NET 文件存储示例](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 删除共享/目录/文件 | [Getting Started with Azure File Service in .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs)（.NET 中 Azure 文件服务入门） |
| 目录属性/元数据 | [Azure 存储 .NET 文件存储示例](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 下载文件 | [Azure 存储 .NET 文件存储示例](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 文件属性/元数据/指标 | [Azure 存储 .NET 文件存储示例](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 文件服务属性 | [Azure 存储 .NET 文件存储示例](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 列出目录和文件 | [Azure 存储 .NET 文件存储示例](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 列出共享 | [Azure 存储 .NET 文件存储示例](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 共享属性/元数据/统计信息 | [Azure 存储 .NET 文件存储示例](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples"></a>队列示例

| **方案** | **示例代码** |
|--------------|-----------------|
| 添加消息 | [Getting Started with Azure Queue Service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs)（.NET 中 Azure 队列服务入门） |
| 客户端加密 | [Azure 存储 .NET 队列客户端加密](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| 创建队列 | [Getting Started with Azure Queue Service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs)（.NET 中 Azure 队列服务入门） |
| 删除消息/队列 | [Getting Started with Azure Queue Service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs)（.NET 中 Azure 队列服务入门） |
| 扫视消息 | [Getting Started with Azure Queue Service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs)（.NET 中 Azure 队列服务入门） |
| 队列 ACL/元数据/统计信息 | [Getting Started with Azure Queue Service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs)（.NET 中 Azure 队列服务入门） |
| 队列服务属性 | [Getting Started with Azure Queue Service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs)（.NET 中 Azure 队列服务入门） |
| 更新消息 | [Getting Started with Azure Queue Service in .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs)（.NET 中 Azure 队列服务入门） |

## <a name="table-samples"></a>表示例

| **方案** | **示例代码** |
|--------------|-----------------|
| 创建表 | [Managing Concurrency using Azure Storage - Sample Application](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262)（使用 Azure 存储管理并发 - 示例应用程序） |
| 删除实体/表 | [.NET 中的 Azure 表存储入门](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| 插入/合并/替换实体 | [Managing Concurrency using Azure Storage - Sample Application](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262)（使用 Azure 存储管理并发 - 示例应用程序） |
| 查询实体 | [.NET 中的 Azure 表存储入门](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| 查询表 | [.NET 中的 Azure 表存储入门](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| 表 ACL/属性 | [.NET 中的 Azure 表存储入门](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| 更新实体 | [Managing Concurrency using Azure Storage - Sample Application](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262)（使用 Azure 存储管理并发 - 示例应用程序） |

## <a name="azure-code-samples-library"></a>Azure 代码示例库

若要查看完整的示例库，请转到 [Azure 代码示例](https://azure.microsoft.com/resources/samples/?service=storage)库，其中提供了可下载和在本地运行的 Azure 存储示例。 代码示例库提供的示例代码格式为 .zip。 或者，可浏览和克隆其 GitHub 存储库来了解每个示例。

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>入门指南

如果要查找有关如何安装和开始使用 Azure 存储客户端库的说明，请查看以下指南。

* [.NET 中 Azure Blob 服务入门](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Getting Started with Azure Queue Service in .NET](../storage-dotnet-how-to-use-queues.md)（.NET 中 Azure 队列服务入门）
* [.NET 中 Azure 表服务入门](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Getting Started with Azure File Service in .NET](../storage-dotnet-how-to-use-files.md)（.NET 中 Azure 文件服务入门）

## <a name="next-steps"></a>后续步骤

了解有关其他语言的示例的信息：

* Java:[使用 Java 的 Azure 存储示例](storage-samples-java.md)
* Python:[使用 Python 的 Azure 存储示例](storage-samples-python.md)
* 所有其他语言：[Azure 存储示例](../storage-samples.md)

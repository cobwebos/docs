---
title: 使用 Java 的 Azure 存储示例 | Microsoft 文档
description: 查看、下载和运行 Azure 存储的示例代码和应用程序 使用 Java 存储客户端库发现 Blob、队列、表和文件的入门示例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: dfdde1ae981dcd2d539dec3667e44e90cef4d1c8
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748045"
---
# <a name="azure-storage-samples-using-java"></a>使用 Java 的 Azure 存储示例

下表概述了示例存储库和每个示例中涉及的场景。 单击链接可查看 GitHub 中相应的示例代码。

> [!NOTE]
> 这些示例使用 Azure 存储 Java v11 库。 有关 v12 代码，请参阅 GitHub 存储库中的[示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)。

## <a name="blob-samples-v11"></a>Blob 示例 (v11)

| **方案** | **示例代码** |
|--------------|-----------------|
| 追加 Blob | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 块 blob | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 客户端加密 | [Java 中 Azure 客户端加密入门](https://github.com/Azure-Samples/storage-java-client-side-encryption) |
| 复制 Blob | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 创建容器 | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 删除 Blob | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 删除容器 | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Blob 元数据/属性/统计信息 | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| 容器 ACL/元数据/属性 | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| 获取页面范围 | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399) |
| 租赁 Blob/容器 | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 列出 Blob/容器 | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 页 blob | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| SAS | [SAS 测试示例](https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513) |
| 服务属性 | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| 快照 Blob | [Java 中 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |

## <a name="file-samples-v11"></a>文件示例 (v11)

| **方案** | **示例代码** |
|--------------|-----------------|
| 创建共享/目录/文件 | [Java 中 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 删除共享/目录/文件 | [Java 中 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 目录属性/元数据 | [Java 中 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| 下载文件 | [Java 中 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 文件属性/元数据/指标 | [Java 中 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| 文件服务属性 | [Java 中 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| 列出目录和文件 | [Java 中 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 列出共享 | [Java 中 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 共享属性/元数据/统计信息 | [Java 中 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |

## <a name="queue-samples-v11"></a>队列示例 (v11)

| **方案** | **示例代码** |
|--------------|-----------------|
| 添加消息 | [Java 中 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63) |
| 客户端加密 | [Java 中 Azure 客户端加密入门](https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java) |
| 创建队列 | [Java 中 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| 删除消息/队列 | [Java 中 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| 扫视消息 | [Java 中 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| 队列 ACL/元数据/统计信息 | [Java 中 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| 队列服务属性 | [Java 中 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| 更新消息 | [Java 中 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java)
|
## <a name="table-samples-v11"></a>表示例 (v11)

| **方案** | **示例代码** |
|--------------|-----------------|
| 创建表 | [Java 中 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 删除实体/表 | [Java 中 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 插入/合并/替换实体 | [Java 中 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 查询实体 | [Java 中 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 查询表 | [Java 中 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 表 ACL/属性 | [Java 中 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java) |
| 更新实体 | [Java 中 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
## <a name="azure-code-samples-library"></a>Azure 代码示例库

若要查看完整的示例库，请转到 [Azure 代码示例](https://azure.microsoft.com/resources/samples/?service=storage)库，其中提供了可下载和在本地运行的 Azure 存储示例。 代码示例库提供的示例代码格式为 .zip。 或者，可浏览和克隆其 GitHub 存储库来了解每个示例。

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>入门指南

如果要查找有关如何安装和开始使用 Azure 存储客户端库的说明，请查看以下指南。

* [Java 中 Azure Blob 服务入门](../blobs/storage-quickstart-blobs-java.md)
* [Java 中 Azure 队列服务入门](../queues/storage-java-how-to-use-queue-storage.md)
* [Java 中 Azure 表服务入门](../../cosmos-db/table-storage-how-to-use-java.md)
* [Java 中 Azure 文件服务入门](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>后续步骤

了解有关其他语言的示例的信息：

* .NET:[使用 .NET 的 Azure 存储示例](storage-samples-dotnet.md)
* JavaScript/Node.js：[使用 JavaScript 的 Azure 存储示例](storage-samples-javascript.md)
* Python:[使用 Python 的 Azure 存储示例](storage-samples-python.md)
* 所有其他语言：[Azure 存储示例](storage-samples.md)

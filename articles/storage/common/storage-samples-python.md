---
title: 使用 Python 的 Azure 存储示例 | Microsoft Docs
description: 查看、下载和运行 Azure 存储的示例代码和应用程序 使用 Python 存储客户端库发现 Blob、队列、表和文件的入门示例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 7f694ee51989023a3e7a72f40700edcbb6a97bae
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747991"
---
# <a name="azure-storage-samples-using-python"></a>使用 Python 的 Azure 存储示例

下表概述了示例存储库和每个示例中涉及的方案。 单击链接可查看 GitHub 中相应的示例代码。

> [!NOTE]
> 这些示例使用 Azure 存储 Python v2.1 库。 有关 v12 代码，请参阅 GitHub 存储库中的[示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)。

## <a name="blob-samples-v21"></a>Blob 示例 (v2.1)

| **方案** | **示例代码** |
|--------------|-----------------|
| 追加 Blob | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| 块 blob | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| 客户端加密 | [通过 Python 管理 Azure Key Vault 中的存储帐户密钥](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| 复制 Blob | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| 创建容器 | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| 删除 Blob | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| 删除容器 | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| Blob 元数据/属性/统计信息 | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| 容器 ACL/元数据/属性 | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| 获取页面范围 | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| 租赁 Blob/容器 | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| 列出 Blob/容器 | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| 页 blob | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [Python 中的共享访问签名](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| 服务属性 | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| 快照 Blob | [Python 中的 Azure Blob 服务入门](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples-v21"></a>文件示例 (v2.1)

| **方案** | **示例代码** |
|--------------|-----------------|
| 创建共享/目录/文件 | [Python 中的 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| 删除共享/目录/文件 | [Python 中的 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| 目录属性/元数据 | [Python 中的 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| 下载文件 | [Python 中的 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| 文件属性/元数据/指标 | [Python 中的 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| 文件服务属性 | [Python 中的 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| 列出目录和文件 | [Python 中的 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| 列出共享 | [Python 中的 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| 共享属性/元数据/统计信息 | [Python 中的 Azure 文件服务入门](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples-v21"></a>队列示例 (v2.1)

| **方案** | **示例代码** |
|--------------|-----------------|
| 添加消息 | [Python 中的 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| 客户端加密 | [通过 Python 管理 Azure Key Vault 中的存储帐户密钥](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| 创建队列 | [Python 中的 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| 删除消息/队列 | [Python 中的 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| 扫视消息 | [Python 中的 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| 队列 ACL/元数据/统计信息 | [Python 中的 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| 队列服务属性 | [Python 中的 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| 更新消息 | [Python 中的 Azure 队列服务入门](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples-v21"></a>表示例 (v2.1)

| **方案** | **示例代码** |
|--------------|-----------------|
| 创建表 | [Python 中的 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| 删除实体/表 | [Python 中的 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| 插入/合并/替换实体 | [Python 中的 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| 查询实体 | [Python 中的 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| 查询表 | [Python 中的 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| 表 ACL/属性 | [Python 中的 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| 更新实体 | [Python 中的 Azure 表服务入门](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Azure 代码示例库

若要查看完整的示例库，请转到 [Azure 代码示例](https://azure.microsoft.com/resources/samples/?service=storage)库，其中提供了可下载和在本地运行的 Azure 存储示例。 代码示例库提供的示例代码格式为 .zip。 或者，可浏览和克隆其 GitHub 存储库来了解每个示例。

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>入门指南

如果要查找有关如何安装和开始使用 Azure 存储客户端库的说明，请查看以下指南。

* [Python 中的 Azure Blob 服务入门](../blobs/storage-quickstart-blobs-python.md)
* [Python 中的 Azure 队列服务入门](../queues/storage-python-how-to-use-queue-storage.md)
* [Python 中的 Azure 表服务入门](../../cosmos-db/table-storage-how-to-use-python.md)
* [Python 中的 Azure 文件服务入门](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>后续步骤

了解有关其他语言的示例的信息：

* .NET:[使用 .NET 的 Azure 存储示例](storage-samples-dotnet.md)
* Java:[使用 Java 的 Azure 存储示例](storage-samples-java.md)
* JavaScript/Node.js：[使用 JavaScript 的 Azure 存储示例](storage-samples-javascript.md)
* 所有其他语言：[Azure 存储示例](storage-samples.md)

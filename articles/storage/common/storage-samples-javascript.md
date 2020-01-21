---
title: 使用 JavaScript 的 Azure 存储示例 | Microsoft 文档
description: 查看、下载和运行 Azure 存储的示例代码和应用程序 使用 JavaScript/Node.js 存储客户端库发现 Blob、队列、表和文件的入门示例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/26/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 44fe68b8b04a1192c928e04c7d2a9d147f400130
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748291"
---
# <a name="azure-storage-samples-using-javascript"></a>使用 JavaScript 的 Azure 存储示例

下表概述了示例存储库和每个示例中涉及的方案。 单击链接可查看 GitHub 中相应的示例代码。

> [!NOTE]
> 这些示例使用 Azure 存储 JavaScript v10 库。 有关 v12 代码，请参阅 GitHub 存储库中的[示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)。

## <a name="blob-samples-v10"></a>Blob 示例 (v10)

| **方案** | **示例代码** |
|--------------|-----------------|
| 块 blob | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L43) |
| 客户端加密 | [通过 JavaScript 管理 Azure Key Vault 中的存储帐户密钥](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| 复制 Blob | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L73) |
| 创建容器 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L54) |
| 删除 Blob | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L103) |
| 删除容器 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L110) |
| Blob 元数据 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L538) |
| Blob 属性 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L478) |
| 容器 ACL | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L444) |
| 容器元数据 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L409) |
| 容器属性 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L377) |
| 获取页面范围 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L170) |
| 租用 Blob | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L216) |
| 租赁容器 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L185) |
| 列出 Blob/容器 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L134) |
| 页 blob | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L129) |
| SAS | [JavaScript 中的共享访问签名](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L257) |
| 服务属性 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L308) |
| 设置 Cors 规则 | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L152) |
| 快照 Blob | [在 JavaScript 中开始使用 Azure Blob 服务](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L79) |

## <a name="file-samples-v10"></a>文件示例 (v10)

| **方案** | **示例代码** |
|--------------|-----------------|
| 创建共享/目录/文件 | [在 JavaScript 中开始使用 Azure 文件服务](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L97) |
| 删除共享/目录/文件 | [在 JavaScript 中开始使用 Azure 文件服务](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L135) |
| 下载文件 | [在 JavaScript 中开始使用 Azure 文件服务](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L128) |
| 列出目录和文件 | [在 JavaScript 中开始使用 Azure 文件服务](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L115) |
| 列出共享 | [在 JavaScript 中开始使用 Azure 文件服务](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L187) |

## <a name="queue-samples-v10"></a>队列示例 (v10)

| **方案** | **示例代码** |
|--------------|-----------------|
| 添加消息 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L142) |
| 客户端加密 | [通过 JavaScript 管理 Azure Key Vault 中的存储帐户密钥](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| 创建队列 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L57) |
| 删除消息 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L164) |
| 删除队列 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L203) |
| 列出队列 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L111) |
| 扫视消息 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L170) |
| 队列 ACL | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L192) |
| 队列 Cors 规则 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L55) |
| 队列元数据 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L161) |
| 队列服务属性 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L94) |
| 队列统计信息 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L149) |
| 更新消息 | [在 JavaScript 中开始使用 Azure 队列服务](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L176) |

## <a name="table-samples-v10"></a>表示例 (v10)

| **方案** | **示例代码** |
|--------------|-----------------|
| Batch 实体 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87) |
| 创建表 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41) |
| 删除实体/表 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67) |
| 插入/合并/替换实体 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |
| 列出表 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63) |
| 查询实体 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59) |
| 查询表 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140) |
| 范围查询 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102) |
| SAS | [JavaScript 中的共享访问签名](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87) |
| 表 ACL | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255) |
| 表 Cors 规则 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149) |
| 表的属性 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188) |
| 表统计信息 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243) |
| 更新实体 | [在 JavaScript 中开始使用 Azure 表服务](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |

## <a name="azure-code-samples-library"></a>Azure 代码示例库

若要查看完整的示例库，请转到 [Azure 代码示例](https://azure.microsoft.com/resources/samples/?service=storage)库，其中提供了可下载和在本地运行的 Azure 存储示例。 代码示例库提供的示例代码格式为 .zip。 或者，可浏览和克隆其 GitHub 存储库来了解每个示例。

[!INCLUDE [storage-node-samples-include](../../../includes/storage-node-samples-include.md)]

## <a name="getting-started-guides"></a>入门指南

如果要查找有关如何安装和开始使用 Azure 存储客户端库的说明，请查看以下指南。

* [在 JavaScript 中开始使用 Azure Blob 服务](../blobs/storage-quickstart-blobs-nodejs.md)
* [在 JavaScript 中开始使用 Azure 队列服务](../queues/storage-nodejs-how-to-use-queues.md)
* [在 JavaScript 中开始使用 Azure 表服务](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>后续步骤

了解有关其他语言的示例的信息：

* .NET:[使用 .NET 的 Azure 存储示例](storage-samples-dotnet.md)
* Java:[使用 Java 的 Azure 存储示例](storage-samples-java.md)
* Python:[使用 Python 的 Azure 存储示例](storage-samples-python.md)
* 所有其他语言：[Azure 存储示例](storage-samples.md)

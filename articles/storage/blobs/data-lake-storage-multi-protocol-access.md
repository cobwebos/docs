---
title: Azure Data Lake Storage 上的多协议访问 |Microsoft Docs
description: 使用 Blob API 和将 Blob API 与 Azure Data Lake Storage Gen2 配合使用的应用程序。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f3e852d178a6720f8d567f4ecb042f0b83bcd56c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583146"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage 上的多协议访问

Blob API 现在兼容有分层命名空间的帐户。 这可以将工具、应用程序和服务的生态系统和多个 Blob 存储功能与具有分层命名空间的帐户解除锁定。

你可能一直必须保留独立的存储解决方案进行对象存储和分析存储，直到最近这种情况才发生改变。 这是因为 Azure Data Lake Storage Gen2 的生态系统支持有限。 它访问 Blob 服务功能（例如诊断日志记录）的权限也有限。 分段存储解决方案难以维护，因为必须在帐户之间移动数据才能完成各种方案。 你再也不需要这样做了。

通过 Data Lake Storage 上的多协议访问，你可以使用工具、应用程序和服务的生态系统来处理数据。 这还包括第三方工具和应用程序。 可以将它们指向具有分层命名空间的帐户，不需修改它们。 这些应用程序即使在调用 Blob API 的情况下也“按原样”运行，因为 Blob API 现在可以对具有分层命名空间的帐户中的数据进行操作。

Blob 存储功能（例如[诊断日志记录](../common/storage-analytics-logging.md)、[访问层级](storage-blob-storage-tiers.md)、[Blob 存储生命周期管理策略](storage-lifecycle-management-concepts.md)）现在适用于具有分层命名空间的帐户。 因此，你可以在 blob 存储帐户上启用分层命名空间，而不会失去对这些重要功能的访问权限。 

> [!NOTE]
> Data Lake Storage 上的多协议访问已公开发布，并在所有区域提供。 多协议访问启用的某些 Azure 服务或 blob 存储功能仍处于预览状态。 有关详细信息，请参阅本文每一节中的表。 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data Lake Storage 多协议访问的工作原理

Blob API 和 Data Lake Storage Gen2 API 可以对具有分层命名空间的存储帐户中的相同数据进行操作。 Data Lake Storage Gen2 通过分层命名空间来路由 Blob API，因此你可以获得一流目录操作和兼容 POSIX 的访问控制列表 (ACL) 的优势。 

![Data Lake Storage 多协议访问概念](./media/data-lake-storage-interop/interop-concept.png) 

使用 Blob API 的现有工具和应用程序会自动获得这些优势。 开发人员不需修改它们。 Data Lake Storage Gen2 会以一致方式应用目录和文件级别 ACL，而不管工具和应用程序使用什么协议来访问数据。 

## <a name="blob-storage-feature-support"></a>Blob 存储功能支持

Data Lake Storage 上的多协议访问使你能够将更多 Blob 存储功能与你的 Data Lake Storage 配合使用。 此表列出了 Data Lake Storage 上的多协议访问启用的功能。 

此表中出现的项将随着时间的推移而更改，从而继续扩展对 Blob 存储功能的支持。 

> [!NOTE]
> 即使 Data Lake Storage 上的多协议访问现已正式发布，对其中某些功能的支持仍处于预览阶段。 

|Blob 存储功能 | 支持级别 |
|---|---|
|[冷访问层](storage-blob-storage-tiers.md)|正式发布|
|Blob Sdk |正式发布|
|[PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |正式发布|
|[CLI](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |正式发布|
|[诊断日志](../common/storage-analytics-logging.md)| 预览|
|[生命周期管理策略](storage-lifecycle-management-concepts.md)| 预览|
|[通过 Azure 事件网格通知](data-lake-storage-events.md)|预览|
|[存档访问层](storage-blob-storage-tiers.md)| 预览|
|[blobfuse](storage-how-to-mount-container-linux.md)|尚不支持|
|[不可变存储](storage-blob-immutable-storage.md)|尚不支持|
|[快照](storage-blob-snapshots.md)|尚不支持|
|[软删除](storage-blob-soft-delete.md)|尚不支持|
|[静态网站](storage-blob-static-website.md)|尚不支持|

若要详细了解 Azure Data Lake Storage Gen2 的一般已知问题和限制，请参阅[已知问题](data-lake-storage-known-issues.md)。

## <a name="azure-ecosystem-support"></a>Azure 生态系统支持

通过 Data Lake Storage 上的多协议访问，还可以将更多 Azure 服务与 Data Lake Storage 连接。 此表列出了由 Data Lake Storage 上的多协议访问启用的服务。 

与受支持的 Blob 存储功能列表一样，此表中出现的项将随时间而改变，因为对 Azure 服务的支持将继续扩展。 

> [!NOTE]
> 即使 Data Lake Storage 上的多协议访问现已正式发布，对其中某些服务的支持仍处于预览阶段。 

|Azure 服务 | 支持级别 |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|正式发布|
|[Azure 事件中心捕获](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|正式发布|
|[Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|正式发布|
|[IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|正式发布|
|[逻辑应用](https://azure.microsoft.com/services/logic-apps/)|正式发布|
|[Azure 搜索](https://docs.microsoft.com/azure/search/search-blob-storage-integration)|预览|

有关 Data Lake Storage Gen2 的 Azure 生态系统支持的完整列表，请参阅将[Azure Data Lake Storage 与 Azure 服务集成](data-lake-storage-integrate-with-azure-services.md)。

若要详细了解 Azure Data Lake Storage Gen2 的一般已知问题和限制，请参阅[已知问题](data-lake-storage-known-issues.md)。

## <a name="next-steps"></a>后续步骤

请参阅[已知问题](data-lake-storage-known-issues.md)





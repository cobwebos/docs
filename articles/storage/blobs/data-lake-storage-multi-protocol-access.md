---
title: Azure Data Lake Storage 上的多协议访问 |Microsoft Docs
description: 使用 Blob Api 和应用程序将 Blob Api 与 Azure Data Lake Storage Gen2 配合使用。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f34c5d5069a158579864320d0fbf965de8936d9c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896110"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage 上的多协议访问

Blob Api 现在适用于具有分层命名空间的帐户。 这可以将工具、应用程序和服务的生态系统和多个 Blob 存储功能与具有分层命名空间的帐户解除锁定。

最近，你可能必须为对象存储和分析存储维护单独的存储解决方案。 这是因为 Azure Data Lake Storage Gen2 有有限的生态系统支持。 它还限制了对 Blob 服务功能的访问，如诊断日志记录。 零碎的存储解决方案难于维护，因为您必须在帐户之间移动数据才能完成各种方案。 您不必再这样做了。

通过 Data Lake Storage 上的多协议访问，你可以使用工具、应用程序和服务的生态系统来处理数据。 这还包括第三方工具和应用程序。 您可以将它们指向具有分层命名空间的帐户，而无需修改它们。 即使应用程序调用 Blob Api，这些应用程序*也能正常工作，* 因为 Blob api 现在可以对具有分层命名空间的帐户中的数据进行操作。

Blob 存储功能（如[诊断日志记录](../common/storage-analytics-logging.md)、[访问层](storage-blob-storage-tiers.md)和[blob 存储生命周期管理策略](storage-lifecycle-management-concepts.md)）现在适用于具有分层命名空间的帐户。 因此，你可以在 blob 存储帐户上启用分层命名空间，而不会失去对这些重要功能的访问权限。 

> [!NOTE]
> Data Lake Storage 上的多协议访问已公开发布，并在所有区域提供。 多协议访问启用的某些 Azure 服务或 blob 存储功能仍处于预览状态。 有关详细信息，请参阅本文每一节中的表。 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data lake 存储上的多协议访问的工作原理

Blob Api 和 Data Lake Storage Gen2 Api 可对具有分层命名空间的存储帐户中的同一数据进行操作。 Data Lake Storage Gen2 通过分层命名空间路由 Blob Api，使你可以获得第一类目录操作和与 POSIX 兼容的访问控制列表（Acl）的好处。 

![Data Lake Storage 概念上的多协议访问](./media/data-lake-storage-interop/interop-concept.png) 

使用 Blob API 的现有工具和应用程序会自动获得这些优势。 开发人员无需修改它们。 Data Lake Storage Gen2 一致地应用目录和文件级 Acl，而不考虑工具和应用程序用于访问数据的协议。 

## <a name="blob-storage-feature-support"></a>Blob 存储功能支持

Data Lake Storage 上的多协议访问使你能够将更多 Blob 存储功能与你的 Data Lake Storage 配合使用。 此表列出了 Data Lake Storage 上的多协议访问启用的功能。 

此表中出现的项将随着时间的推移而更改，从而继续扩展对 Blob 存储功能的支持。 

> [!NOTE]
> 即使 Data Lake Storage 上的多协议访问现已正式发布，对其中某些功能的支持仍处于预览阶段。 

|Blob 存储功能 | 支持级别 |
|---|---|
|[冷访问层](storage-blob-storage-tiers.md)|正式发布|
|Blob REST Api|正式发布|
|Blob Sdk |正式发布|
|[PowerShell （Blob）](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |正式发布|
|[CLI （Blob）](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |正式发布|
|[通过 Azure 事件网格通知](data-lake-storage-events.md)|正式发布|
|具有文件系统语义的 Blob Sdk （[.net](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-dotnet) &vert; [Python](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-python) &vert; [Java](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-java)）|预览|
|[具有文件系统语义的 PowerShell](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-powershell)|预览|
|[具有文件系统语义的 CLI](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-cli)|预览|
|[诊断日志](../common/storage-analytics-logging.md)| 预览|
|[生命周期管理策略](storage-lifecycle-management-concepts.md)| 预览|
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
|[Azure 认知搜索](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|预览|

有关 Data Lake Storage Gen2 的 Azure 生态系统支持的完整列表，请参阅将[Azure Data Lake Storage 与 Azure 服务集成](data-lake-storage-integrate-with-azure-services.md)。

若要详细了解 Azure Data Lake Storage Gen2 的一般已知问题和限制，请参阅[已知问题](data-lake-storage-known-issues.md)。

## <a name="next-steps"></a>后续步骤

查看[已知问题](data-lake-storage-known-issues.md)





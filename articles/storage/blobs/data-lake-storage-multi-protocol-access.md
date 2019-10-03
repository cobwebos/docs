---
title: Azure Data Lake Storage 上的多协议访问（预览版） |Microsoft Docs
description: 使用 Blob Api 和应用程序将 Blob Api 与 Azure Data Lake Storage Gen2 配合使用。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 471e3008c25ddef83dd9b502dd8f677ae31cc71b
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259375"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Azure Data Lake Storage 上的多协议访问（预览版）

Blob Api 现在适用于具有分层命名空间的帐户。 这将使工具、应用程序和服务的整个生态系统以及所有 Blob 存储功能与具有分层命名空间的帐户解除锁定。

最近，你可能必须为对象存储和分析存储维护单独的存储解决方案。 这是因为 Azure Data Lake Storage Gen2 有有限的生态系统支持。 它还限制了对 Blob 服务功能的访问，如诊断日志记录。 零碎的存储解决方案难于维护，因为您必须在帐户之间移动数据才能完成各种方案。 您不必再这样做了。

> [!NOTE]
> Data Lake Storage 上的多协议访问以公共预览版提供，可在[这些区域](#region-availability)中使用。 若要查看限制，请参阅[已知问题](data-lake-storage-known-issues.md)一文。 若要注册预览版，请参阅[此页](https://aka.ms/blobinteropsignup)。

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>使用应用程序、工具和服务的整个生态系统

如果在 Data Lake Storage 上注册多协议访问的预览，则可以使用整个工具、应用程序和服务生态系统来处理所有数据。 这包括 azure[流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction)、 [IOT 中心](https://docs.microsoft.com/azure/iot-hub/)、 [Power BI](https://docs.microsoft.com/power-bi/desktop-data-sources)等 azure 服务。 

这还包括第三方工具和应用程序。 您可以将它们指向具有分层命名空间的帐户，而无需修改它们。 即使应用程序调用 Blob Api，这些应用程序*也能正常工作，* 因为 Blob api 现在可以对具有分层命名空间的帐户中的数据进行操作。

> [!NOTE]
> 若要查看限制，请参阅[已知问题](data-lake-storage-known-issues.md)一文。

## <a name="use-all-blob-storage-features"></a>使用 Blob 存储的所有功能

Blob 存储功能（如[诊断日志记录](../common/storage-analytics-logging.md)、[访问层](storage-blob-storage-tiers.md)和[blob 存储生命周期管理策略](storage-lifecycle-management-concepts.md)）现在适用于具有分层命名空间的帐户。 因此，你可以在 blob 存储帐户上启用分层命名空间，而不会失去对这些重要功能的访问权限。 

> [!NOTE]
> 若要查看限制，请参阅[已知问题](data-lake-storage-known-issues.md)一文。

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data lake 存储上的多协议访问的工作原理

Blob Api 和 Data Lake Storage Gen2 Api 可对具有分层命名空间的存储帐户中的同一数据进行操作。 Data Lake Storage Gen2 通过分层命名空间路由 Blob Api，使你可以获得第一类目录操作和与 POSIX 兼容的访问控制列表（Acl）的好处。 

![Data Lake Storage 概念上的多协议访问](./media/data-lake-storage-interop/interop-concept.png) 

使用 Blob API 的现有工具和应用程序会自动获得这些优势。 开发人员无需修改它们。 Data Lake Storage Gen2 一致地应用目录和文件级 Acl，而不考虑工具和应用程序用于访问数据的协议。 

<a id="region-availability" />

## <a name="region-availability"></a>适用地区

Azure Data Lake Storage （预览版）上的多协议访问在以下区域中提供：

|||||
|-|-|-|-|
|美国中部|美国中西部|加拿大中部|
|East US|东亚|北欧|
|美国东部 2|东南亚|西欧|
|美国西部|澳大利亚东部|日本东部|
|美国西部 2|巴西南部||

## <a name="next-steps"></a>后续步骤

查看[已知问题](data-lake-storage-known-issues.md)





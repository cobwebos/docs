---
title: Azure Data Lake Storage 上的多协议访问 |Microsoft Docs
description: 使用 Blob Api 和应用程序将 Blob Api 与 Azure Data Lake Storage Gen2 配合使用。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914852"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage 上的多协议访问

Blob Api 现在适用于具有分层命名空间的帐户。 这可以将工具、应用程序和服务的生态系统和多个 Blob 存储功能与具有分层命名空间的帐户解除锁定。

最近，你可能必须为对象存储和分析存储维护单独的存储解决方案。 这是因为 Azure Data Lake Storage Gen2 有有限的生态系统支持。 它还限制了对 Blob 服务功能的访问，如诊断日志记录。 零碎的存储解决方案难于维护，因为您必须在帐户之间移动数据才能完成各种方案。 您不必再这样做了。

通过 Data Lake Storage 上的多协议访问，你可以使用工具、应用程序和服务的生态系统来处理数据。 这还包括第三方工具和应用程序。 您可以将它们指向具有分层命名空间的帐户，而无需修改它们。 即使应用程序调用 Blob Api，这些应用程序*也能正常工作，* 因为 Blob api 现在可以对具有分层命名空间的帐户中的数据进行操作。

Blob 存储功能（如[诊断日志记录](../common/storage-analytics-logging.md)、[访问层](storage-blob-storage-tiers.md)和[blob 存储生命周期管理策略](storage-lifecycle-management-concepts.md)）现在适用于具有分层命名空间的帐户。 因此，你可以在 blob 存储帐户上启用分层命名空间，而不会失去对这些重要功能的访问权限。 

> [!NOTE]
> Data Lake Storage 上的多协议访问已公开发布，并在所有区域提供。 多协议访问启用的某些 Azure 服务或 Blob 存储功能仍处于预览状态。  这些文章汇总了对 Blob 存储功能和 Azure 服务集成的当前支持。 
>
> [Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)
>
>[支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data lake 存储上的多协议访问的工作原理

Blob Api 和 Data Lake Storage Gen2 Api 可对具有分层命名空间的存储帐户中的同一数据进行操作。 Data Lake Storage Gen2 通过分层命名空间路由 Blob Api，使你可以获得第一类目录操作和与 POSIX 兼容的访问控制列表（Acl）的好处。 

![Data Lake Storage 概念上的多协议访问](./media/data-lake-storage-interop/interop-concept.png) 

使用 Blob API 的现有工具和应用程序会自动获得这些优势。 开发人员无需修改它们。 Data Lake Storage Gen2 一致地应用目录和文件级 Acl，而不考虑工具和应用程序用于访问数据的协议。 

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)
- [支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)
- [支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)





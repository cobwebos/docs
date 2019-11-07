---
title: Azure Data Lake Storage Gen2 中的索引文档（预览）
titleSuffix: Azure Cognitive Search
description: 了解如何在 Azure Data Lake Storage Gen2 中为内容和元数据编制索引。 此功能目前处于公共预览阶段
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 390f45ce874e4e64d3a7b9a6c3944983e0ba9028
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719921"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中的索引文档

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2 支持目前为公共预览版。 提供的预览功能不带服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 你可以通过填写[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)来请求对预览的访问权限。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前没有门户或 .NET SDK 支持。


设置 Azure 存储帐户时，可以选择启用[分层命名空间](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)。 这允许将帐户中的内容集合组织到目录和嵌套子目录的层次结构中。 启用分层命名空间即可启用[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)。

本文介绍如何开始 Azure Data Lake Storage Gen2 中的索引文档。

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>设置 Azure Data Lake Storage Gen2 索引器

需要完成几个步骤才能 Data Lake Storage Gen2 的索引内容。

### <a name="step-1-sign-up-for-the-preview"></a>步骤1：注册预览版

填写[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)即可注册 Data Lake Storage Gen2 索引器预览。 一旦你接受到预览版，你将收到一封确认电子邮件。

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>步骤2：遵循 Azure Blob 存储索引设置步骤

收到预览注册成功的确认后，便可以创建索引管道了。

可以通过使用[REST API 版本 2019-05-06-Preview](search-api-preview.md)来索引 Data Lake Storage Gen2 内容和元数据。 目前不支持门户或 .NET SDK。

Data Lake Storage Gen2 中的索引内容与 Azure Blob 存储中的索引内容相同。 若要了解如何设置 Data Lake Storage Gen2 数据源、索引和索引器，请参阅[如何在 Azure Blob 存储中通过 azure 认知搜索为文档编制索引](search-howto-indexing-azure-blob-storage.md)。 Blob 存储一文还提供了有关支持的文档格式、提取的 Blob 元数据属性、增量索引等信息。 此信息对于 Data Lake Storage Gen2 是相同的。

## <a name="access-control"></a>访问控制

Azure Data Lake Storage Gen2 实现一个支持 Azure 基于角色的访问控制（RBAC）和类似于 POSIX 的访问控制列表（Acl）的[访问控制模型](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 在 Data Lake Storage Gen2 编制内容的索引时，Azure 认知搜索不会从内容中提取 RBAC 和 ACL 信息。 因此，此信息不会包含在 Azure 认知搜索索引中。

如果保持对索引中每个文档的访问控制很重要，则由应用程序开发人员来实现[安全修整](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)。
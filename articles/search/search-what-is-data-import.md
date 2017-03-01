---
title: "Azure 搜索中的数据上传 | Microsoft Docs"
description: "了解如何将数据上载到 Azure 搜索中的索引。"
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 292c9150822363aba3336b1efce579dc5362cb14
ms.openlocfilehash: e522d608e8ff51e00b3c1a461bf9ba909b0105af


---
# <a name="upload-data-to-azure-search"></a>将数据上载到 Azure 搜索
> [!div class="op_single_selector"]
> * [概述](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

可通过两种方法在数据中填充索引。 第一种方法是使用 Azure 搜索 [REST API](search-import-data-rest-api.md) 或 [.NET SDK](search-import-data-dotnet.md) 将数据手动推送至索引。 第二种方法是[将支持的数据源指向](search-indexer-overview.md)索引，让 Azure 搜索自动提取数据。

## <a name="push-data-to-an-index"></a>将数据推送至索引
这种方法是指以编程方式将数据发送到 Azure 搜索以使其可供搜索。 对于具有极低延迟要求的应用程序（例如，如果需要搜索操作与动态库存数据库同步），只能选择推送模型。

可以使用 [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) 或 [.NET SDK](search-import-data-dotnet.md) 将数据推送至索引。 目前尚没有支持通过门户推送数据的工具。

此方法相比拉模型更加灵活，因为你可以单个或批量上载文档（每批最多 1000 个或 16MB，以先达到为准）。 推送模型还允许你将文档上载到 Azure Search，而不考虑数据的位置。

Azure 搜索识别的数据格式为 JSON，数据集中的所有文档必须包含可映射到索引架构中定义的字段的字段。 

## <a name="pull-data-into-an-index"></a>将数据提取到索引中
提取模型对支持的数据源进行爬网，将数据自动上载到索引中。 在 Azure 搜索中，此功能通过*索引器*实现，当前可用于 [Blob 存储](search-howto-indexing-azure-blob-storage.md)、[表存储](search-howto-indexing-azure-tables.md)、[DocumentDB](http://aka.ms/documentdb-search-indexer)、[Azure SQL 数据库和 Azure VM 上的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)。 

索引器将索引连接到数据源（通常是表、视图或等效的结构），将源字段映射到索引中的等效字段。 在执行期间，行集将自动转换为 JSON 并载入指定的索引中。 所有索引器支持计划，使用户能够指定数据的刷新频率。 大多数索引器提供更改跟踪（如果受数据源的支持）。 除了识别新文档外，通过跟踪对现有文档的更改和删除外，索引器免除了主动管理索引中数据的必要。 

索引器功能已在 [Azure 门户](search-import-data-portal.md)、[REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) 和 [.NET SDK](https://docs.microsoft.com/otnet/api/microsoft.azure.search.iindexersoperations?redirectedfrom=MSDN#microsoft_azure_search_iindexersoperations) 中公开。 

使用门户的一个优势在于，Azure 搜索通常可以通过读取源数据集的元数据来生成默认的索引架构。 在处理生成的索引之前可对其进行修改，此后，只能编辑不需要重建索引的架构。 如果想要进行的更改会直接影响架构，则需要重建索引。 

填充索引后，可以在门户命令栏中使用“搜索资源管理器”作为验证步骤。

## <a name="query-an-index-using-search-explorer"></a>使用搜索资源管理器查询索引

针对文档上载执行初步检查的捷径之一是在门户中使用“搜索资源管理器”。 使用资源管理器可以直接查询索引，而无需编写任何代码。 搜索体验取决于默认设置，例如[简单语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)和默认的 [searchMode 查询参数](https://docs.microsoft.com/rest/api/searchservice/search-documents)。 结果将以 JSON 格式返回，方便用户检查整个文档。

> [!TIP]
> 有大量的 [Azure 搜索代码示例](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)包含了嵌入的或随时可用的数据集，帮助用户轻松入门。 门户中还提供了一个示例索引器，以及一个由小型房地产数据集组成的数据源（名为“realestate-us-sample”）。 针对示例数据源运行预配置的索引器时，将会创建索引并连同文档一起加载该索引，然后，可以使用搜索资源管理器或编写的代码查询该索引。


<!--HONumber=Feb17_HO3-->



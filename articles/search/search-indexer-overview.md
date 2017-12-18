---
title: "Azure 搜索中的索引器 | Microsoft Docs"
description: "对 Azure SQL 数据库、Azure Cosmos DB 或 Azure 存储爬网，提取可搜索的数据并填充 Azure 搜索索引。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/17/2017
ms.author: heidist
ms.openlocfilehash: 827c0122fafceca369c0350e6846c77e98abc3b5
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="indexers-in-azure-search"></a>Azure 搜索中的索引器
> [!div class="op_single_selector"]
>
> * [概述](search-indexer-overview.md)
> * [门户](search-import-data-portal.md)
> * [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [Azure Cosmos DB](search-howto-index-documentdb.md)
> * [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)
> * [Azure 表存储](search-howto-indexing-azure-tables.md)
>

Azure 搜索中的 *索引器* 是一种爬网程序，它从外部数据源提取可搜索的数据和元数据，并根据索引与数据源之间字段到字段的映射填充索引。 因为不需要编写任何将数据推送到索引的代码，该服务就能拉取数据，因此这种方法有时也称为“拉取模式”。

索引器基于数据源类型或平台，单个索引器适用于 Azure 上的 SQL Server、Cosmos DB、Azure 表存储和 Blob 存储，等等。

可以单独使用索引器来引入数据，也可以结合索引器使用多种技术来加载索引中的部分字段。

可以按需运行索引器，也可以采用每 15 分钟运行一次的定期数据刷新计划来运行索引器。 要进行更频繁的更新，则需要采用“推送模式”，便于同时更新 Azure 搜索和外部数据源中的数据。

## <a name="approaches-for-creating-and-managing-indexers"></a>创建及管理索引器的方法

可以使用以下方法创建和管理索引器：

* [门户 > 导入数据向导](search-get-started-portal.md)
* [服务 REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

一开始会将新的索引器宣布为预览版功能。 预览版功能首先在 API（REST 和 .NET）中引入，然在逐渐公开发行以后再集成到门户中。 如果评估的是新索引器，则应做好编写代码的计划。

## <a name="basic-configuration-steps"></a>基本配置步骤
索引器可提供数据源独有的功能。 因此，索引器或数据源配置的某些方面会因索引器类型而不同。 但是，所有索引器的基本构成元素和要求都相同。 下面介绍所有索引器都适用的共同步骤。

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源
索引器从保存信息的数据源（如连接字符串和可能的凭据）拉取数据。 当前支持以下数据源：

* [Azure SQL 数据库或 Azure 虚拟机上的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* 适用于所选内容类型的 [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)
* [Azure 表存储](search-howto-indexing-azure-tables.md)

数据源的配置和管理独立于使用数据源的索引器，这意味着多个索引器可使用一个数据源，同时加载多个索引。

### <a name="step-2-create-an-index"></a>步骤 2：创建索引
索引器会自动执行某些与数据引入相关的任务，但通常不会自动创建索引。 先决条件是必须具有预定义的索引，且索引的字段必须与外部数据源中的字段匹配。 有关构建索引的详细信息，请参阅 [创建索引（Azure 搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/Create-Index)。 如需字段关联方面的帮助，请参阅 [Azure 搜索索引器中的字段映射](search-indexer-field-mappings.md)。

> [!Tip]
> 虽然不能使用索引器来生成索引，但可以使用门户中的**导入数据**向导。 大多数情况下，该向导可以根据源中现有的元数据推断索引架构，提供一个初级索引架构，该架构在向导处于活动状态时可以进行内联编辑。 在服务上创建索引以后，若要在门户中进一步进行编辑，多数情况下只能添加新字段。 可以将向导视为索引的创建工具而非修订工具。 如需手动方式的学习，请一步步完成[门户演练](search-get-started-portal.md)。

### <a name="step-3-create-and-schedule-the-indexer"></a>步骤 3：创建和计划索引器
索引器定义是一种构造，用于指定索引、数据源和计划。 索引器可从另一个服务引用数据源，前提是该数据源来自同一个订阅。 有关构建索引器的详细信息，请参阅 [创建索引器（Azure 搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)。

## <a name="next-steps"></a>后续步骤
了解基本概念后，下一步是查看每种数据源特定的要求和任务。

* [Azure SQL 数据库或 Azure 虚拟机上的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)
* [Azure 表存储](search-howto-indexing-azure-tables.md)
* [使用 Azure 搜索 Blob 索引器为 CSV blob 编制索引](search-howto-index-csv-blobs.md)
* [使用 Azure 搜索 Blob 索引器为 JSON blob 编制索引](search-howto-index-json-blobs.md)

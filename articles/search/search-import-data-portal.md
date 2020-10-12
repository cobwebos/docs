---
title: 使用 Azure 门户将数据导入搜索索引
titleSuffix: Azure Cognitive Search
description: 了解如何使用 Azure 门户中的“导入数据”向导从 Azure VM 上的 Cosmos DB、Blob 存储、表存储、SQL 数据库、SQL 托管实例和 SQL Server 抓取 Azure 数据。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cff009d5d1e187e8d0330fadca530b57b3e3d21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935205"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Azure 认知搜索的导入数据向导

Azure 门户在 Azure 认知搜索仪表板上提供了“导入数据”向导，用于制作索引的原型和加载索引。 本文介绍该向导的优点和限制、输入和输出以及一些用法信息。 有关使用内置示例数据逐步运行该向导的实践指导，请参阅[使用 Azure 门户创建 Azure 认知搜索索引](search-get-started-portal.md)快速入门。

此向导执行的操作包括：

1 - 连接到支持的 Azure 数据源。

2 - 创建通过采样源数据推断的索引架构。

3 -（可选）添加 AI 扩充以提取或生成内容和结构。

4 - 运行向导以创建对象、导入数据、设置计划和其他配置选项。

该向导会输出许多对象，这些对象将保存到搜索服务，可通过编程方式或其他工具访问它们。

## <a name="advantages-and-limitations"></a>优点和限制

在编写任何代码之前，可以使用向导进行原型制作和概念证明测试。 向导将连接到外部数据源，对数据进行采样以创建初始索引，然后将数据作为 JSON 文档导入到 Azure 认知搜索中的索引。 

采样是推断索引架构的过程，它存在一些限制。 创建数据源时，向导将会选取文档的样本，以确定哪些列是数据源的一部分。 不一定会读取所有文件，因为对于极大的数据源，这可能需要好几个小时。 在提供一系列文档的情况下，将使用源元数据（例如字段名称或类型）在索引架构中创建字段集合。 根据源数据的复杂性，可能需要编辑初始架构以提高准确性，或对其进行扩展以获得完整性。 可以在索引定义页上以内联方式进行更改。

总体而言，使用向导的优点非常明显：只要符合要求，就可以在数分钟内制作出可查询索引的原型。 向导在一定程度上可以处理索引编制的复杂性，例如，以 JSON 文档的形式提供数据。

下面汇总了已知的限制：

+ 向导不支持迭代或重用。 每次运行向导都会创建新的索引、技能集和索引器配置。 在向导中只能保存和重用数据源。 若要编辑或细化其他对象，必须使用 REST API 或 .NET SDK 来检索和修改结构。

+ 源内容必须位于受支持的 Azure 数据源中。

+ 采样是针对一部分源数据执行的。 对于大型数据源，向导可能会遗漏字段。 如果采样不足，可能需要扩展架构或更正推断出的数据类型。

+ 门户中公开的 AI 扩充限制为几个内置技能。 

+ 可以通过向导创建的[知识存储](knowledge-store-concept-intro.md)限制为几个默认投影。 Blob 容器和表附带默认名称和结构，以方便你保存向导创建的扩充文档。

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>数据源输入

“导入数据”向导使用 Azure 认知搜索索引器提供的内部逻辑连接到外部数据源。索引器旨在对源采样、读取元数据、破解文档以读取内容和结构，并将内容序列化为 JSON，供后续导入到 Azure 认知搜索。

只能从单个表、数据库视图或等效的数据结构导入，但是，此结构可能包含分层的或嵌套的子结构。 有关详细信息，请参阅[如何为复杂类型建模](search-howto-complex-data-types.md)。

在运行向导之前，应创建此单个表或视图，其中必须包含内容。 针对空数据源运行“导入数据”向导并无意义，其原因显而易见。

|  选项 | 说明 |
| ---------- | ----------- |
| **现有数据源** |如果已在搜索服务中定义索引器，则可能已经获得了一个可以重用的现有数据源定义。 在 Azure 认知搜索中，数据源对象仅供索引器使用。 可以编程方式或通过“导入数据”向导创建数据源对象，然后按需重用这些对象。|
| **示例**| Azure 认知搜索提供了两个在教程和快速入门中使用的内置示例数据源：房地产 SQL 数据库，以及托管在 Cosmos DB 上的“酒店”数据库。 若要根据“酒店”示例进行演练，请参阅[在 Azure 门户中创建索引](search-get-started-portal.md)快速入门。 |
| [Azure SQL 数据库或 SQL 托管实例](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |可以在此页上或通过 ADO.NET 连接字符串，指定服务名称、具有读取权限的数据库用户的凭据和数据库名称。 选择要查看或自定义属性的连接字符串选项。 <br/><br/>必须在此页上指定提供行集的表或视图。 连接成功后会显示此选项，并提供下拉列表以便可以进行选择。|
| **Azure VM 上的 SQL Server** |指定完全限定的服务名、用户 ID 和密码以及数据库作为连接字符串。 若要使用此数据源，以前必须已在加密连接的本地存储中安装了证书。 有关说明，请参阅[SQL VM 与 Azure 认知搜索的连接](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。 <br/><br/>必须在此页上指定提供行集的表或视图。 连接成功后会显示此选项，并提供下拉列表以便可以进行选择。 |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|要求包括帐户、数据库和集合。 集合中的所有文档都将包含在索引中。 可以定义查询以平展或筛选行集，或者将查询留空。 在此向导中，不需要查询。|
| [**Azure Blob 存储**](search-howto-indexing-azure-blob-storage.md) |要求包括存储帐户和容器。 （可选）如果 blob 名称遵循用于分组的虚拟命名约定，可以将名称的虚拟目录部分指定为容器下的某个文件夹。 有关详细信息，请参阅[为 Blob 存储编制索引](search-howto-indexing-azure-blob-storage.md)。 |
| [**Azure 表存储**](search-howto-indexing-azure-tables.md) |要求包括存储帐户和表名。 （可选）可以指定一个查询来检索表的子集。 有关详细信息，请参阅[为表存储编制索引](search-howto-indexing-azure-tables.md)。 |

## <a name="wizard-output"></a>向导输出

在幕后，向导将会创建、配置和调用以下对象。 向导运行完成后，可以在门户页中找到其输出。 服务的“概述”页包含索引、索引器、数据源和技能组的列表。 可以在门户中查看索引定义的完整 JSON 代码。 对于其他定义，可以使用 [REST API](/rest/api/searchservice/) 来获取特定的对象。

| Object | 说明 | 
|--------|-------------|
| [数据源](/rest/api/searchservice/create-data-source)  | 将连接信息（包括凭据）保存到源数据。 某个数据源对象专用于索引器。 | 
| [索引](/rest/api/searchservice/create-index) | 用于全文搜索和其他查询的物理数据结构。 | 
| [技能集](/rest/api/searchservice/create-skillset) | 用于操作、转换和调整内容（包括分析和提取图像文件中的信息）的完整指令集。 它包括对提供扩充的认知服务资源的引用，但非常简单的和受限制的结构除外。 它还可能包含知识存储定义。  | 
| [索引器](/rest/api/searchservice/create-indexer)  | 一个配置对象，指定数据源、目标索引、可选计划，以及有关错误处理和 base-64 编码的可选技能集、可选计划和可选配置设置。 |


## <a name="how-to-start-the-wizard"></a>如何启动向导

可以通过服务“概述”页上的命令栏启动“导入数据”向导。

1. 在 [Azure 门户](https://portal.azure.com)中，从仪表板打开搜索服务页，或者在服务列表中[查找服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

2. 在顶部服务概述页中，单击“导入数据”  。

   ![门户中的“导入数据”命令](./media/search-import-data-portal/import-data-cmd2.png "启动“导入数据”向导")

还可以通过其他 Azure 服务（包括 Azure Cosmos DB、Azure SQL 数据库、SQL 托管实例和 Azure Blob 存储）启动“导入数据”。 在服务概述页上的左侧导航窗格中查找“添加 Azure 认知搜索”。

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>如何在向导中编辑或完成索引架构

向导将生成不完整的索引，可以使用从输入数据源获取的文档对其进行填充。 对于功能索引，请确保定义了以下元素。

1. 字段列表是否完整？ 添加采样遗漏的新字段，并删除对搜索体验没有作用的，或不会在[筛选器表达式](search-query-odata-filter.md)或[评分配置文件](index-add-scoring-profiles.md)中使用的所有字段。

1. 数据类型是否适合传入的数据？ Azure 认知搜索支持[实体数据模型 (EDM) 数据类型](/rest/api/searchservice/supported-data-types)。 对于 Azure SQL 数据，有一个[映射图表](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping)会列出等效值。 有关更多背景信息，请参阅[字段映射和转换](search-indexer-field-mappings.md)。

1. 是否有一个可用作键的字段？ 此字段的类型必须是 Edm.string，并且必须唯一标识某个文档。 对于关系数据，它可能会映射到主键。 对于 Blob，它可能是 `metadata-storage-path`。 如果字段值包含空格或短划线，则必须在“高级选项”下的“创建索引器”步骤中设置“Base 64 编码密钥”选项以禁止验证检查这些字符    。

1. 设置属性以确定如何在索引中使用该字段。 

   请花些时间来完成此步骤，因为属性确定了索引中字段的物理表达式。 以后若要更改属性（即使是以编程方式进行更改），几乎总要删除并重建索引。 **Searchable** 和 **Retrievable** 等核心属性[对存储会产生负面影响](search-what-is-an-index.md#index-size)。 启用筛选器和使用建议器会提高存储要求。 
   
   + “可搜索”启用全文搜索  。 在自由格式查询或查询表达式中使用的每个字段必须有此属性。 为标记为“可搜索”的每个字段创建反向索引  。

   + “可检索”在搜索结果中返回该字段  。 用于提供内容以搜索结果的每个字段必须有此属性。 设置此字段不会明显影响索引大小。

   + “可筛选”允许在筛选表达式中引用该字段  。 在 $filter 表达式中使用的每个字段必须有此属性  。 筛选表达式用于精确匹配项。 由于文本字符串保持不变，因此需要额外的存储空间来容纳逐字内容。

   + “可查找”为分面导航启用该字段  。 只有也标记为“可筛选”的字段可标记为“可查找”   。

   + “可排序”允许在排序中使用该字段  。 在 $Orderby 表达式中使用的每个字段必须有此属性  。

1. 是否需要[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)？ 对于**可搜索的** Edm.string 字段，若要获得语言增强的索引和查询，可以设置**分析器**。 

   默认值为“标准 Lucene”，但如果想要使用 Microsoft 的分析器以进行高级词汇处理（如解决不规则名词和动词形式），可选择“Microsoft 英语”   。 在门户中只能指定语言分析器。 若要使用自定义分析器或非语言分析器（例如关键字、模式等），必须以编程方式来实现。 有关分析器的详细信息，请参阅[添加语言分析器](search-language-support.md)。

1. 是否需要自动完成或建议结果形式的自动提示功能？ 选中“建议器”复选框，对所选字段启用[自动提示查询建议和自动完成](index-add-suggesters.md)。 使用建议器会增加索引中标记化字词的数目，因此会消耗更多的存储。


## <a name="next-steps"></a>后续步骤

了解向导的优点和限制的最佳方式就是逐步运行该向导。 以下快速入门会引导你完成每个步骤。

> [!div class="nextstepaction"]
> [使用 Azure 门户创建 Azure 认知搜索索引](search-get-started-portal.md)
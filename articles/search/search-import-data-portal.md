---
title: 使用 Azure 门户将数据导入搜索索引 - Azure 搜索
description: 了解如何使用 Azure 门户中的“导入数据”向导从 Cosmos DB、Blob 存储、表存储、SQL 数据库和 Azure VM 上的 SQL Server 抓取 Azure 数据。
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 89f43227cfca3519a4985c5c961cf0b3c5774177
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "71936909"
---
# <a name="import-data-wizard-for-azure-search"></a>Azure 搜索的导入数据向导

Azure 门户在 Azure 搜索仪表板上提供了一个**导入数据**向导，用于原型制作和加载索引。 本文介绍使用向导、输入和输出以及一些使用情况信息的优点和限制。 有关使用内置示例数据逐句使用向导的动手指南，请参阅[使用 Azure 门户快速入门创建 Azure 搜索索引](search-get-started-portal.md)。

此向导执行的操作包括：

1-连接到支持的 Azure 数据源。

2-创建通过采样源数据推断出的索引架构。

3-（可选）添加 AI 根据以提取或生成内容和结构。

4-运行向导以创建对象、导入数据、设置计划和其他配置选项。

向导将会输出多个保存到搜索服务的对象，您可以通过编程方式或在其他工具中访问这些对象。

## <a name="advantages-and-limitations"></a>优点和限制

在编写任何代码之前，您可以使用该向导进行原型测试和概念证明测试。 向导将连接到外部数据源，对数据进行采样以创建初始索引，然后将数据作为 JSON 文档导入到 Azure 搜索的索引中。 

采样是指在其中推断索引架构并且有一些限制的过程。 创建数据源后，向导会选取文档示例，以确定哪些列是数据源的一部分。 并非所有文件都是读取的，因为对于非常大的数据源，这可能需要几个小时。 给定一组文档，源元数据（如字段名称或类型）用于在索引架构中创建字段集合。 根据源数据的复杂性，可能需要编辑初始架构以获取准确性，或对其进行扩展以实现完整性。 可以在 "索引定义" 页上以内联方式进行更改。

总体而言，使用该向导的优点是显而易见的：只要满足要求，你就可以在数分钟内原型查询索引。 索引的一些复杂之处（例如，以 JSON 文档的形式提供数据）由向导处理。

下面总结了已知限制：

+ 向导不支持迭代或重复使用。 每次遍历向导都会创建一个新的索引、技能组合和索引器配置。 只有数据源可以保存并在向导中重复使用。 若要编辑或修改其他对象，必须使用 REST Api 或 .NET SDK 来检索和修改结构。

+ 源内容必须驻留在同一订阅下的服务中受支持的 Azure 数据源。

+ 采样基于源数据的一个子集。 对于大型数据源，向导可能会丢失字段。 如果采样不足，则可能需要扩展架构或纠正推断的数据类型。

+ AI 扩充（如门户中所公开）仅限一些内置的技能。 

+ 可以通过向导创建的[知识库](knowledge-store-concept-intro.md)仅限于几个默认投影。 如果要保存向导创建的已扩充文档，则 blob 容器和表将附带默认名称和结构。

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>数据源输入

**导入数据**向导使用 Azure 搜索索引器提供的内部逻辑连接到外部数据源，这些索引器可用于采样源、读取元数据、破解文档以读取内容和结构，以及将内容序列化为 JSON以后导入到 Azure 搜索。

您只能从单个表、数据库视图或等效的数据结构导入，但结构可能包含层次结构或嵌套的子结构。 有关详细信息，请参阅[如何为复杂类型建模](search-howto-complex-data-types.md)。

在运行该向导之前应创建此单个表或视图，并且它必须包含内容。 出于明显原因，对空数据源运行 "**导入数据**" 向导并无意义。

|  选项 | 描述 |
| ---------- | ----------- |
| **现有数据源** |如果已在搜索服务中定义索引器，则可能具有可重复使用的现有数据源定义。 在 Azure 搜索中，索引器仅使用数据源对象。 您可以通过编程方式或通过**导入数据**向导来创建数据源对象，并根据需要重新使用它们。|
| **示例**| Azure 搜索提供了两个教程和快速入门中使用的内置示例数据源：一个房地产 SQL 数据库和一个托管在 Cosmos DB 上的酒店数据库。 有关基于酒店样品的演练，请参阅在 Azure 门户快速入门[中创建索引](search-get-started-portal.md)。 |
| [**Azure SQL 数据库**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |可以在此页上或通过 ADO.NET 连接字符串，指定服务名称、具有读取权限的数据库用户的凭据和数据库名称。 选择要查看或自定义属性的连接字符串选项。 <br/><br/>必须在此页上指定提供行集的表或视图。 连接成功后会显示此选项，并提供下拉列表以便可以进行选择。|
| **Azure VM 上的 SQL Server** |指定完全限定的服务名、用户 ID 和密码以及数据库作为连接字符串。 若要使用此数据源，以前必须已在加密连接的本地存储中安装了证书。 如需说明，请参阅[与 Azure 搜索的 SQL VM 连接](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。 <br/><br/>必须在此页上指定提供行集的表或视图。 连接成功后会显示此选项，并提供下拉列表以便可以进行选择。 |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|要求包括帐户、数据库和集合。 集合中的所有文档都将包含在索引中。 您可以定义一个查询以平展或筛选行集，或将查询留空。 不需要在此向导中进行查询。|
| [**Azure Blob 存储**](search-howto-indexing-azure-blob-storage.md) |要求包括存储帐户和容器。 （可选）如果 blob 名称遵循用于分组的虚拟命名约定，可以将名称的虚拟目录部分指定为容器下的某个文件夹。 有关详细信息，请参阅[为 Blob 存储编制索引](search-howto-indexing-azure-blob-storage.md)。 |
| [**Azure 表存储**](search-howto-indexing-azure-tables.md) |要求包括存储帐户和表名。 （可选）可以指定一个查询来检索表的子集。 有关详细信息，请参阅[为表存储编制索引](search-howto-indexing-azure-tables.md)。 |

## <a name="wizard-output"></a>向导输出

在幕后，向导将创建、配置和调用以下对象。 向导运行完成后，可以在门户页中找到它的输出。 服务的 "概述" 页包含索引、索引器、数据源和技能集的列表。 可以在门户中以完整的 JSON 格式查看索引定义。 对于其他定义，可以使用[REST API](https://docs.microsoft.com/rest/api/searchservice/)获取特定的对象。

| 对象 | 描述 | 
|--------|-------------|
| [数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | 将连接信息保存到源数据，包括凭据。 数据源对象专用于索引器。 | 
| [索引](https://docs.microsoft.com/rest/api/searchservice/create-index) | 用于全文搜索和其他查询的物理数据结构。 | 
| [技能组合](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | 用于操作、转换和调整内容的完整说明集，包括分析和提取图像文件中的信息。 除了非常简单的结构有限以外，它还包括对提供扩充的认知服务资源的引用。 此外，它还可能包含知识存储定义。  | 
| [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | 一个配置对象，用于指定数据源、目标索引、可选的技能组合、可选计划以及用于错误处理和64编码的可选配置设置。 |


## <a name="how-to-start-the-wizard"></a>如何启动向导

"导入数据" 向导从 "服务概述" 页上的命令栏开始。

1. 在 [Azure 门户](https://portal.azure.com)中，从仪表板打开搜索服务页，或者在服务列表中[查找服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

2. 在顶部服务概述页中，单击“导入数据”。

   ![门户中的 "导入数据" 命令](./media/search-import-data-portal/import-data-cmd2.png "启动“导入数据”向导")

还可以启动从其他 Azure 服务（包括 Azure Cosmos DB、Azure SQL 数据库和 Azure Blob 存储）**导入数据**。 在服务概述页上的左侧导航窗格中寻找“添加 Azure 搜索”。

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>如何在向导中编辑或完成索引架构

向导将生成一个不完整的索引，该索引将用从输入数据源获取的文档填充。 对于功能索引，请确保定义了以下元素。

1. 字段列表是否已完成？ 添加采样未命中的新字段，并删除任何不会向搜索体验添加值或不会在[筛选器表达式](search-query-odata-filter.md)或[计分配置文件](index-add-scoring-profiles.md)中使用的值。

1. 数据类型是否适合传入数据？ Azure 搜索支持[实体数据模型（EDM）数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)。 对于 Azure SQL 数据，存在布局等效值的[映射图](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#mapping-between-sql-and-azure-search-data-types)。 有关更多背景信息，请参阅[字段映射和转换](search-indexer-field-mappings.md)。

1. 是否有一个可以用作*密钥*的字段？ 此字段必须为 Edm，并且它必须唯一标识文档。 对于关系数据，可以将其映射到主键。 对于 blob，它可能是 `metadata-storage-path`。 如果字段值包含空格或短划线，则必须在“高级选项”下的“创建索引器”步骤中设置“Base 64 编码密钥”选项以禁止验证检查这些字符。

1. 设置属性以确定该字段在索引中的使用方式。 

   请花时间执行此步骤，因为属性确定索引中字段的物理表达式。 如果要在以后更改属性，甚至以编程方式更改属性，几乎总是需要删除并重建索引。 可**搜索**和可**检索**的核心属性对存储的影响可[忽略不计](search-what-is-an-index.md#storage-implications)。 启用筛选器并使用建议器会提高存储要求。 
   
   + “可搜索”启用全文搜索。 在自由格式查询或查询表达式中使用的每个字段必须有此属性。 为标记为“可搜索”的每个字段创建反向索引。

   + “可检索”在搜索结果中返回该字段。 用于提供内容以搜索结果的每个字段必须有此属性。 设置此字段不会明显影响索引大小。

   + “可筛选”允许在筛选表达式中引用该字段。 在 $filter 表达式中使用的每个字段必须有此属性。 筛选表达式用于精确匹配项。 由于文本字符串保持不变，因此需要额外的存储空间来容纳逐字内容。

   + “可查找”为分面导航启用该字段。 只有也标记为“可筛选”的字段可标记为“可查找”。

   + “可排序”允许在排序中使用该字段。 在 $Orderby 表达式中使用的每个字段必须有此属性。

1. 是否需要[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)？ 对于可**搜索**的 Edm 字段，你可以设置**分析器**（如果你想要对其进行语言增强索引和查询）。 

   默认值为“标准 Lucene”，但如果想要使用 Microsoft 的分析器以进行高级词汇处理（如解决不规则名词和动词形式），可选择“Microsoft 英语”。 只能在门户中指定语言分析器。 使用自定义分析器或非语言分析器（如关键字、模式等）必须以编程方式完成。 有关分析器的详细信息，请参阅[添加语言分析器](search-language-support.md)。

1. 是否需要 typeahead 功能的形式为自动完成或建议的结果？ 选中 "**建议器**" 复选框，以在所选字段中启用[typeahead 查询建议和自动完成](index-add-suggesters.md)。 建议器添加到索引中的标记化字词的数目，因此会消耗更多存储。


## <a name="next-steps"></a>后续步骤

了解向导的优点和限制的最佳方式是单步执行此向导。 以下快速入门指导你完成每个步骤。

> [!div class="nextstepaction"]
> [使用 Azure 门户创建 Azure 搜索索引](search-get-started-portal.md)
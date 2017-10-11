---
title: "创建索引（门户 - Azure 搜索）| Microsoft Docs"
description: "使用 Azure 门户创建索引。"
services: search
manager: jhubbard
author: heidisteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: a7d98ab0937a7d3f932d5df34c19ae091129804e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>使用 Azure 门户创建 Azure 搜索索引
> [!div class="op_single_selector"]
> * [概述](search-what-is-an-index.md)
> * [门户](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

使用 Azure 门户中内置的索引设计器创建要在 Azure 搜索服务上运行的[搜索索引](search-what-is-an-index.md)或构建其原型。 

## <a name="prerequisites"></a>先决条件

本文假设读者拥有 [Azure 订阅](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)和 [Azure 搜索服务](search-create-service-portal.md)。  

## <a name="find-your-search-service"></a>查找搜索服务
1. 登录到 Azure 门户页并查看[你的订阅的搜索服务](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. 选择 Azure 搜索服务。

## <a name="name-the-index"></a>为索引命名

1. 单击页面顶部的命令栏中的“添加索引”按钮。
2. 命名 Azure 搜索索引。 
   * 以字母开头。
   * 仅使用小写字母、数字或短划线（“-”）。
   * 名称的长度上限为 60 个字符。

  索引名称将成为在到索引的连接上使用的且用于在 Azure 搜索 REST API 中发送 HTTP 请求的终结点 URL 的一部分。

## <a name="define-the-fields-of-your-index"></a>定义索引的字段

索引构成包括一个“字段集合”，它定义了索引中的可搜索数据。 更具体地说，它指定你单独上传的文档的结构。 字段集合包括必需字段和可选字段（已命名的和键入的），并具有决定了字段使用方式的索引属性。

1. 在“添加索引”边栏选项卡中，单击“字段 >”以滑动打开字段定义边栏选项卡。 

2. 接受生成的类型为 Edm.String 的“键”字段。 默认情况下，该字段名为 *id*，但可以将其重命名，只要该字符串符合[命名规则](https://docs.microsoft.com/rest/api/searchservice/Naming-rules)即可。 对于每个 Azure 搜索索引来说，键字段都是必需的，并且它必须是一个字符串。

3. 添加字段来完整指定将要上传的文档。 如果文档包含 *id*、*hotel name*、*address*、*city* 和 *region*，请在索引中为每个字段创建对应的字段。 有关设置属性方面的帮助信息，请查看[下一部分中的设计指南](#design)。

4. （可选）添加在筛选表达式中内部使用的任何字段。 可以设置字段的属性来将字段从搜索操作中排除。

5. 完成时，单击“确定”以保存并创建索引。

## <a name="tips-for-adding-fields"></a>有关添加字段的提示

在门户中创建索引是键盘密集型操作。 可以通过遵循以下工作流尽量减少步骤：

1. 首先，通过输入名称并设置数据类型来构建字段列表。

2. 接下来，使用每个属性顶部的复选框来批量为所有字段启用设置，然后，有选择性地为不需要选中复选框的少数字段清除复选框。 例如，字符串字段通常是可搜索的。 在这种情况下，可以单击“可检索”和“可搜索”以便在搜索结果中返回字段的值，同时允许对字段进行全文搜索。 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>用于设置属性的设计指南

尽管可以随时添加新字段，但在索引的生存期内现有字段定义将被锁定。 因此，开发人员通常使用门户创建简单索引、测试创意，或者使用门户页面来查找设置。 如果采用基于代码的方式以便可以轻松重新生成索引，那么对索引进行频繁迭代的设计就更为高效。

在保存索引之前，将分析器和建议器与字段进行关联。 请确保单击通过每个选项卡式页面来向索引定义添加语言分析器和建议器。

字符串字段通常标记为“可搜索”和“可检索”。

用来缩小搜索结果范围的字段包括“可排序”、“可筛选”和“可查找”。

字段属性决定了字段的使用方式，例如，是否用于全文搜索、分面导航和排序等操作中。 下表介绍了每个属性。

|属性|说明|  
|---------------|-----------------|  
|**可搜索**|可全文搜索，在编制索引期间遵从语法分析，例如分词。 如果将某个可搜索字段设置为“sunny day”之类的值，在内部它将拆分为单独的标记“sunny”和“day”。 有关详细信息，请参阅[全文搜索工作原理](search-lucene-query-architecture.md)。|  
|**可筛选**|在 **$filter** 查询中引用。 `Edm.String` 或 `Collection(Edm.String)` 类型的可筛选字段不进行分词，因此，比较仅用于查找完全匹配项。 例如，如果将此类字段 f 设置为“sunny day”，则 `$filter=f eq 'sunny'` 将找不到任何匹配项，但 `$filter=f eq 'sunny day'` 可找到。 |  
|**可排序**|默认情况下，系统按分数对结果进行排序，但可以配置基于文档中字段的排序。 `Collection(Edm.String)` 类型的字段不能为“可排序”。 |  
|**可查找**|通常用于包括了按类别（例如特定城市中的宾馆）的命中次数的搜索结果呈现中。 此选项无法与 `Edm.GeographyPoint` 类型的字段一起使用。 `Edm.String` 类型的**可筛选**、**可排序**或**可查找**字段的长度最多可以是 32 千字节。 有关详细信息，请参阅[创建索引 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)。|  
|**键**|文档在索引内的唯一标识符。 必须仅选择单个字段作为键字段，并且它必须是 `Edm.String` 类型的。|  
|**可检索**|决定了是否可以在搜索结果中返回此字段。 当希望将某个字段（例如“利润”）用作筛选器、排序或评分机制，但不希望该字段显示给最终用户时，这很有用。 对于 `key` 字段，此属性必须为 `true`。|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>创建示例 API 部分中使用的 hotels 索引

Azure 搜索 API 文档包括了代码示例，其中采用了简单的 *hotels* 索引。 在下面的屏幕截图中，可以看到索引定义，包括在定义索引期间指定的法语语言分析器，可以在门户中练习重新创建该索引定义。

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>后续步骤

在创建 Azure 搜索索引后，可以前进到下一步骤：[将可搜索数据上传到索引中](search-what-is-data-import.md)。

另外，也可以更深入地了解索引。 除了字段集合之外，索引还指定分析器、建议器、评分配置文件和 CORS 设置。 门户中提供了用于定义最常用元素（字段、分析器和建议器）的选项卡式页面。 若要创建或修改其他元素，可以使用 REST API 或 .NET SDK。

## <a name="see-also"></a>另请参阅

 [全文搜索工作原理](search-lucene-query-architecture.md)  
 [搜索服务 REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)


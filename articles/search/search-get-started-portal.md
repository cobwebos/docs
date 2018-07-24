---
title: 有关如何使用门户在 Azure 搜索中进行索引编制、查询和筛选的教程 | Microsoft Docs
description: 本教程介绍如何使用 Azure 门户和预定义的示例数据在 Azure 搜索中生成索引。 探索全文搜索、筛选器、分面 (Facet)、模糊搜索、地域搜索等功能。
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 124963359d0b2d4050156958de195e47b9331c92
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007978"
---
# <a name="tutorial-use-built-in-tools-for-azure-search-indexing-and-queries"></a>教程：使用内置工具在 Azure 搜索中进行索引编制和查询

在 Azure 门户中 Azure 搜索服务页上，只需付出小小的努力，就能使用内置工具进行概念测试和动手实践。 门户工具不能完全与 .NET 和 REST API 搭配使用，但对于快速概念证明测试，向导和编辑器能够提供方便的辅助。 这篇不包含代码的简介可帮助你开始使用一个小型的已发布数据集立即编写有趣的查询。 

> [!div class="checklist"]
> * 首先从公共示例数据着手，使用“导入数据”向导自动生成 Azure 搜索索引。 
> * 查看已发布到 Azure 搜索的任何索引的索引架构和属性。
> * 使用“搜索浏览器”探索全文搜索、筛选器、分面、模糊搜索和地域搜索。  

门户工具并不支持完整的 Azure 搜索功能。 如果这些工具受到过多的限制，请考虑参阅[在 .NET 中进行基于代码的 Azure 搜索编程简介](search-howto-dotnet-sdk.md)，或使用[用于发出 REST API 调用的 Web 测试工具](search-fiddler.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 也可以花费 3 分钟时间观看这部 [Azure 搜索概述视频](https://channel9.msdn.com/Events/Connect/2016/138)，其中对本教程所述的步骤做了 6 分钟的演示。

## <a name="prerequisites"></a>先决条件

[创建 Azure 搜索服务](search-create-service-portal.md)，或者在当前订阅下找到一个现有的服务。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开 Azure 搜索服务的服务仪表板。 如果未将服务磁贴固定到仪表板，可通过以下方式找到服务： 
   
   * 在跳转栏中，单击左侧导航窗格中的“所有服务”。
   * 在搜索框中，键入“搜索”获取订阅的搜索相关服务列表。 单击“搜索服务”。 服务应会出现在该列表中。 

### <a name="check-for-space"></a>检查空间
很多客户开始使用免费服务。 此版本限制为三个索引、三个数据源和三个索引器。 在开始之前，请确保有空间存储额外的项目。 本教程会创建每个对象的一个实例。 

> [!TIP] 
> 服务仪表板中的磁贴你将已有多少个索引、索引器和数据源。 “索引器”磁贴显示成功和失败指示器。 单击该磁贴可查看索引器计数。 
>
> ![索引器和数据源的磁贴][1]
>

## <a name="create-index"></a> 创建索引并加载数据
搜索查询将循环访问[索引](search-what-is-an-index.md)，索引中包含可搜索数据、元数据，以及用于优化某些搜索行为的构造。

为了在门户中完成此任务，我们将使用可通过“导入数据”向导利用[索引器](search-indexer-overview.md)进行爬网的内置示例数据集。 索引器是特定于源的爬网程序，可以从支持的 Azure 数据源中读取元数据和内容。 在代码中，可以创建索引器并将其作为独立的资源进行管理。 在门户中，索引器通过“导入数据”向导公开。 

#### <a name="step-1-start-the-import-data-wizard"></a>步骤 1：启动“导入数据”向导
1. 在 Azure 搜索服务仪表板上，单击命令栏中的“导入数据”  ，以启动用于创建和填充索引的向导。
   
    ![导入数据命令][2]

2. 在向导中，单击“连接到数据” > “示例” > “realestate-us-sample”。 此数据源中已预先配置名称、类型和连接信息。 创建后，它将成为可在其他导入操作中重复使用的“现有数据源”。

    ![选择示例数据集][9]

3. 单击“确定”使用该数据集。

#### <a name="skip-cognitive-skills"></a>跳过认知技能

“导入数据”包括一个可选的认知技能步骤，该步骤将 AI 算法添加到索引。 本教程不会介绍此功能，因此，请直接跳到“自定义目标索引”。 如果你很想了解 Azure 搜索中新的认知搜索预览版功能，请尝试阅读[认知搜索快速入门](cognitive-search-quickstart-blob.md)或[教程](cognitive-search-tutorial-blob.md)。

   ![跳过认知技能步骤][11]

#### <a name="step-2-define-the-index"></a>步骤 2：定义索引
创建索引通常是基于代码的手动过程，但向导可以针对它能够爬网的所有数据源生成索引。 索引至少需要使用一个名称和一个字段集合（其中一个字段标记为文档键）来唯一标识每个文档。

字段包含数据类型和属性。 顶部的复选框为*索引属性*，用于控制如何使用字段。 

* **可检索**意味着该字段将显示在搜索结果列表中。 清除此复选框即可将单个字段标记为关闭搜索结果限制，例如当字段仅用于筛选器表达式时。 
* “可筛选”、“可排序”和“可查找”确定字段是否可用于筛选器、排序或方面导航结构。 
* **可搜索**意味着该字段将包括在全文搜索中。 字符串可搜索。 数值字段和布尔字段通常标记为不可搜索。 

默认情况下，向导会在数据源中扫描用作键字段基础的唯一标识符。 字符串经过属性化，可检索且可搜索。 整数经过属性化，可检索、可筛选、可排序且可分面。

  ![生成的 realestate 索引][3]

单击“确定”创建该索引。

#### <a name="step-3-define-the-indexer"></a>步骤 3：定义索引器
仍在“导入数据”向导中，单击“索引器” > “名称”，并键入索引器的名称。 

此对象定义一个可执行过程。 可将该对象放入定期计划，但我们暂时单击“确定”，使用默认选项立即运行索引器一次。  

  ![realestate 索引器][8]

## <a name="check-progress"></a>检查进度
要监视数据导入，请返回服务仪表板并向下滚动，并双击“索引器”磁贴打开索引器列表。 列表中应会出现新建的索引器，其状态指示“正在进行”或“成功”，此外还会列出已编制索引的文档数。

   ![索引器进度消息][4]

## <a name="view-the-index"></a>查看索引

服务仪表板中的磁贴提供摘要信息以及对详细信息的访问。 例如，在“索引”磁贴中，应会看到现有索引的列表，包括刚刚在上一步骤中创建的 *realestate-us-sample* 索引。

现在请单击“realestate-us-sample”索引，以查看该索引的定义的门户选项。 使用“添加/编辑字段”选项可以创建新字段并完全指定其属性。 现有字段在 Azure 搜索中具有实际的表示形式，因此不可修改，即使在代码中也是如此。 若要从根本上更改现有字段，请创建新字段并丢弃原始字段。 

   ![示例索引定义][10]

随时可以添加其他构造，例如评分配置文件和 CORS 选项。 

若要清楚地了解在索引设计过程中可以和不可以编辑哪些内容，请花点时间查看索引定义选项。 灰显的选项表示对应的值不可修改或删除。

## <a name="query-index"></a> 查询索引
我们继续。现在应已创建了一个可以使用内置[**搜索资源管理器**](search-explorer.md)查询页查询的搜索索引。 该页提供了一个搜索框，用于测试任意查询字符串。 

> [!TIP]
> 在 [Azure 搜索概述视频](https://channel9.msdn.com/Events/Connect/2016/138)中，从第 6 分 8 秒开始的片段演示了以下步骤。
>

1. 单击命令栏上的“搜索浏览器”  。

   ![搜索浏览器命令][5]

2. 在命令栏中单击“更改索引”切换到 *realestate-us-sample*。 在命令栏中单击“设置 API 版本”，查看有哪些 REST API 可用。 对于以下查询，请使用正式版 (2017-11-11)。 

   ![索引和 API 命令][6]

3. 在搜索栏中输入以下查询字符串，并单击“搜索”。

    > [!NOTE]
    > “搜索资源管理器”仅供用于处理 [REST API 请求](https://docs.microsoft.com/rest/api/searchservice/search-documents)。 搜索浏览器接受[简单查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)和[完整 Lucene 查询分析器](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)的语法，加上可在[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)操作中使用的所有搜索参数。
    > 


#### <a name="example-string-searchseattle"></a>示例（字符串）：`search=seattle`

+ **search** 参数用于输入关键字来执行全文搜索，在本例中，将返回华盛顿州金县的结果列表，其中包含文档中任何可搜索字段内的 *Seattle*。 

+ **搜索浏览器**以 JSON 格式返回结果，如果文档采用密集结构，这种结果将很冗长且难以阅读。 这是有意而为的；整个文档的可见性是一个重要因素，尤其是在测试期间。 为了改善用户体验，需要编写代码用于[处理搜索结果](search-pagination-page-layout.md)，以提供重要元素。

+ 文档由标记为在索引中“可检索”的所有字段构成。 若要在门户中查看索引属性，请在“索引”磁贴中单击“realestate-us-sample”。

#### <a name="example-parameterized-searchseattlecounttruetop100"></a>示例（参数化）：`search=seattle&$count=true&$top=100`

+ **&** 符号用于追加可以按任意顺序指定的搜索参数。 

+  **$count=true** 参数返回所有已返回文档的总计数。 此值显示在搜索结果顶部附近。 可以通过监视 **$count=true** 报告的更改来验证筛选器查询。 如果计数较小，则表示筛选器正在工作。

+ **$top=100** 返回所有文档中排名最高的 100 个文档。 默认情况下，Azure 搜索返回前 50 个最佳匹配项。 可以通过 **$top** 增加或减少返回的结果。

## <a name="filter-query"></a> 筛选查询

追加 **$filter** 参数时，会将筛选器包括在搜索请求中。 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>示例（已筛选）：`search=seattle&$filter=beds gt 3`

+ **$filter** 参数返回与提供的条件匹配的结果。 在本例中，条件为卧室数大于 3。 

+ 筛选器语法是一种 OData 构造。 有关详细信息，请参阅 [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)（筛选器 OData 语法）。

## <a name="facet-query"></a> 分面查询

分面筛选器包括在搜索请求中。 可以使用分面参数，返回与所提供分面值匹配的文档的聚合计数。 

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>示例（使用范围缩减进行分面）：`search=*&facet=city&$top=2`

+ **search=*** 是空搜索。 空搜索会搜索所有内容。 提交空查询的原因之一是针对整个文档集进行筛选器或分面。 例如，你希望某个分面导航结构由索引中的所有城市组成。

+  **facet** 返回可传递给 UI 控件的导航结构。 它将返回类别和计数。 在本例中，类别基于城市数目。 Azure 搜索中没有聚合，但可以通过 `facet` 进行近似聚合，提供每个类别中的文档计数。

+ **$top=2** 返回两个文档，演示如何使用 `top` 来减少或增加结果。

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>示例（包含数字值的分面）：`search=seattle&facet=beds`**

+ 此查询针对 *Seattle* 执行文本搜索后返回的床位分面。 可将 beds 一词指定为分面，因为该字段已标记为可在索引中检索、筛选和分面，并且它包含的值（数字 1 到 5）适合用于将列表分类为组（包含 3 间卧室和 4 间卧室的房屋列表）。 

+ 只有可筛选的字段才可分面。 结果中只返回仅可检索的字段。

## <a name="highlight-query"></a> 添加突出显示

搜索词突出显示是对与关键字匹配的文本设置的格式，表示在特定字段中找到的匹配项。 如果搜索词深藏在说明中，可以添加搜索词突出显示来方便找到这些词。 

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>示例（突出显示）：`search=granite countertops&highlight=description`

+ 在此示例中，格式化短语 granite countertops 更容易在说明字段中发现。

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>示例（语言分析）：`search=mice&highlight=description`

+ 全文搜索将查找具有类似语义的单词形式。 在本例中，搜索结果包含搜索“mice”关键字后，包含“mouse”的突出显示文本（针对老鼠泛滥的家庭执行的搜索）。 由于执行了语言分析，同一单词的不同形式可能会显示在结果中。 

+ Azure 搜索支持 Lucene 和 Microsoft 提供的 56 种分析器。 Azure 搜索使用的默认分析器是标准的 Lucene 分析器。 

## <a name="fuzzy-search"></a> 试用模糊搜索

默认情况下，执行典型的搜索时，如果拼错查询字词（例如，将西雅图地区的 Samammish 高原拼写为 *samamish*），则无法返回匹配项。 以下示例不会返回任何结果。

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>示例（拼错字词且未经处理）：`search=samamish`

若要处理拼写错误，可以使用模糊搜索。 使用完整的 Lucene 查询语法时，将启用模糊搜索：在查询中设置 **queryType=full**，以及将 **~** 追加到搜索字符串时，就会启用模糊搜索。 

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>示例（拼错字词且已经处理）：`search=samamish~&queryType=full`

现在，此示例会返回包含“Sammamish”匹配项的文档。

如果未指定 **queryType**，将使用默认的简单查询分析器。 简单查询分析器速度更快，但如果需要执行模糊搜索、正则表达式、近似搜索或其他高级查询类型，则需要使用完整语法。 

模糊搜索和通配符搜索对搜索输出产生影响。 不会针对这些查询格式执行语言分析。 在使用模糊搜索和通配符搜索之前，请查看 [Azure 搜索中全文搜索的工作原理](search-lucene-query-architecture.md#stage-2-lexical-analysis)，并查找有关词法分析例外情况的部分。

有关完整查询分析器支持的查询方案的详细信息，请参阅 [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)（Azure 搜索中的 Lucene 查询语法）。

## <a name="geo-search"></a> 尝试地理空间搜索

对于包含坐标的字段，支持通过 [edm.GeographyPoint 数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)执行地理空间搜索。 地域搜索是 [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)（筛选器 OData 语法）中指定的一种筛选器类型。 

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>示例（地理坐标筛选器）：`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

该示例查询将筛选位置数据的所有结果，这些结果与给定地点（以纬度和经度坐标的形式指定）之间的距离小于 5 公里。 通过添加 **$count**，可以查看在更改距离或坐标时会返回多少个结果。 

如果搜索应用程序具有“附近查找”功能或使用地图导航，地理空间搜索非常有用。 但它不属于全文搜索。 如果用户要求是按名称搜索城市或国家/地区，请添加包含该城市或国家/地区的名称的字段，并添加坐标。

## <a name="takeaways"></a>要点

本教程演示了在 Azure 门户中使用“导入数据”向导和“搜索资源管理器”的基本步骤。

其中介绍了[索引器](search-indexer-overview.md)（“导入数据”向导背后的推动力），以及索引设计的基本工作流，包括[已发布的索引支持的修改](ttps://docs.microsoft.com/rest/api/searchservice/update-index)。 

本教程还通过一些演示筛选器、搜索词突出显示、模糊搜索和地理搜索等重要功能的实践示例，介绍了查询语法。

最后，介绍了如何通过在仪表板中单击任何索引、索引器或者为订阅创建的数据源对应的磁贴来获取信息。 以后当你使用自己的索引或者同事创建的索引时，可以使用门户快速检查数据源定义或字段集合的构造，而无需搜索不太熟悉的代码。

## <a name="clean-up-resources"></a>清理资源

完成本教程后，最快的清理方式是删除包含 Azure 搜索服务的资源组。 现在，可以删除资源组以永久删除其中的所有内容。 在门户中，资源组名称显示在 Azure 搜索服务的“概述”页上。

## <a name="next-steps"></a>后续步骤

若要对 Azure 搜索进行其他基于工具的探索，请考虑使用 Postman 或 Fiddler 等 REST 测试工具：

> [!div class="nextstepaction"]
> [用于调用 Azure 搜索 REST API 的 Web 测试工具](search-fiddler.md)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
[10]: ./media/search-get-started-portal/sample-index-def.png
[11]: ./media/search-get-started-portal/skip-cog-skill-step.png
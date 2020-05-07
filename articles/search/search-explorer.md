---
title: Azure 门户中的“搜索浏览器”查询工具
titleSuffix: Azure Cognitive Search
description: 在此 Azure 门户快速入门中，使用“搜索浏览器”了解查询语法、测试查询表达式或检查搜索文档。 搜索浏览器在 Azure 认知搜索中查询索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80369655"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>快速入门：在门户中使用“搜索浏览器”来运行查询

“搜索浏览器”  是一种内置查询工具，用于针对 Azure 认知搜索中的搜索索引运行查询。 利用此工具，可以轻松学习查询语法，测试查询或筛选器表达式，或者通过验证是否存在较新的内容来确认索引刷新结果。

本快速入门使用 realestate-us-sample-index  来演示搜索浏览器。 请求是使用[搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/) 表述的，响应以 JSON 文档的形式返回。

## <a name="prerequisites"></a>先决条件

+ [创建 Azure 认知搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。

+ 本快速入门使用了 realestate-us-sample-index  。 分步完成[导入数据  ](search-import-data-portal.md)向导，以便基于内置的示例数据源生成索引。

## <a name="start-search-explorer"></a>启动搜索浏览器

1. 在 [Azure 门户](https://portal.azure.com)中，从仪表板打开搜索服务页，或者[查找你的服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

1. 从命令栏打开“搜索浏览器”：

   ![门户中的“搜索浏览器”命令](./media/search-explorer/search-explorer-cmd2.png "门户中的“搜索浏览器”命令")

    或在打开的索引上使用嵌入的“搜索浏览器”  选项卡：

   ![“搜索浏览器”选项卡](./media/search-explorer/search-explorer-tab.png "“搜索浏览器”选项卡")

## <a name="unspecified-query"></a>未指定查询

若要首先查看内容，请执行空搜索，方法是单击“搜索”，不提供搜索词  。 空搜索作为第一个查询十分有用，因为它返回全部文档，以便查看文档组合。 空搜索没有搜索级别，按任意顺序返回文档（所有文档都为 `"@search.score": 1`）。 默认情况下，搜索请求中会返回 50 个文档。

空搜索的等效语法是 `*` 或 `search=*`。
   
   ```http
   search=*
   ```

   **结果**
   
   ![空查询示例](./media/search-explorer/search-explorer-example-empty.png "不合格查询或空查询示例")

## <a name="free-text-search"></a>自由文本搜索

自由格式查询（带或不带运算符）可用于模拟从自定义应用发送到 Azure 认知搜索的用户定义查询。 只会扫描在索引定义中将其属性设置为“可搜索”  的那些字段来查找匹配项。 

请注意，如果提供了搜索条件（例如查询词或表达式），则会应用搜索排名。 以下示例对自定义文本搜索进行了说明。

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **结果**

   可以使用 Ctrl-F 在结果中搜索感兴趣的特定字词。

   ![自由文本查询示例](./media/search-explorer/search-explorer-example-freetext.png "自由文本查询示例")

## <a name="count-of-matching-documents"></a>匹配文档计数 

添加 $count=true  以获取在索引中找到的匹配项数。 在空搜索中，计数是指索引中的文档总数。 在限定搜索中，计数是与查询输入匹配的文档数。

   ```http
   $count=true
   ```

   **结果**

   ![文档计数示例](./media/search-explorer/search-explorer-example-count.png "索引中的匹配文档计数")

## <a name="limit-fields-in-search-results"></a>限制搜索结果中的字段

添加 [$select  ](search-query-odata-select.md)，将结果限制为显式命名的字段，以便在“搜索浏览器”中获得可读性更强的输出  。 若要保留搜索字符串“$count=true”，请在参数前面加上 &   。 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **结果**

   ![限制字段示例](./media/search-explorer/search-explorer-example-selectfield.png "限制搜索结果中的字段")

## <a name="return-next-batch-of-results"></a>返回下一批结果

Azure 认知搜索根据搜索排名返回排名前 50 的匹配项。 若要获取下一组匹配的文档，请附加“$top=100,&$skip=50”，这会将结果集增加为 100 个文档（默认值为 50，最大值为 1000）并跳过前 50 个文档  。 前面提到，需要提供搜索条件，例如查询词或表达式，以便获得排列好的结果。 请注意，搜索分数随搜索结果中搜索的深入而降低。

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **结果**

   ![批量搜索结果](./media/search-explorer/search-explorer-example-topskip.png "返回下一批搜索结果")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>筛选表达式（大于、小于、等于）

如果要指定精确条件搜索，而不是进行自定义文本搜索，请使用 [$filter  ](search-query-odata-filter.md) 参数。 必须在索引中将此字段的属性设置为“可筛选”。  此示例搜索大于 3 间的卧室：

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **结果**

   ![筛选器表达式](./media/search-explorer/search-explorer-example-filter.png "按条件筛选")

## <a name="order-by-expressions"></a>Order-by 表达式

添加 [$orderby  ](search-query-odata-orderby.md)，按搜索分数之外的其他字段对结果排序。 必须在索引中将此字段的属性设置为“可排序”。  可用于测试此功能的示例表达式如下所示：

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **结果**

   ![Orderby 表达式](./media/search-explorer/search-explorer-example-ordery.png "更改排序顺序")

“$filter”和“$orderby”表达式都是 OData 构造   。 有关详细信息，请参阅 [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)（筛选器 OData 语法）。

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>要点

在本快速入门中，你使用“搜索浏览器”  通过 REST API 查询了一个索引。

+ 结果会作为详细的 JSON 文档返回，以便可以完整地查看文档的构建情况和内容。 可以使用示例中显示的查询表达式来限制返回的字段。

+ 文档由标记为在索引中“可检索”的所有字段构成  。 若要在门户中查看索引属性，请单击搜索概述页面上“索引”列表中的“realestate-us-sample”   。

+ 自由格式查询类似于在商用 Web 浏览器中输入的内容，可用于测试最终用户体验。 例如，假设有一个内置的房地产示例索引，可以输入“华盛顿湖西雅图公寓”，再使用 Ctrl-F 在搜索结果中查找字词。 

+ 查询和筛选器表达式使用 Azure 认知搜索支持的语法来表达。 默认为[简单语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)，但可选择使用[完整 Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 进行更强大的查询。 [筛选表达式](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)采用的是 OData 语法。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接   ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

若要详细了解查询结构和语法，请使用 Postman 或某个等效工具创建可利用该 API 的更多部分的查询表达式。 [搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/) 对于学习和探索特别有用。

> [!div class="nextstepaction"]
> [使用 Postman 创建基本查询](search-query-simple-examples.md)
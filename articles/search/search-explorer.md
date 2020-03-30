---
title: Azure 门户中的搜索浏览器查询工具
titleSuffix: Azure Cognitive Search
description: 在此 Azure 门户快速入门中，使用搜索资源管理器学习查询语法、测试查询表达式或检查搜索文档。 搜索资源管理器查询 Azure 认知搜索中的索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369655"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>快速入门：使用搜索资源管理器在门户中运行查询

**搜索资源管理器**是一种内置查询工具，用于在 Azure 认知搜索中针对搜索索引运行查询。 此工具通过验证存在较新的内容，可以轻松地学习查询语法、测试查询或筛选器表达式或确认索引刷新的结果。

此快速入门使用**房地产-我们样本索引**来演示搜索资源管理器。 请求使用[搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)进行配制，响应返回为 JSON 文档。

## <a name="prerequisites"></a>先决条件

+ [创建 Azure 认知搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。

+ **房地产-我们样本指数**用于此快速启动。 逐步执行[**导入数据**](search-import-data-portal.md)向导，从内置示例数据源生成索引。

## <a name="start-search-explorer"></a>启动搜索资源管理器

1. 在[Azure 门户](https://portal.azure.com)中，从仪表板打开搜索服务页面或[查找服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

1. 从命令栏打开搜索资源管理器：

   ![门户中的搜索浏览器命令](./media/search-explorer/search-explorer-cmd2.png "门户中的搜索浏览器命令")

    或者使用打开索引上的嵌入**搜索资源管理器**选项卡：

   ![搜索资源管理器选项卡](./media/search-explorer/search-explorer-tab.png "搜索资源管理器选项卡")

## <a name="unspecified-query"></a>未指定的查询

对于第一次查看内容，请单击"**搜索**"，但未提供任何字词，即可执行空搜索。 空搜索作为第一个查询十分有用，因为它返回全部文档，以便查看文档组合。 空搜索没有搜索级别，按任意顺序返回文档（所有文档都为 `"@search.score": 1`）。 默认情况下，搜索请求中会返回 50 个文档。

空搜索的等效语法是 `*` 或 `search=*`。
   
   ```http
   search=*
   ```

   **结果**
   
   ![空查询示例](./media/search-explorer/search-explorer-example-empty.png "不合格查询或空查询示例")

## <a name="free-text-search"></a>免费文本搜索

自由格式查询（带或不带运算符）可用于模拟从自定义应用发送到 Azure 认知搜索的用户定义查询。 仅扫描索引定义中归结为**可搜索**的字段。 

请注意，当您提供搜索条件（如查询词或表达式）时，搜索排名就会发挥作用。 以下示例对自定义文本搜索进行了说明。

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **结果**

   可以使用 Ctrl-F 在结果中搜索感兴趣的特定字词。

   ![自定义文本查询示例](./media/search-explorer/search-explorer-example-freetext.png "自定义文本查询示例")

## <a name="count-of-matching-documents"></a>匹配文档计数 

添加 **$count_true**以获取索引中找到的匹配项数。 在空搜索中，计数是指索引中的文档总数。 在限定搜索中，计数是与查询输入匹配的文档数。

   ```http
   $count=true
   ```

   **结果**

   ![文档计数示例](./media/search-explorer/search-explorer-example-count.png "索引中的匹配文档计数")

## <a name="limit-fields-in-search-results"></a>限制搜索结果中的字段

添加[**$select**](search-query-odata-select.md)以将结果限制为显式命名的字段，以便**搜索资源管理器**中的更多可读输出。 若要保留搜索字符串“$count=true”，请在参数前面加上 &********。 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **结果**

   ![限制字段示例](./media/search-explorer/search-explorer-example-selectfield.png "限制搜索结果中的字段")

## <a name="return-next-batch-of-results"></a>返回下一批结果

Azure 认知搜索根据搜索级别返回前 50 个匹配项。 若要获取下一组匹配的文档，请附加“$top=100,&$skip=50”，这会将结果集增加为 100 个文档（默认值为 50，最大值为 1000）并跳过前 50 个文档****。 前面提到，需要提供搜索条件，例如查询词或表达式，以便获得排列好的结果。 请注意，搜索分数随搜索结果中搜索的深入而降低。

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **结果**

   ![批量搜索结果](./media/search-explorer/search-explorer-example-topskip.png "返回下一批搜索结果")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>筛选表达式（大于、小于、等于）

如果要指定精确的条件而不是自由文本搜索，请使用[**$filter**](search-query-odata-filter.md)参数。 该字段必须归为索引中的**可筛选**字段。 此示例搜索大于 3 间的卧室：

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **结果**

   ![筛选器表达式](./media/search-explorer/search-explorer-example-filter.png "按条件筛选")

## <a name="order-by-expressions"></a>Order-by 表达式

添加[**$orderby**](search-query-odata-orderby.md)以除搜索分数之外的另一个字段对结果进行排序。 该字段必须归为索引中的**可排序**字段。 可用于测试此功能的示例表达式是：

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **结果**

   ![Orderby 表达式](./media/search-explorer/search-explorer-example-ordery.png "更改排序顺序")

“$filter”和“$orderby”表达式都是 OData 构造********。 有关详细信息，请参阅 [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)（筛选器 OData 语法）。

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>要点

在此快速入门中，您可以使用**搜索资源管理器**使用 REST API 查询索引。

+ 结果会作为详细的 JSON 文档返回，以便可以完整地查看文档的构建情况和内容。 可以使用示例中显示的查询表达式来限制返回的字段。

+ 文档由索引中标记为 **"可检索"** 的所有字段组成。 若要在门户中查看索引属性，请单击搜索概述页面上“索引”列表中的“realestate-us-sample”******。

+ 自由格式查询类似于在商用 Web 浏览器中输入的内容，可用于测试最终用户体验。 例如，假设有一个内置的房地产示例索引，可以输入“华盛顿湖西雅图公寓”，再使用 Ctrl-F 在搜索结果中查找字词。 

+ 查询和筛选器表达式在 Azure 认知搜索支持的语法中表示。 默认为[简单语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)，但可选择使用[完整 Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 进行更强大的查询。 [筛选表达式](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)采用的是 OData 语法。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接********，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

要了解有关查询结构和语法的更多信息，请使用 Postman 或等效工具创建利用 API 更多部分的查询表达式。 [搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)特别有助于学习和探索。

> [!div class="nextstepaction"]
> [在邮递员中创建基本查询](search-query-simple-examples.md)
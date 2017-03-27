---
title: "如何在 Azure 搜索中实现分面导航 | Microsoft Docs"
description: "将分面导航添加到与 Azure 搜索集成的应用程序，它是 Microsoft Azure 上的一项云托管的搜索服务。"
services: search
documentationcenter: 
author: HeidiSteen
manager: mblythe
editor: 
ms.assetid: cdf98fd4-63fd-4b50-b0b0-835cb08ad4d3
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 3/10/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 158fb81f7f252fc8e1e4c71a77b1b53ec83cd051
ms.lasthandoff: 03/11/2017


---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>如何在 Azure 搜索中实现分面导航
分面导航是一种筛选机制，用于在搜索应用程序中提供自定向的深化导航。 尽管术语“分面导航”可能较陌生，但可以肯定的是你之前使用过此类导航。 如以下示例所示，分面导航就是用于筛选结果的类别。

 ![][1]

## <a name="why-use-faceted-navigation"></a>为何使用分面导航？
分面导航是备用搜索入口点，提供了一种手动键入复杂搜索表达式的便捷替代方法。 分面可帮助用户查找所需内容，同时确保获取相关结果。 作为开发人员，分面允许你公开用于导航搜索库的最有用的搜索条件。 在在线零售应用程序中，分面导航通常基于品牌、分类（童鞋）、尺寸、价格、受欢迎程度和评级生成。 

搜索技术不同，分面导航的实现也不同。 在 Azure 搜索中，分面导航在查询时生成，使用之前在架构中指定的特性化字段。

-   在应用程序生成的查询中，查询必须发送“分面查询参数”，才能接收该记录结果集的可用分面筛选值。

-   若要实际剪裁记录结果集，应用程序必须应用 `$filter` 表达式。

在应用程序开发中，编写构造查询的代码会产生大量工作。 你想要从分面导航中获取的许多应用程序行为都由该服务提供，包括对定义范围操作和获取分面结果计数操作的内置支持。 该服务还包括合理的默认值，有助于避免庞大的导航结构。 

## <a name="the-basics"></a>基础知识
如果你不熟悉搜索开发，对分面导航定义的最佳理解是它显示了自定向搜索的可能性。 它是一种基于预定义筛选的深化搜索体验，用于通过点击操作快速缩小搜索结果范围。 

### <a name="interaction-model"></a>交互模型

分面导航的搜索体验是迭代的，因此我们可以首先将其理解为展开以响应用户操作的查询序列。

起始点是提供分面导航的应用程序页面，通常位于外围。 分面导航通常是树结构，带有每个值的复选框或可单击文本。 

1. 发送到 Azure 搜索的查询通过一个或多个分面查询参数指定分面导航结构。 例如，查询可能包括 `facet=Rating`，可能通过 `:values` 或 `:sort` 选项进一步优化表示。
2. 通过使用在请求中指定的分面，表示层可呈现提供分面导航的搜索页面。
3. 假设分面导航结构包括“评级”，用户单击“4”，即表示仅应显示评级 4 或更高评级的产品。 
4. 作为响应，应用程序发送一个包括 `$filter=Rating ge 4` 的查询 
5. 表示层会更新页面，显示减少的结果集，以便仅包含符合新筛选条件的项（在此示例中，是指评级为 4 或更高的产品）。

分面是查询参数，不要将其与查询输入混淆。 它绝不会用作查询中的选择条件。 相反，将分面查询参数视为对在响应中返回的导航结构的输入。 对于用户提供的每个分面查询参数，Azure 搜索将评估每个分面值对应的部分结果中的记录数。

请注意步骤 4 中的 `$filter`。 这是分面导航的一个重要方面。 尽管分面和筛选器在 API 中相互独立，但你需要使用二者提供所需体验。 

### <a name="application-design-pattern"></a>应用程序设计模式

在应用程序代码中，模式旨在使用分面查询参数返回分面导航结构以及分面结果，还可使用 $filter 表达式处理单击事件。 将 `$filter` 表达式视为实际剪裁返回到表示层的搜索结果的代码隐藏。 假设分面依据为“颜色”，通过 `$filter` 表达式单击颜色“红色”，表示仅选择颜色为红色的项。 

### <a name="query-basics"></a>查询基础知识

在 Azure 搜索中，通过一个或多个查询参数指定请求（有关每个参数的描述，请参阅[搜索记录](http://msdn.microsoft.com/library/azure/dn798927.aspx)）。 所有查询参数都不是必需的，但必须至少有一个查询参数，才能使查询有效。

精度（通常表示能够筛选出不相关的匹配记录）通过以下一个或两个表达式实现：

-   **search=**  
    此参数的值构成搜索表达式。 它可能是一段单独的文本，也可能是包含多个词语或运算符的复杂搜索表达式。 在服务器上，搜索表达式用于全文搜索，在索引中查询可搜索字段获取匹配的词语，从而按排序顺序返回结果。 如果将 `search` 设置为 null，将在整个索引中执行查询（即 `search=*`）。 在此情况下，该查询的其他元素（例如 `$filter` 或计分配置文件）将是影响所返回文档 (`($filter`) 或所采用顺序（`scoringProfile` 或 `$orderby`）的主要因素。

-   **$filter=** 筛选器是一种功能强大的机制，用于根据特定记录属性的值限制搜索结果的大小。 先对 `$filter` 进行评估，然后针对生成可用值并对每个值进行相应计数的逻辑执行分面。

复杂搜索表达式会降低查询的性能。 如果可以，使用正确结构化的筛选表达式提高精度并改进查询性能。

若要更好地了解筛选器如何添加更高精度，请比较复杂搜索表达式与包括筛选表达式的搜索表达式：

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

这两个查询都有效，但如果你要查找西雅图有停车场的非汽车旅馆，则第二个查询更好。
-   第一个查询依赖于提及的特定字词或字符串字段（如名称、描述）和包含可搜索数据的任何其他字段中未提及的字词。
-   第二个查询查找对结构化数据的精确匹配项，需要的准确性可能更高。

在包括分面导航的应用程序中，你想要确保缩小通过筛选表达式获取的搜索结果范围，以便实现分面导航结果上的每个用户操作。

<a name="howtobuildit"></a>

## <a name="build-a-search-app-with-faceted-navigation"></a>生成包含分面导航的搜索应用
Azure 搜索中的分面导航在可生成请求的应用程序代码中实现，但依赖于你的架构中的预定义元素。

`Facetable [true|false]` 索引属性在你的搜索索引中预定义，并在选定字段上设置以允许或拒绝在分面导航结构中使用。 如果没有 `"Facetable" = true`，则无法在分面导航中使用字段。

代码中的表示层提供用户体验。 它应列出分面导航的组成部分，例如标签、值、复选框和计数。 Azure 搜索 REST API 与平台无关，因此可以使用所需的任何语言和平台。 请务必包括支持增量刷新的 UI 元素，以便在选择每个其他分面时更新 UI 状态。 

在查询时，你的应用程序代码将创建包含 `facet=[string]` 的请求，该字符串是提供分面依据字段的请求参数。 查询可能具有多个分面（例如 `&facet=color&facet=category&facet=rating`），每个分面采用与 (&) 字符分隔。

应用程序代码还必须构造 `$filter` 表达式，处理分面导航中的单击事件。 `$filter` 可减少搜索结果，使用分面值作为筛选条件。

Azure 搜索根据用户输入的词语返回搜索结果，以及对分面导航结构的更新。 在 Azure 搜索中，分面导航是单级别构造，包含分面值以及针对每个值找到的结果计数。

以下部分详细介绍了如何生成每个部分。

<a name="buildindex"></a>

## <a name="build-the-index"></a>生成索引
通过此索引属性，在索引中基于每个字段启用分面：`"Facetable": true`。  
可能用于分面导航的所有字段类型在默认情况下均为 `Facetable`。 此类字段类型包括 `Edm.String`、`Edm.DateTimeOffset` 和所有数值字段类型（实质上，所有字段类型都可进行分面，但 `Edm.GeographyPoint` 除外，因为它无法用于分面导航中）。 

在生成索引时，分面导航的最佳做法是针对绝不应用作分面的字段显式关闭分面功能。  具体而言，应将单独值的字符串字段（例如 ID 或产品名称）设置为 `"Facetable": false`，以避免在分面导航中意外（和无效）使用它们。

下面是 AdventureWorks 目录示例应用程序（剪裁了某些属性，以减小总体大小）的架构：

 ![][3]

请注意，对于不应用作分面的字符串字段（例如 ID 或名称），关闭 `Facetable`。 在不需要分面的情况下将其关闭有助于保持较小的索引大小，通常可提高性能。

> [!TIP]
> 作为最佳做法，包括每个字段的完整索引属性集。 尽管 `Facetable` 对于几乎所有字段默认处于打开状态，但有意设置每个属性有助于你深刻理解每个架构决策的含义。 

<a name="checkdata"></a>

## <a name="check-the-data"></a>检查数据
你的数据质量直接影响分面导航结构是否按预期具体化，以及帮助你构建可减少结果集的筛选器的有效性。

如果希望按“品牌”或“价格”进行分面，每个记录都应包含 *BrandName* 和 *ProductPrice* 值，这些值有效、一致且可用作筛选选项。

下面列出了要清理的内容的一些提醒：

* 对于分面依据的每个字段，判断它包含的值是否适合作为自定向搜索中的筛选器。 这些值应该简短、具有描述性且特点明显，以便用户在有竞争力的选项之间明确选择此值。
* 拼写错误或几乎匹配的值。 如果分面依据为“颜色”，并且字段值包括 Orange 和 Ornage（拼写错误），根据“颜色”字段的分面将选取二者。
* 混合大小写文本也可能在分面导航中造成破坏，orange 和 Orange 会显示为两个不同的值。 
* 相同值的单数和复数形式可能导致为每个值生成单独的分面。

可想而知，认真准备数据是生成有效分面导航的重要方面。

<a name="presentationlayer"></a>

## <a name="build-the-presentation-layer"></a>生成表示层
通过表示层重新处理有助于发现可能错误的要求，并了解对搜索体验至关重要的功能。

在分面导航上，你的 Web 或应用程序页面可显示分面导航结构、检测页面上的用户输入并插入更改的元素。 

对于 Web 应用程序，AJAX 通常用于表示层中，因为它允许用户刷新增量更改。 你也可以使用 ASP.NET MVC 或任何其他通过 HTTP 连接到 Azure 搜索服务的可视化平台。 本文中引用的示例应用程序 **AdventureWorks 目录**是 ASP.NET MVC 应用程序。

源自示例应用程序的 **index.cshtml** 文件的以下示例可生成动态 HTML 结构，用于在搜索结果页面中显示分面导航。 在该示例中，分面导航内置于搜索结果页面中，并在用户提交搜索词后显示。

请注意，每个分面都具有以下项：
-   一个标签（Colors、Categories、Prices）。
-   一个到分面字段的绑定（color、categoryName、listPrice）。
-   一个 `.count` 参数，用来返回为该分面结果找到的项数。

  ![][2]

> [!TIP]
> 在设计搜索结果页面时，请记住添加用于清除分面的机制。 如果使用复选框，用户可以轻松了解如何清除筛选器。 对于其他布局，可能需要痕迹导航模式或其他创新方法。 例如，在 AdventureWorks 目录示例应用程序中，你可以单击标题“AdventureWorks 目录”重置搜索页面。

<a name="buildquery"></a>

## <a name="build-the-query"></a>生成查询
编写用于生成查询的代码应指定有效查询的所有部分，包括搜索表达式、分面、筛选器、计分配置文件，所有这些内容都用于明确表述请求。 在本部分中，我们将了解分面适用于查询的部分，以及将筛选器与分面结合使用来交付减少后的结果集的方式。

通常，最好从示例着手。 源自 **CatalogSearch.cs** 文件的以下示例生成将基于“颜色”、“类别”和“价格”创建分面导航的请求。 

请注意，分面是此示例应用程序中不可或缺的部分。 AdventureWorks 目录中的搜索体验围绕分面导航和筛选器进行设计。 这是在页面中生成分面导航的重要部分。 示例应用程序包括分面的 URI 参数（颜色、类别、价格）作为“搜索”方法的属性（如示例应用程序中构造）。

  ![][4]

分面查询参数设置为字段，根据数据类型，可通过逗号分隔列表（包括 `count:<integer>`、`sort:<>`、`intervals:<integer>` 和 `values:<list>`）对其执行进一步参数化。 设置范围时，数值数据支持值列表。 有关使用情况详细信息，请参阅[搜索记录（Azure 搜索 API）](http://msdn.microsoft.com/library/azure/dn798927.aspx)。

通过分面，应用程序明确表述的请求还应生成筛选器，以便根据分面值选择缩小候选记录集。 对于自行车商店，分面导航提供了就“有哪些颜色、制造商和类型的自行车”问题的线索，同时筛选对“在此价格区间，具体有哪些红色的山地自行车”问题的答案。

当用户单击“红色”，指示应仅显示红色产品时，应用程序发送的下一条查询将为 `$filter=Color eq ‘Red’`。

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>提示和最佳做法

### <a name="indexing-tips"></a>索引编制提示
**如果不使用搜索框，请提高索引效率**

如果你的应用程序以独占方式（即无搜索框）使用分面导航，可将字段标记为 `searchable=false` 或 `facetable=true` 以生成更简洁的索引。 此外，仅在整个分面值上编制索引，不支持词内换行，也不支持对包含多个词的值的组成部分编制索引。

**指定哪些字段可用作分面**

回想一下，索引的架构确定哪些字段可用作分面。 假设某个字段可进行分面，该查询可指定分面依据的字段。 分面时依据的字段提供了显示在标签下面的值。 

显示在每个标签下面的值可从索引检索。 例如，如果分面字段为“颜色”，可用于其他筛选的值将是该字段的值（红色、黑色等）。

仅可针对 Numeric 和 DateTime 值，在分面字段上显式设置值（例如 `facet=Rating,values:1|2|3|4|5`）。 这些字段类型允许使用值列表，以简化将分面结果分入连续范围（基于数值或时段的范围）操作。 

**默认情况下，只能具有单个层次的分面导航** 

如上所述，不存在对在层次结构中嵌套分面的直接支持。 全新的分面导航仅支持一个筛选器级别。 但是，存在解决方法。 你可以针对每个层次结构使用一个入口点，在 `Collection(Edm.String)` 中对层次结构分面结构进行编码。 本文未介绍如何实现此解决方法，但你可以在 [OData 示例](http://msdn.microsoft.com/library/ff478141.aspx)中阅读有关集合的信息。 

### <a name="querying-tips"></a>查询提示
**验证字段**

如果你根据不受信任的用户输入动态生成分面列表，应验证分面字段的名称是否有效，或者在使用 .NET 中的 `Uri.EscapeDataString()` 或所选平台的等效项时对名称进行转义。

### <a name="filtering-tips"></a>筛选提示
**使用筛选器提高搜索准确性**

使用筛选器。 如果你只依赖于搜索表达式，词干分析可能导致返回的记录在其任何字段中都没有精度分面值。

**使用筛选器提高搜索性能**

筛选器缩小搜索的候选记录集，并且不对它们进行排序。 如果你有大量记录，使用选择性非常多的深化分面通常能够显著提高性能。
  
**仅筛选分面字段**

在深化分面时，你通常只希望在特定（分面）字段中，而不是在所有可搜索字段中的任意位置包括具有分面值的记录。 添加筛选器可增强目标字段，方法是将服务定向到仅在分面字段中搜索匹配的值。

**使用更多筛选器修剪分面结果**

分面结果是在匹配分面词语的搜索结果中找到的记录。 在以下示例中，在*云计算*的搜索结果中，254 个项还具有*内部规范*作为内容类型。 项不一定互相排斥。 如果某个项满足这两个筛选条件，它将分别计入每一个。 存在针对通常用于实现记录标记的 `Collection(Edm.String)` 字段进行分面的情况。

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

一般情况下，如果发现分面结果持续很大，我们建议添加更多筛选器（如前面部分所述），以便为用户提供更多缩小搜索范围的选项。

### <a name="tips-about-result-count"></a>关于结果计数的提示

**限制分面导航中的项数**

对于导航树中的每个分面字段，默认限制为 10 个值。 此默认值对导航结构有意义，因为它可使值列表保持在合理的大小。 可通过向计数分配某个值替代默认值。

* `&facet=city,count:5` 指定仅返回在排序最高的结果中找到的前 5 个城市作为分面结果。 假设搜索词“飞机场”有 32 个匹配项，如果查询指定 `&facet=city,count:5`，分面结果中仅包含前五个搜索结果中具有最多记录的唯一城市。

请注意分面结果和搜索结果之间的区别。 搜索结果是与查询匹配的所有记录。 分面结果是与每个分面值对应的匹配项。 在该示例中，搜索结果将包括未在分面分类列表中的城市名称（在我们的示例中为&5;）。 通过分面导航筛选出的结果对清理了分面或选择“城市”之外的其他分面的用户可见。 

> [!NOTE]
> 讨论 `count` 时，如果存在多个类型，会让人产生混淆。 下表简要概述了在 Azure 搜索 API、示例代码和记录中使用词语的方式。 

* `@colorFacet.count`<br/>
  在表示代码中，你应该在分面上看到一个计数参数，用于显示分面结果数。 在分面结果中，计数指示与分面词语或范围匹配的记录数。
* `&facet=City,count:12`<br/>
  在分面查询中，可将计数设置为某个值。  默认值为 10，但你可以设置更高或更低的值。 设置 `count:12` 可获取分面结果中按记录计数排序的前 12 个匹配项。
* "`@odata.count`"<br/>
  在查询响应中，该值指示搜索结果中匹配项的数目。 一般情况下，该值大于所有合并的分面结果总和，因为存在匹配搜索词但没有分面值匹配结果的项。

**获取分面结果中的计数**

当向分面查询添加筛选器时，你可能希望保留分面语句（例如 `facet=Rating&$filter=Rating ge 4`）。 从技术上讲，不需要分面“分级”，但保留它会返回分级 4 和更高分级的分面值计数。 例如，如果用户单击“4”并且查询包括大于或等于“4”的筛选器，将针对每个等于和大于 4 的分级返回计数。  

**确保获取准确的分面计数**

在某些情况下，你可能发现分面计数与结果集不匹配（请参阅 [Azure 搜索中的分面导航（论坛帖子）](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)）。

由于分片体系结构，分面计数可能不准确。 每个搜索索引具有多个分片，每个分片报告按记录计数排序的前 N 个分面，然后合并到单个结果中。 如果某些分片具有大量匹配值，而其他分配的值很少，你可能会发现某些分面值丢失或未计入结果中。

尽管此行为可能随时更改，但如果你现在遇到此行为，可通过以下方式解决它：人为地将 count:<number> 扩大到非常大的值，以强制从每个分片进行完整报告。 如果 count: 的值大于或等于字段中唯一值的数目，可保证获得准确结果。 但是，如果记录计数非常高，性能可能会受到负面影响，因此请谨慎使用此选项。

### <a name="user-interface-tips"></a>用户界面提示
**为分面导航中的每个字段添加标签**

标签通常在 HTML 或窗体（在示例应用程序中为 **index.cshtml**）中定义。 Azure 搜索中没有用于分面导航标签或任何其他元数据类型的 API。

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range-of-values"></a>基于值范围进行筛选
基于范围分面是常见的搜索应用程序要求。 数值数据和 DateTime 值都支持范围。 可在[搜索记录（Azure 搜索 API）](http://msdn.microsoft.com/library/azure/dn798927.aspx)中阅读有关每种方法的详细信息。

Azure 搜索通过提供两种用于计算范围的方法，简化范围构造。 对于这两种方法，Azure 搜索根据用户提供的输入创建适当的范围。 例如，如果用户指定范围值为 10|20|30，它将自动创建范围 0-10、10-20、20-30。 示例应用程序删除任何空的间隔。 

**方法 1：使用间隔参数**  
若要以&10; 美元的增量设置价格分面，将指定：`&facet=price,interval:10`

**方法 2：使用值列表**  
对于数值数据，可以使用值列表。  请考虑 listPrice 的分面范围，如下所示：

  ![][5]

使用值列表在 **CatalogSearch.cs** 文件中指定范围：

    facet=listPrice,values:10|25|100|500|1000|2500

通过以下方式生成每个范围：使用 0 作为起点、使用列表中的某个值作为终结点，然后剪裁上一个范围以创建离散间隔。 Azure 搜索将执行此操作作为分面导航的一部分。 无需编写用于结构化每个间隔的代码。

### <a name="build-a-filter-for-a-range"></a>针对范围生成筛选器
若要根据用户所选的范围筛选记录，你可以在包含两个部分的表达式中使用 `"ge"` 和 `"lt"` 筛选器运算符，该表达式可定义范围的终结点。 例如，如果用户选择范围 10-25，筛选器将为 `$filter=listPrice ge 10 and listPrice lt 25`。

在示例应用程序中，筛选器表达式使用 **priceFrom** 和 **priceTo** 参数设置终结点。 **CatalogSearch.cs** 中的 **BuildFilter** 方法包含筛选器表达式，可向你提供范围内的记录。

  ![][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>基于距离进行筛选
常见的筛选器用于根据你所在的当前位置帮助你选择附近的商店、餐馆或目的地。 虽然此类型的筛选器看起来像分面导航，但它实际上只是筛选器。 我们在此处针对专门查找该特定设计问题的实现建议的用户进行介绍。

Azure 搜索中有两个地理空间函数：**geo.distance** 和 **geo.intersects**。

* **geo.distance** 函数返回两点之间的距离（以千米为单位），一个点是字段，另一个点是作为筛选器一部分传递的常量。 
* 如果给定的点位于给定的多边形范围内（其中该点是字段，多边形指定为作为筛选器一部分传递的坐标常量列表），**geo.intersects** 函数将返回 true。

你可以在 [OData 表达式语法（Azure 搜索）](http://msdn.microsoft.com/library/azure/dn798921.aspx)中查找筛选器示例。

<a name="tryitout"></a>

## <a name="try-the-demo"></a>尝试演示
Azure Search Adventure Works 演示包含本文中引用的示例。 当你处理搜索结果时，观看“查询构造中的更改”的 URL。 此应用程序在你选择每个分面时，将分面追加到 URI。

1. 将示例应用程序配置为使用服务 URL 和 API 密钥。 
   
   请注意在 CatalogIndexer 项目的 Program.cs 文件中定义的架构。 它指定 color、listPrice、size、weight、categoryName 和 modelName 等可分面的字段。  实际上，仅其中几个字段（color、listPrice、categoryName）在分面导航中实现。
2. 运行应用程序。 
   
   首先，仅搜索框可见。 你可以立即单击“搜索”按钮获取所有结果，或键入搜索词。
   
   ![][7]
3. 输入搜索词（例如自行车），然后单击“搜索”。 快速执行查询。
   
   分面导航结构也与搜索结果一起返回。  在 URL 中，“颜色”、“类别”和“价格”的分面均为 null。 在搜索结果页面中，分面导航结构包括每个分面结果的计数。
   
   ![][8]
4. 单击颜色、类别和价格范围。 分面在初始搜索时为 null，但当对它们设置值时，将从搜索结果中剪裁掉不再匹配的项。 请注意，URI 选取对你的查询的更改。
   
   ![][9]
5. 若要清除分面查询，以便你可以尝试不同的查询行为，请单击页面顶部的“AdventureWorks 目录”。
   
   ![][10]

6.  若要测试你的知识，可为 *modelName* 添加分面字段。 已为此分面设置索引，因此无需对该索引进行任何更改。 但是需要修改 HTML 以包括模型的新分面，并将分面字段添加到查询构造函数。

<a name="nextstep"></a>

## <a name="learn-more"></a>了解详细信息
观看 [Azure 搜索深入研究](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410)。 在 45:25，演示了如何实现分面。

有关分面导航设计准则的更多见解，建议查看以下链接：

* [针对分面搜索进行设计](http://www.uie.com/articles/faceted_search/)
* [设计模式：分面导航](http://alistapart.com/article/design-patterns-faceted-navigation)


<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx



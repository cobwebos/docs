---
title: "在门户中构建第一个 Azure 搜索索引 | Microsoft 文档"
description: "在 Azure 门户中使用预定义的示例数据生成索引。 探索全文搜索、筛选器、分面 (Facet)、模糊搜索、地域搜索等功能。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 02/22/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 3f608b8ae7a7812b939e0d9ab1cb8e19853220fd
ms.openlocfilehash: 6ed23693c95ce9025576d59fa6df9d640950ada3
ms.lasthandoff: 02/24/2017


---
# <a name="build-and-query-your-first-azure-search-index-in-the-portal"></a>在门户中生成和查询第一个 Azure 搜索索引

在 Azure 门户中，通过“导入数据”向导开始使用预定义的示例数据集快速生成索引。 使用“搜索浏览器”探索全文搜索、筛选器、分面、模糊搜索和地域搜索。  

这篇不包含代码的简介可帮助用户开始使用预定义的数据，使他们能够立即编写有趣的查询。 尽管门户工具不能取代代码，但可以帮助完成以下任务：

+ 在没有丰富专业知识的情况下展开实践和学习
+ 在“导入数据”中编写代码之前制作索引原型
+ 在“搜索浏览器”中测试查询和分析器语法
+ 查看已发布到服务的现有索引并查找其属性

**估计所需时间：**大约 15 分钟，但如果还需要注册帐户或服务，则所需时间会更长。 

此外，也可以花费大约 3 分钟时间观看这部 [Azure 搜索概述视频](https://channel9.msdn.com/Events/Connect/2016/138)，其中对本教程所述的步骤做了 6 分钟的演示。

## <a name="prerequisites"></a>先决条件

本教程假设读者拥有 [Azure 订阅](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)和 [Azure 搜索服务](search-create-service-portal.md)。 

## <a name="find-your-service"></a>找到服务
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开 Azure 搜索服务的服务仪表板。 如果未将服务磁贴固定到仪表板，可通过以下方式找到你的服务： 
   
   * 在跳转栏中，单击左侧导航窗格底部的“更多服务”。
   * 在搜索框中，键入“搜索”获取订阅的搜索服务列表。 你的服务应会出现在该列表中。 

## <a name="check-for-space"></a>检查空间
很多客户开始使用免费服务。 此版本限制为三个索引、三个数据源和三个索引器。 在开始之前，请确保有空间存储额外的项目。 本教程将创建每个对象的一个实例。 

> [!TIP] 
> 服务仪表板中的磁贴将你已有多少个索引、索引器和数据源。 “索引器”磁贴显示成功和失败指示器。 单击该磁贴可查看索引器计数。 
>
> ![索引器和数据源的磁贴][1]
>

## <a name="a-namecreate-indexa-create-an-index-and-load-data"></a><a name="create-index"></a>创建索引并加载数据
搜索查询将循环访问 *索引* ，索引中包含可搜索数据、元数据，以及用于优化某些搜索行为的构造。

为了在门户中完成此任务，我们将使用可通过“导入数据”向导利用索引器进行爬网的内置示例数据集。 

#### <a name="step-1-start-the-import-data-wizard"></a>步骤 1：启动“导入数据”向导
1. 在 Azure 搜索服务仪表板上，单击命令栏中的“导入数据”  ，以启动用于创建和填充索引的向导。
   
    ![导入数据命令][2]

2. 在向导中，单击“数据源” > “示例” > “realestate-us-sample”。 此数据源中已预先配置名称、类型和连接信息。 创建后，它将成为可在其他导入操作中重复使用的“现有数据源”。

    ![选择示例数据集][9]

3. 单击“确定”使用该数据集。

#### <a name="step-2-define-the-index"></a>步骤 2：定义索引
创建索引通常是基于代码的手动过程，但向导可以针对它能够爬网的所有数据源生成索引。 索引至少需要使用一个名称和一个字段集合（其中一个字段标记为文档键）来唯一标识每个文档。

字段包含数据类型和属性。 顶部的复选框为*索引属性*，用于控制如何使用字段。 

*  意味着该字段将显示在搜索结果列表中。 清除此复选框即可将单个字段标记为关闭搜索结果限制，例如当字段仅用于筛选器表达式时。 
* “可筛选”、“可排序”和“可查找”确定字段是否可用于筛选器、排序或方面导航结构。 
*  意味着该字段将包括在全文搜索中。 字符串可搜索。 数值字段和布尔字段通常标记为不可搜索。 

默认情况下，向导将在数据源中扫描用作键字段基础的唯一标识符。 字符串经过属性化，可检索且可搜索。 整数经过属性化，可检索、可筛选、可排序且可分面。

  ![生成的 realestate 索引][3]

单击“确定”创建该索引。

#### <a name="step-3-define-the-indexer"></a>步骤 3：定义索引器
仍在“导入数据”向导中，单击“索引器” > “名称”，然后键入索引器的名称。 

此对象定义一个可执行过程。 可将该对象放入定期计划，但我们暂时单击“确定”，使用默认选项立即运行索引器一次。  

  ![realestate 索引器][8]

## <a name="check-progress"></a>检查进度
若要监视数据导入，请返回服务仪表板并向下滚动，然后双击“索引器”磁贴打开索引器列表。 列表中应会出现新建的索引器，其状态指示“正在进行”或“成功”，此外还会列出已编制索引的文档数。

   ![索引器进度消息][4]

## <a name="a-namequery-indexa-query-the-index"></a><a name="query-index"></a>查询索引
现在已有一个可以查询的搜索索引。 **搜索浏览器** 是内置于门户中的查询工具。 它提供一个搜索框，用于验证搜索结果是否符合预期。 

> [!TIP]
> 在 [Azure 搜索概述视频](https://channel9.msdn.com/Events/Connect/2016/138)中，从第 6 分 8 秒开始的片段演示了以下步骤。
>

1. 单击命令栏上的“搜索浏览器”  。

   ![搜索浏览器命令][5]

2. 在命令栏中单击“更改索引”切换到 *realestate-us-sample*。

   ![索引和 API 命令][6]

3. 在命令栏中单击“设置 API 版本”，查看有哪些 REST API 可用。 使用预览 API 可以访问尚未正式发布的新功能。 对于以下查询，除非有明确的指示，否则请使用正式版 (2016-09-01)。 

    > [!NOTE]
    > [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 与 [.NET 库](search-howto-dotnet-sdk.md#core-scenarios)完全相同，但**搜索浏览器**只适合处理 REST 调用。 搜索浏览器接受[简单查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)和[完整 Lucene 查询分析器](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)的语法，加上可在[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)操作中使用的所有搜索参数。
    > 

4. 在搜索栏中输入以下查询字符串，然后单击“搜索”。

  ![搜索查询示例][7]

**`search=seattle`**

+ `search` 参数用于输入关键字来执行全文搜索，在本例中，将返回华盛顿州金县的结果列表，其中包含文档中任何可搜索字段内的 *Seattle*。 

+ **搜索浏览器**以 JSON 格式返回结果，如果文档采用密集结构，这种结果将很冗长且难以阅读。 根据具体的文档，可能需要编写用于处理搜索结果的代码来提取重要元素。 

+ 文档由标记为可在索引中检索的所有字段构成。 若要在门户中查看索引属性，请在“索引”磁贴中单击“realestate-us-sample”。

**`search=seattle&$count=true&$top=100`**

+ `&` 符号用于追加可以按任意顺序指定的搜索参数。 

+  `$count=true` 参数返回所有已返回文档的总计数。 可以通过监视 `$count=true` 报告的更改来验证筛选器查询。 

+ `$top=100` 返回所有文档中排名最高的 100 个文档。 默认情况下，Azure 搜索返回前 50 个最佳匹配项。 可以通过 `$top` 增加或减少返回的结果。

**`search=*&facet=city&$top=2`**

+ `search=*` 是空搜索。 空搜索会搜索所有内容。 提交空查询的原因之一是针对整个文档集进行筛选器或分面。 例如，你希望某个分面导航结构由索引中的所有城市组成。

+  `facet` 返回可传递给 UI 控件的导航结构。 它将返回类别和计数。 在本例中，类别基于城市数目。 Azure 搜索中没有聚合，但你可以通过 `facet` 进行近似聚合，提供每个类别中的文档计数。

+ `$top=2` 返回两个文档，演示如何使用 `top` 来减少或增加结果。

**`search=seattle&facet=beds`**

+ 此查询针对 *Seattle* 执行文本搜索后返回的床位分面。 可将 `"beds"` 指定为分面，因为该字段已标记为可在索引中检索、筛选和分面，并且它包含的值（数字 1 到 5）适合用于将列表分类为组（包含 3 间卧室和 4 间卧室的房屋列表）。 

+ 只有可筛选的字段才可分面。 结果中只返回仅可检索的字段。

**`search=seattle&$filter=beds gt 3`**

+ `filter` 参数返回与提供的条件匹配的结果。 在本例中，条件为卧室数大于 3。 

+ 筛选器语法是一种 OData 构造。 有关详细信息，请参阅 [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)（筛选器 OData 语法）。

**`search=granite countertops&highlight=description`**

+ 搜索词突出显示是对与关键字匹配的文本设置的格式，表示在特定字段中找到的匹配项。 如果搜索词深藏在说明中，可以添加搜索词突出显示来方便找到这些词。 在本例中，使用带有格式的短语 `"granite countertops"` 可以更方便地在说明字段中看到该短语。

**`search=mice&highlight=description`**

+ 全文搜索将查找具有类似语义的单词形式。 在本例中，搜索结果包含搜索“mice”关键字后，包含“mouse”的突出显示文本（针对老鼠泛滥的家庭执行的搜索）。 由于执行了语言分析，同一单词的不同形式可能会显示在结果中。 

+ Azure 搜索支持 Lucene 和 Microsoft 提供的 56 种分析器。 Azure 搜索使用的默认分析器是标准的 Lucene 分析器。 

**`search=samamish`**

+ 执行典型的搜索时，如果拼错单词（例如，将西雅图地区的 Samammish 高原拼写为“samamish”），则无法返回匹配项。 若要处理拼写错误，可以使用下一个示例所述的模糊搜索。

**`search=samamish~&queryType=full`**

+ 指定 `~` 符号并使用完整查询分析器时，将启用模糊搜索，它会解释并正确分析 `~` 语法。 

+ 选择启用完整查询分析器（设置 `queryType=full`）后，可以使用模糊搜索。 有关完整查询分析器支持的查询方案的详细信息，请参阅 [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)（Azure 搜索中的 Lucene 查询语法）。

+ 如果未指定 `queryType`，将使用默认的简单查询分析器。 简单查询分析器速度更快，但如果需要执行模糊搜索、正则表达式、近似搜索或其他高级查询类型，则需要使用完整语法。 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ 对于包含坐标的字段，支持通过 [edm.GeographyPoint 数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)执行地理空间搜索。 地域搜索是 [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)（筛选器 OData 语法）中指定的一种筛选器类型。 

+ 该示例查询将筛选位置数据的所有结果，这些结果与给定地点（以纬度和经度坐标的形式指定）之间的距离小于 5 公里。 通过添加 `$count`，可以查看在更改距离或坐标时会返回多少个结果。 

+ 如果搜索应用程序具有“附近查找”功能或使用地图导航，地理空间搜索非常有用。 但它不属于全文搜索。 如果用户要求是按名称搜索城市或国家/地区，请添加包含该城市或国家/地区的名称的字段，并添加坐标。

## <a name="next-steps"></a>后续步骤

+ 修改刚刚创建的任一对象。 运行一次该向导后，可以返回查看或修改单个组件：索引、索引器或数据源。 不允许对索引进行某些编辑操作（如更改字段数据类型），但大多数属性和设置是可修改的。

  若要查看单个组件，请单击仪表板上的“索引”、“索引器”或“数据源”磁贴，显示现有对象的列表。 若要详细了解如何在不重新生成的情况下进行索引编辑，请参阅 [Update Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/update-index)（更新索引（Azure 搜索 REST API）。

+ 尝试针对其他数据源使用上述工具和步骤。 示例数据集 `realestate-us-sample` 摘自 Azure 搜索可爬网的 Azure SQL 数据库。 除了 Azure SQL 数据库以外，Azure 搜索还可爬网 Azure VM 和 DocumentDB 上的 Azure 表存储、Blob 存储、SQL Server 中的平面数据结构中的索引并对其进行推断。 向导支持所有这些数据源。 在代码中，可以使用*索引器*轻松填充索引。

+ 可通过推送模型支持其他所有非索引器数据源，代码会将 JSON 格式的新行集与更改的行集推送到索引。 有关详细信息，请参阅 [Add, update, or delete documents in Azure Search](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)（在 Azure 搜索中添加、更新或删除文档）。

若要详细了解本文中所述的其他功能，请访问以下链接：

* [索引器概述](search-indexer-overview.md)
* [创建索引（包括索引属性的详细说明）](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [搜索浏览器](search-explorer.md)
* [搜索文档（包括查询语法的示例）](https://docs.microsoft.com/rest/api/searchservice/search-documents)


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

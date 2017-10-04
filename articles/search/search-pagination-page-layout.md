---
title: "如何在 Azure 搜索中对搜索结果分页 | Microsoft Docs"
description: "Azure 搜索中的分页，这是 Microsoft Azure 上的一项托管云搜索服务。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: a0a1d315-8624-4cdf-b38e-ba12569c6fcc
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: heidist
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1054e15a2751c53aad5dbc8054c4cec41102dee9
ms.contentlocale: zh-cn
ms.lasthandoff: 12/08/2016

---
# <a name="how-to-page-search-results-in-azure-search"></a>如何在 Azure 搜索中对搜索结果分页
本文提供有关如何使用 Azure 搜索服务 REST API 实现搜索结果页面的标准元素（例如总计数、记录检索、排序顺序和导航）的指南。

在下面所述的每种情况下，通过发送给 Azure 搜索服务的[搜索记录](http://msdn.microsoft.com/library/azure/dn798927.aspx)请求指定与页面相关的选项，用于将数据或信息提供到你的搜索结果页面。 请求包括 GET 命令、路径和查询参数，用于通知服务正在请求的内容以及如何明确表述响应。

> [!NOTE]
> 有效的请求包括大量元素，例如服务 URL 和路径、HTTP 谓词、`api-version` 等。 为简洁起见，我们剪裁了示例，以便仅突出显示与分页相关的语法。 有关请求语法的详细信息，请参阅 [Azure 搜索服务 REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx) 文档。
> 
> 

## <a name="total-hits-and-page-counts"></a>总匹配记录和页面计数
几乎所有搜索页面都以显示从查询返回的结果总数，然后以较小的区块返回这些结果为基础。

![][1]

在 Azure 搜索中，使用 `$count`、`$top` 和 `$skip` 参数返回这些值。 以下示例显示总匹配记录的示例请求，作为 `@OData.count` 返回：

        GET /indexes/onlineCatalog/docs?$count=true

检索 15 个组中的记录，同时显示总匹配记录，从第一页开始：

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

分页结果需要 `$top` 和 `$skip`，其中 `$top` 指定批量返回的项数，`$skip` 指定要跳过的项数。 在以下示例中，每个页面显示接下来的 15 个项，由 `$skip` 中的增量跳转指示。

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>布局
在搜索结果页中，你可能想要显示缩略图、字段子集和完整产品页面的链接。

 ![][2]

在 Azure 搜索中，使用 `$select` 和查找命令实现这种体验。

若要返回平铺布局的字段的子集：

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

图像和媒体文件不可直接搜索，它们应存储在其他存储平台（Azure Blob 存储）中以减少成本。 在索引和记录中，定义存储外部内容的 URL 地址的字段。 然后，你可以将该字段用作图像引用。 图像的 URL 应在记录中。

若要检索 **onClick** 事件的产品说明页，请使用 [查找记录](http://msdn.microsoft.com/library/azure/dn798929.aspx) 传递要检索的记录的密钥。 密钥的数据类型为 `Edm.String`。 在此示例中，它是 *246810*。 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>按相关性、分级或价格排序
默认情况下通常按相关性排序顺序，但通常也可以使用现成的备用排序顺序，以便客户可以将现有结果重组到其他排序顺序中。

 ![][3]

在 Azure 搜索中，对于所有编制索引为 `"Sortable": true.` 的字段，根据 `$orderby` 表达式进行排序

相关性与计分配置文件密切关联。 你可以使用依赖于文本分析和统计信息对所有结果排序顺序的默认计分，针对某个搜索词，分数越高，相应记录的匹配项就越多且越相关。

备用排序顺序通常与 **onClick** 事件关联，这些事件可回调生成排序顺序的方法。 例如，以此页面元素为例：

 ![][4]

你将创建一个接受选定排序选项作为输入并返回与该选项关联的条件的排序列表的方法。

 ![][5]

> [!NOTE]
> 虽然默认计分足以处理许多方案，但我们建议使用基于相关性的自定义计分配置文件。 通过自定义计分配置文件，你可以增强对你的业务更有益的项。 有关详细信息，请参阅[添加计分配置文件](http://msdn.microsoft.com/library/azure/dn798928.aspx)。 
> 
> 

## <a name="faceted-navigation"></a>多面导航
搜索导航是搜索页面上的常见功能，通常位于页面侧边或顶部。 在 Azure 搜索中，分面导航基于预定义筛选器提供自定向搜索。 有关详细信息，请参阅 [Azure 搜索中的分面导航](search-faceted-navigation.md)。

## <a name="filters-at-the-page-level"></a>页面级别的筛选器
如果你的解决方案设计包含了专用于特定内容类型（例如页面顶部列出了部门的在线零售应用程序）的搜索页面，你可以在 **onClick** 事件旁边插入筛选器表达式，以在预筛选状态下打开页面。 

你可以发送带有或不带有搜索表达式的筛选器。 例如，将按品牌名称筛选以下请求，以便仅返回与之匹配的记录。

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

有关 `$filter` 表达式的详细信息，请参阅[搜索记录（Azure 搜索 API）](http://msdn.microsoft.com/library/azure/dn798927.aspx)。

## <a name="see-also"></a>另请参阅
* [Azure 搜索服务 REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx)
* [索引操作](http://msdn.microsoft.com/library/azure/dn798918.aspx)
* [文档操作](http://msdn.microsoft.com/library/azure/dn800962.aspx)
* [有关 Azure 搜索的视频和教程](search-video-demo-tutorial-list.md)
* [Azure 搜索中的分面导航](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 


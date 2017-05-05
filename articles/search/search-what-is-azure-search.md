---
title: "什么是 Azure 搜索 | Microsoft Docs"
description: "Azure 搜索是完全托管的托管云搜索服务。 通过此功能概述，了解详细信息。"
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/24/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 3720aed95706f6e8dd9a28085a6de0bc1f756a9b
ms.lasthandoff: 04/26/2017

---
# <a name="what-is-azure-search"></a>什么是 Azure 搜索？
Azure 搜索是一种搜索即服务云解决方案，它将服务器和基础结构的管理委托给 Microsoft，为用户提供随时可用的服务，用户可在填充数据后使用此服务将搜索添加到 Web 或移动应用程序。 借助 Azure 搜索，可以使用简单的 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 或 [.NET SDK](search-howto-dotnet-sdk.md) 在应用程序中添加稳定的搜索体验，无需管理搜索基础结构或精通搜索技术。

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>在应用或站点中嵌入功能强大的搜索体验


### <a name="full-text-search-and-text-analysis"></a>全文搜索和文本分析

可以使用[简单的查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)来表述查询，该查询语法提供了逻辑运算符、短语搜索运算符、后缀运算符、优先级运算符。 此外，[Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)可以启用模糊搜索、近似搜索、术语提升和正则表达式。 Azure 搜索还支持[自定义的词汇分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)，以允许应用程序使用拼音匹配和正则表达式处理复杂的搜索查询。

### <a name="language-support"></a>语言支持

Azure 搜索支持适用于 [56 种不同语言](https://docs.microsoft.com/rest/api/searchservice/language-support)的词汇分析器。 使用 Lucene 分析器和 Microsoft 分析器（Office 和必应中的自然语言处理已经过多年优化），Azure 搜索可以在应用程序的搜索框中分析文本，从而以智能方式处理特定于语言的语言学，包括谓词时态、词性、不规则复数名词（例如“mouse”与“mice”）、词取消复合、词拆分（对于不带空格的语言）等。

### <a name="data-integration"></a>数据集成

你可以推送 JSON 数据结构来填充 Azure 搜索索引。 另外，对于受支持的数据源，你可以使用[索引器](search-indexer-overview.md)自动爬网式搜索 Azure SQL 数据库、Azure DocumentDB 或 [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)，以便将搜索索引的内容与主要数据存储同步。

**文档破解**实现了[主要文件格式的索引编制](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office、PDF 和 HTML 文档。

### <a name="search-experience"></a>搜索体验

+ 可针对自动完成搜索栏和提前键入查询启用**搜索建议**。 当用户输入部分搜索输入内容时，[将显示索引中实际文档的建议](https://docs.microsoft.com/rest/api/searchservice/suggesters)。

+ 通过[仅仅单个查询参数](https://docs.microsoft.com/azure/search/search-faceted-navigation)实现了**分面导航**。 Azure 搜索返回一个分面导航结构，你可以将该结构用作类别列表背后的代码，用于自定向筛选（例如，按价格范围或品牌来筛选目录项）。

+ 可以使用**筛选器**将分面导航纳入到应用程序的 UI 中，改进查询表述，以及基于用户或开发人员指定的条件进行筛选。 可以使用 [OData 语法](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)创建筛选器。

+ **命中项突出显示**[向搜索结果中的匹配关键字应用可视化格式设置](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 可以选择哪些字段返回突出显示的片段。

+ **简单计分**是 Azure 搜索的主要优点。 [计分配置文件](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)用于在文档中自行将相关性建模为值的函数。 例如，你可能希望较新产品或打折产品显示在搜索结果的顶部位置。 也可以基于已跟踪和单独存储的客户搜索首选项将标记用于个性化计分，来生成计分配置文件。

+ **排序**通过索引架构提供给多个字段，然后使用单个搜索参数在查询时切换。

+ [通过 Azure 搜索所提供的对搜索结果的优化控制](search-pagination-page-layout.md)，**分页**和限制搜索结果将变得更简单。  

### <a name="geosearch"></a>地理搜索

Azure 搜索可以智能地处理、筛选和显示地理位置。 借助 Azure 搜索，用户可以基于搜索结果对指定位置的临近程度浏览数据，或者基于特定的地理区域浏览数据。 本视频介绍其工作原理：[第 9 频道：Azure 搜索和地理空间数据](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)。

### <a name="cloud-service-advantages"></a>云服务的优点

+ **高可用性**可确保极其可靠的搜索服务体验。 正确缩放时，[Azure 搜索可提供 99.9% SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

+ 对于作为端到端解决方案的**完全托管**，Azure 搜索绝对不需要基础结构管理。 通过在两个维度进行缩放以便处理更多文档存储和/或更高的查询负载，可以根据你的需求来定制服务。

### <a name="monitoring-and-reporting"></a>监视和报告

+ [可以收集并分析](search-traffic-analytics.md)**搜索流量分析**，以便根据用户键入到搜索框的内容来解锁见解。

+ 门户页面中会捕获并报告关于每秒查询数、延迟和限制的指标，无需额外进行配置。 你还可以轻松监视索引和文档计数，以便可以根据需要调整容量。 

### <a name="tools-for-prototyping-and-inspection"></a>用于原型制作和检查的工具

在门户中，可以使用**导入数据**向导来配置索引器、索引设计器以建立索引，并可以使用**搜索浏览器**对直接来自你帐户的 Azure 门户的所有索引发出查询，从而测试查询并优化计分配置文件。 还可以打开任何索引来查看其架构。

## <a name="how-it-works"></a>工作原理
### <a name="step-1-provision-service"></a>步骤 1：预配服务
可使用 [Azure 门户](https://portal.azure.com/)或 [Azure 资源管理 API](https://msdn.microsoft.com/library/azure/dn832684.aspx) 调整 Azure 搜索服务。

根据搜索服务的配置方式，将使用与其他 Azure 搜索订阅者共享的服务免费层，或仅由你的服务使用的专用资源[付费层](https://azure.microsoft.com/pricing/details/search/)。 预配服务时，还需要选择托管服务的数据中心区域。

根据所选服务的层，可在以下两个方面缩放服务：1) 添加副本以便增长容量来处理大量查询负载，以及 2) 添加分区以便为更多文档添加存储。 通过单独处理文档存储和查询吞吐量，可以根据你自己的特定需求来自定义搜索服务。

### <a name="step-2-create-index"></a>步骤 2：创建索引
将内容上载到 Azure 搜索服务之前，必须先定义 Azure 搜索索引。 索引类似于用于保存数据的数据库表，可接受搜索查询。 定义索引架构以便映射到要搜索的文档结构，这类似于数据库中的字段。

这些索引的架构可在 Azure 门户中创建，也可以[使用 .NET SDK](search-howto-dotnet-sdk.md) 或 [REST API](https://msdn.microsoft.com/library/azure/dn798941.aspx) 以编程方式进行创建。 定义索引后，可以将数据上载到 Azure 搜索服务以便随后编制索引。

### <a name="step-3-index-data"></a>步骤 3：索引数据
在定义索引的字段和属性后，可随时将内容上载到索引中。 可使用推模型或拉模型将数据上载到索引。

通过可按需或为计划更新配置的索引器提供拉模型（请参阅[索引器操作（Azure 搜索服务 REST API）](https://msdn.microsoft.com/library/azure/dn946891.aspx)），以便你可以轻松从 Azure DocumentDB、Azure SQL 数据库、Azure Blob 存储或托管在 Azure VM 中的 SQL Server 引入数据和数据更改。

推模型通过 SDK 或 REST API 进行提供，用于将更新的文档发送到索引。 可以从使用 JSON 格式的几乎任何数据集推送数据。 有关加载数据的指南，请参阅[添加、更新或删除文档](https://msdn.microsoft.com/library/azure/dn798930.aspx)或[如何使用.NET SDK）](search-howto-dotnet-sdk.md)。

### <a name="step-4-search"></a>步骤 4：搜索
在填充 Azure 搜索索引后，现在可通过将简单的 HTTP 请求与 REST API 或 .NET SDK 结合使用，向服务终结点[发出搜索查询](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

虽然可以在门户中执行许多任务，但 Azure 搜索是为希望将搜索功能集成到现有应用程序中的开发人员打造的。 可以使用以下编程接口。

|平台 |说明 |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | 任何编程平台和语言（包括 Xamarin、Java 和 JavaScript）支持的 HTTP 命令|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API 的 .NET 包装器以 C# 和其他针对 .NET Framework 的托管代码语言提供了有效编码。 |

## <a name="free-trial"></a>免费试用
Azure 订户可以[在免费层中预配服务](search-create-service-portal.md)。

如果不是订户，可以[免费建立一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)：获取可用来试用付费版 Azure 服务的信用额度，甚至在用完信用额度后，仍可以保留帐户和使用免费的 Azure 服务（如网站）。 你的信用卡将永远不会付费，除非你显式更改设置并要求付费。

也可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)：MSDN 订阅每月为你提供可用于试用付费版 Azure 服务的信用额度。 

## <a name="watch-a-short-video"></a>观看短视频

搜索引擎是在移动应用中、网站上和公司数据存储中检索信息时常用的驱动程序。 Azure 搜索提供了用于打造与大型商业网站上的搜索体验类似的搜索体验的工具。 这段时长为 9 分钟的视频出自项目经理 Liam Cavanagh 之手，其中介绍了集成一个搜索引擎如何会使你的应用受益、Azure 搜索中的主要功能，以及典型的工作流是怎样的。 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 分钟介绍了主要功能和用例。
+ 3-4 分钟介绍了服务预配。 
+ 4-6 分钟介绍了用来使用内置的房地产业数据集创建索引的“导入数据”向导。
+ 6-9 分钟介绍了搜索浏览器和各种查询。




---
title: "什么是 Azure 搜索 | Microsoft 文档"
description: "具有高级搜索功能的搜索引擎，可从自定义网站、应用以及公司数据和文件存储中的搜索栏中使用。"
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: heidist
experiment_id: heidist-20170221
translationtype: Human Translation
ms.sourcegitcommit: 71694028366f48682881523c0220a4158078a76c
ms.openlocfilehash: b851f906b57225dce516d9704fdace80c44c4897
ms.lasthandoff: 02/23/2017

---
# <a name="what-is-azure-search"></a>什么是 Azure 搜索？

Azure 搜索提供了一个具有智能搜索行为的专用可编程搜索引擎，它为你的网站、应用以及公司文件或数据存储中的内容搜索栏提供支持。 

![由 Azure 搜索提供支持的搜索栏](./media/search-what-is-azure-search/search-powered-by-azsearch3.png)

可靠的搜索体验是大多数 Web 和移动应用所必备的。 能够基于语义上完全相同但表面上看略有不同的输入（例如“car”和“auto”）执行查询词自动完成、拼写更正以及匹配的全文搜索几乎是最小的栏，无论为体验提供支持的技术有多复杂。 对缩放、可靠性以及搜索与后端数据存储之间的同步的操作要求同样重要。 

Azure 搜索提供了全面的功能，因此你可以同时满足搜索和操作要求。

![具有典型的搜索功能的搜索栏和自定义搜索页](./media/search-what-is-azure-search/search-page-callouts3.png)

## <a name="how-it-works"></a>工作原理

若要使用 Azure 搜索，请在你的 Azure 订阅中预配一项免费或付费服务，创建并加载包含你的可搜索内容的索引，然后调用 API 来发出搜索请求并处理结果。 如果你需要大规模的专用资源，则必须使用付费服务。

|层 |说明 |
|-----|------------|
|[免费](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) | 一项可以快速预配的共享服务，可用于小型工作负荷（学习和评估方案中的典型工作负荷）。 |
|[可计费](search-sku-tier.md) | 采用分层次容量级别（从基本到标准高密度）的专用服务；在不同的定价层提供广泛的部署配置。|

Azure 搜索在云中作为 Microsoft 提供的托管服务运行。 它可以与任何应用程序平台上的自定义代码相集成。 你的全托管服务和专用内容全局可用、可编程、可伸缩且可恢复。 

专用服务采用由 Microsoft Azure 提供支持的 [99.9% 服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)以 24-7 的方式运行。

## <a name="how-it-compares"></a>它如何进行比较

多个云服务提供商提供了自定义搜索引擎，该引擎包含为自定义应用中的搜索栏提供支持的功能。 某些引擎提供了可比较的基线功能，以及全文搜索、地理搜索，并且能够处理搜索输入中一定程度的模糊性。 通常，它是一项[专用功能](#feature-drilldown)，或者是 API、工具以及用于确定最匹配项的管理功能的易化和总体简化。

与其他搜索解决方案相比，Azure 搜索为主要依赖于信息检索搜索和内容导航的应用提供了必要的一层附加功能，因此在处理 Azure 上的内容存储和数据库的全文搜索工作负荷方面，它最为强大。 

+ 在索引层的 Azure 数据集成（爬网程序）
+ 用于集中管理的 Azure 门户
+ Azure 可伸缩性、可靠性和世界一流的可用性
+ 语言分析和自定义分析，提供分析器，用于支持以 56 种语言进行可靠的全文搜索
+ 对以搜索为中心的应用通用的核心功能，包括相关性、分面、建议、同义词、地理搜索和更多功能（在下文中列出）。

> [!Note]
> 完全支持非 Azure 数据源。 可以通过管道将任何 JSON 文档集合传输到 Azure 搜索引擎。

用例能够利用 Azure 搜索中最广泛的功能，包括在线目录、业务线程序以及文档发现应用程序。

### <a name="feature-drilldown"></a>功能明细

#### <a name="full-text-search-and-text-analysis"></a>全文搜索和文本分析

可以使用[简单的查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)来表述查询，该查询语法提供了逻辑运算符、短语搜索运算符、后缀运算符、优先级运算符。 此外，[Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)可以启用模糊搜索、近似搜索、术语提升和正则表达式。 Azure 搜索还支持[自定义的词汇分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)，以允许应用程序使用拼音匹配和正则表达式处理复杂的搜索查询。

#### <a name="language-support"></a>语言支持

Azure 搜索支持适用于 [56 种不同语言](https://docs.microsoft.com/rest/api/searchservice/language-support)的词汇分析器。 使用 Lucene 分析器和 Microsoft 分析器（Office 和必应中的自然语言处理已经过多年优化），Azure 搜索可以在应用程序的搜索框中分析文本，从而以智能方式处理特定于语言的语言学，包括谓词时态、词性、不规则复数名词（例如“mouse”与“mice”）、词取消复合、词拆分（对于不带空格的语言）等。

#### <a name="data-integration"></a>数据集成

你可以推送 JSON 数据结构来填充 Azure 搜索索引。 另外，对于受支持的数据源，你可以使用[索引器](search-indexer-overview.md)自动爬网式搜索 Azure SQL 数据库、Azure DocumentDB 或 [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)，以便将搜索索引的内容与主要数据存储同步。

**文档破解**实现了[主要文件格式的索引编制](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office、PDF 和 HTML 文档。

#### <a name="search-experience"></a>搜索体验

+ 可针对自动完成搜索栏和提前键入查询启用**搜索建议**。 当用户输入部分搜索输入内容时，[将显示索引中实际文档的建议](https://docs.microsoft.com/rest/api/searchservice/suggesters)。

+ 通过[仅仅单个查询参数](https://docs.microsoft.com/azure/search/search-faceted-navigation)实现了**分面导航**。 Azure 搜索返回一个分面导航结构，你可以将该结构用作类别列表背后的代码，用于自定向筛选（例如，按价格范围或品牌来筛选目录项）。

+ 可以使用**筛选器**将分面导航纳入到应用程序的 UI 中，改进查询表述，以及基于用户或开发人员指定的条件进行筛选。 可以使用 [OData 语法](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)创建筛选器。

+ **命中项突出显示**[向搜索结果中的匹配关键字应用可视化格式设置](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 可以选择哪些字段返回突出显示的片段。

+ **简单计分**是 Azure 搜索的主要优点。 [计分配置文件](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)用于在文档中自行将相关性建模为值的函数。 例如，你可能希望较新产品或打折产品显示在搜索结果的顶部位置。 也可以基于已跟踪和单独存储的客户搜索首选项将标记用于个性化计分，来生成计分配置文件。

+ **排序**通过索引架构提供给多个字段，然后使用单个搜索参数在查询时切换。

+ [通过 Azure 搜索所提供的对搜索结果的优化控制](search-pagination-page-layout.md)，**分页**和限制搜索结果将变得更简单。  

#### <a name="geosearch"></a>地理搜索

Azure 搜索可以智能地处理、筛选和显示地理位置。 借助 Azure 搜索，用户可以基于搜索结果对指定位置的临近程度浏览数据，或者基于特定的地理区域浏览数据。 本视频介绍其工作原理：[第 9 频道：Azure 搜索和地理空间数据](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)。

#### <a name="cloud-service-advantages"></a>云服务的优点

+ **高可用性**可确保极其可靠的搜索服务体验。 正确缩放时，[Azure 搜索可提供 99.9% SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

+ 对于作为端到端解决方案的**完全托管**，Azure 搜索绝对不需要基础结构管理。 通过在两个维度进行缩放以便处理更多文档存储和/或更高的查询负载，可以根据你的需求来定制服务。

#### <a name="monitoring-and-reporting"></a>监视和报告

+ [可以收集并分析](search-traffic-analytics.md)**搜索流量分析**，以便根据用户键入到搜索框的内容来解锁见解。

+ 门户页面中会捕获并报告关于每秒查询数、延迟和限制的指标，无需额外进行配置。 你还可以轻松监视索引和文档计数，以便可以根据需要调整容量。 

#### <a name="tools-for-prototyping-and-inspection"></a>用于原型制作和检查的工具

在门户中，可以使用**导入数据**向导来配置索引器、索引设计器以建立索引，并可以使用**搜索浏览器**对直接来自你帐户的 Azure 门户的所有索引发出查询，从而测试查询并优化计分配置文件。 还可以打开任何索引来查看其架构。

## <a name="how-to-get-started"></a>如何入门

可以先从免费服务起步，然后[使用内置的示例数据创建并查询索引](search-get-started-portal.md)。 若非必须编写代码，对于所有任务，都可以使用门户，这是一种快速试用 Azure 搜索的方法。

1. Azure 订户可以[在免费层中预配服务](search-create-service-portal.md)。

  非订户可以[打开免费的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)并使用免费信用额度来试用付费 Azure 服务。 信用额度用完后，你可以保留帐户并继续使用免费的 Azure 服务，例如网站。 除非你显式更改设置并要求付费，否则不会对你的信用卡收取任何费用。

  你还可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。 MSDN 订阅每月为你提供可用来试用付费版 Azure 服务的信用额度。 

2. 可以针对[内置的房地产业示例数据集](search-get-started-portal.md#create-index)运行**导入数据**向导。

  此向导可以基于大多数 Azure 数据源生成并加载索引。 对于向导未明确支持的数据源，必须编写代码。

3. 可以使用[搜索浏览器](search-get-started-portal.md#query-index)来查询索引。

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

虽然可以在门户中执行许多任务，但 Azure 搜索是为希望将搜索功能集成到现有应用程序中的开发人员打造的。 可以使用以下编程接口。

|平台 |说明 |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | 任何编程平台和语言（包括 Xamarin、Java 和 JavaScript）支持的 HTTP 命令|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API 的 .NET 包装器以 C# 和其他针对 .NET Framework 的托管代码语言提供了有效编码。 |

## <a name="watch-a-short-video"></a>观看短视频

搜索引擎是在移动应用中、网站上和公司数据存储中检索信息时常用的驱动程序。 Azure 搜索提供了用于打造与大型商业网站上的搜索体验类似的搜索体验的工具。 这段时长为 9 分钟的视频出自项目经理 Liam Cavanagh 之手，其中介绍了集成一个搜索引擎如何会使你的应用受益、Azure 搜索中的主要功能，以及典型的工作流是怎样的。 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 分钟介绍了主要功能和用例。
+ 3-4 分钟介绍了服务预配。 
+ 4-6 分钟介绍了用来使用内置的房地产业数据集创建索引的“导入数据”向导。
+ 6-9 分钟介绍了搜索浏览器和各种查询。



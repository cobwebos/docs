---
title: "什么是 Azure 搜索 | Microsoft Docs"
description: "Azure 搜索是完全托管的托管云搜索服务。 通过此功能概述了解详细信息。"
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
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 29385af9183ef2f8431581aaa5fe38e89404d068


---
# <a name="what-is-azure-search"></a>什么是 Azure 搜索？
Azure 搜索是一种搜索即服务云解决方案，它将服务器和基础结构的管理委托给 Microsoft，为用户提供随时可用的服务，用户可在填充数据后使用此服务将搜索添加到 Web 或移动应用程序。 借助 Azure 搜索，可以使用简单的 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 或 [.NET SDK](search-howto-dotnet-sdk.md) 在应用程序中添加稳定的搜索体验，无需管理搜索基础结构或精通搜索技术。

## <a name="give-your-users-a-powerful-search-experience"></a>为用户提供功能强大的搜索体验
**功能强大的查询**可以使用[简单的查询语法](https://msdn.microsoft.com/library/azure/dn798920.aspx)进行表述，它提供逻辑运算符、短语搜索运算符、后缀运算符、优先级运算符。 此外，[Lucene 查询语法](https://msdn.microsoft.com/library/azure/mt589323.aspx)可以启用模糊搜索、近似搜索、术语提升和正则表达式。 Azure 搜索还支持自定义的词汇分析器，以允许应用程序使用拼音匹配和正则表达式处理复杂的搜索查询。

[为 56 种不同语言提供](https://msdn.microsoft.com/library/azure/dn879793.aspx)**语言支持**。 使用 Lucene 分析器和 Microsoft 分析器（Office 和必应中的自然语言处理已经过多年优化），Azure 搜索可以在应用程序的搜索框中分析文本，从而以智能方式处理特定于语言的语言学，包括谓词时态、词性、不规则复数名词（例如“mouse”与“mice”）、词取消复合、词拆分（对于不带空格的语言）等。

可针对自动完成搜索栏和提前键入查询启用**搜索建议**。 当用户输入部分搜索输入内容时，[将显示索引中实际文档的建议](https://msdn.microsoft.com/library/azure/dn798936.aspx)。

**突出显示**[允许](https://msdn.microsoft.com/library/azure/dn798927.aspx)用户在包含其查询匹配项的每个结果中查看文本片段。 可以选择哪些字段返回突出显示的片段。

使用 Azure 搜索，可轻松将**分面导航**添加到搜索结果页。 使用[仅单个查询参数](https://msdn.microsoft.com/library/azure/dn798927.aspx)，Azure 搜索将返回所有必需的信息以便在应用 UI 中构造分面搜索体验，以便用户可以深入了解和筛选搜索结果（例如按价格范围或品牌筛选目录项）。

**地理空间**支持以智能方式处理、筛选和显示地理位置。 借助 Azure 搜索，用户可以基于指定位置搜索结果的临近程度浏览数据，或者基于特定的地理区域进行浏览。 本视频介绍其工作原理：[第 9 频道：Azure 搜索和地理空间数据](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)。

**筛选器**可用于轻松将分面导航合并到应用程序的 UI、增强查询表述，并基于特定于用户或开发人员的条件筛选。 使用 [OData 语法](https://msdn.microsoft.com/library/azure/dn798921.aspx)创建功能强大的筛选器。

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>使开发人员能够使用易用服务
**高可用性**可确保极其可靠的搜索服务体验。 正确缩放时，[Azure 搜索可提供 99.9% SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

对于作为端到端解决方案的**完全托管**，Azure 搜索绝对不需要基础结构管理。 通过在两个方面进行缩放来处理更多文档存储和/或更高查询负载，从而可以根据你的需求轻松定制服务。

使用[索引器](https://msdn.microsoft.com/library/azure/dn946891.aspx)的**数据集成**允许 Azure 搜索自动搜索 Azure SQL 数据库、Azure DocumentDB 或 [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)，以便将搜索索引的内容与主要数据存储同步。

**文档破译**（当前为预览版）可用于[读取主要文件格式并为其编制索引](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office 以及 PDF 和 HTML 文档。

[可轻松收集并分析](search-traffic-analytics.md)**搜索流量分析**，以便根据用户键入到搜索框的内容来解锁见解。

**简单计分**是 Azure 搜索的主要优点。 [计分配置文件](https://msdn.microsoft.com/library/azure/dn798928.aspx)用于允许组织在文档中自行将相关性建模为值函数。 例如，你可能希望较新产品或打折产品显示在搜索结果的顶部位置。 也可以基于已跟踪和单独存储的客户搜索首选项将标记用于个性化计分，来生成计分配置文件。

**排序**通过索引架构提供给多个字段，然后使用单个搜索参数在查询时切换。

[通过 Azure 搜索所提供的对搜索结果的优化控制](search-pagination-page-layout.md)，**分页**和限制搜索结果将变得更简单。  

借助**搜索资源管理器**，可直接根据所有索引从帐户的 Azure 门户发出查询，以便可以轻松测试查询并优化计分配置文件。

## <a name="how-it-works"></a>工作原理
### <a name="1-provision-service"></a>1.预配服务
可使用 [Azure 门户](https://portal.azure.com/)或 [Azure 资源管理 API](https://msdn.microsoft.com/library/azure/dn832684.aspx) 调整 Azure 搜索服务。

根据搜索服务的配置方式，将使用与其他 Azure 搜索订阅者共享的服务免费层，或仅由你的服务使用的专用资源[付费层](https://azure.microsoft.com/pricing/details/search/)。 预配服务时，还需要选择托管服务的数据中心区域。

根据所选服务的层，可在以下两个方面缩放服务：1) 添加副本以便增长容量来处理大量查询负载，以及 2) 添加分区以便为更多文档添加存储。 通过单独处理文档存储和查询吞吐量，可以根据你自己的特定需求来自定义搜索服务。

### <a name="2-create-index"></a>2.创建索引
将内容上载到 Azure 搜索服务之前，必须先定义 Azure 搜索索引。 索引类似于用于保存数据的数据库表，可接受搜索查询。 定义索引架构以便映射到要搜索的文档结构，这类似于数据库中的字段。

这些索引的架构可在 Azure 门户中创建，也可以[使用 .NET SDK](search-howto-dotnet-sdk.md) 或 [REST API](https://msdn.microsoft.com/library/azure/dn798941.aspx) 以编程方式进行创建。 定义索引后，可以将数据上载到 Azure 搜索服务以便随后编制索引。

### <a name="3-index-data"></a>3.索引数据
在定义索引的字段和属性后，可随时将内容上载到索引中。 可使用推模型或拉模型将数据上载到索引。

通过可按需或为计划更新配置的索引器提供拉模型（请参阅[索引器操作（Azure 搜索服务 REST API）](https://msdn.microsoft.com/library/azure/dn946891.aspx)），以便你可以轻松从 Azure DocumentDB、Azure SQL 数据库、Azure Blob 存储或托管在 Azure VM 中的 SQL Server 引入数据和数据更改。

推模型通过 SDK 或 REST API 进行提供，用于将更新的文档发送到索引。 可以从使用 JSON 格式的几乎任何数据集推送数据。 有关加载数据的指南，请参阅[添加、更新或删除文档](https://msdn.microsoft.com/library/azure/dn798930.aspx)或[如何使用.NET SDK）](search-howto-dotnet-sdk.md)。

### <a name="4-search"></a>4.搜索
在填充 Azure 搜索索引后，现在可通过将简单的 HTTP 请求与 REST API 或 .NET SDK 结合使用，向服务终结点[发出搜索查询](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

## <a name="try-it-now-for-free"></a>立即试用（免费！）
可立即试用 Azure 搜索！ 如果已经有 Azure 帐户，可以[在免费层中预配服务](search-create-service-portal.md)。

如果没有 Azure 帐户，可以尝试免费的 60 分钟会话，无需进行注册。 转到[试用 Azure App Service](http://go.microsoft.com/fwlink/p/?LinkId=618214)，然后选择“Web 应用”。 然后，选择“ASP.NET + Azure 搜索”模板以开始操作。




<!--HONumber=Nov16_HO3-->



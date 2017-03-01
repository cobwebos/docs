---
title: "如何使用 Azure 搜索中的计分配置文件 | Microsoft Docs"
description: "通过计分配置文件优化 Azure 搜索中的搜索排名，这是 Microsoft Azure 上的一项托管云搜索服务。"
services: search
documentationcenter: 
author: HeidiSteen
manager: mblythe
editor: 
ms.assetid: 7af6ede7-318f-4cea-8fdb-27090460c261
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/17/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 7a633460f249f7c7b6675a5ab23b1724549cccba
ms.openlocfilehash: ca07067b73b72b5ff8b93a72fa7eef55e9d47cc2


---
# <a name="how-to-use-scoring-profiles-in-azure-search"></a>如何使用 Azure 搜索中的计分配置文件
计分配置文件是 Microsoft Azure 搜索的一项功能，用于自定义搜索评分的计算，这将影响项目在搜索结果列表中的排列方式。 通过提升满足预定义条件的项，可以考虑计分配置文件作为模型相关性的一种方式。 例如，假设应用程序是联机酒店预订网站。 通过提升 `location` 字段，包含相关字词（如“西雅图”）的搜索将导致 `location` 字段中具有“西雅图”的项分数较高。 请注意，可以拥有多个计分配置文件或者没有（如果默认计分足以满足应用程序）。

为了帮助你试用计分配置文件，可下载使用计分配置文件的示例应用程序，来更改搜索结果的排名顺序。 示例是控制台应用程序（对于实际应用程序开发而言或许不太现实），尽管如此，它却是一款有用的学习工具。 

示例应用程序演示了使用称为 `musicstoreindex` 的虚构数据的评分行为。 使用简单的示例应用，可以轻松修改计分配置文件和查询，然后查看在执行程序时对排名顺序的直接影响。

<a id="sub-1"></a>

## <a name="prerequisites"></a>先决条件
示例应用程序是使用 Visual Studio 2013 以 C# 形式编写的。 如果你还没有 Visual Studio 的副本，请试用免费的 [Visual Studio 2013 Express 版本](http://www.visualstudio.com/products/visual-studio-express-vs.aspx)。

将需要 Azure 订阅和 Azure 搜索服务才能完成教程。 有关设置服务方面的帮助，请参阅[在门户中创建搜索服务](search-create-service-portal.md)。

[!INCLUDE [You need an Azure account to complete this tutorial](../../includes/free-trial-note.md)]

<a id="sub-2"></a>

## <a name="download-the-sample-application"></a>下载示例应用程序
转到 codeplex 上的 [Azure 搜索计分配置文件演示](https://azuresearchscoringprofiles.codeplex.com/)，以便下载本教程中所述的示例应用程序。

在“源代码”选项卡上，单击“下载”获取解决方案的 zip 文件。 

 ![][12]

<a id="sub-3"></a>

## <a name="edit-appconfig"></a>编辑 app.config
1. 提取文件后，在 Visual Studio 中打开解决方案以便编辑配置文件。
2. 在“解决方案资源管理器”中，双击“app.config”。 此文件指定服务终结点以及用于验证请求的 `api-key`。 可以从经典门户中获取这些值。
3. 登录到 [Azure 门户](https://portal.azure.com)。
4. 转到 Azure 搜索的服务仪表板。
5. 单击“属性”磁贴以复制服务 URL
6. 单击“密钥”磁贴以复制 `api-key`。

向 app.config 添加完 URL 和 `api-key` 后，应用程序设置应如下所示：

   ![][11]

<a id="sub-4"></a>

## <a name="explore-the-application"></a>了解应用程序
你基本可以生成并运行应用了，但在执行此操作前，请先查看用于创建和填充索引的 JSON 文件。

**Schema.json** 会定义索引，包括本演示中强调的计分配置文件。 请注意，架构会定义索引中使用的所有字段，包括可用于计分配置文件的不可搜索字段，例如 `margin`。 计分配置文件语法将在[将计分配置文件添加到 Azure 搜索索引](http://msdn.microsoft.com/library/azure/dn798928.aspx)中进行介绍。

**Data1 3.json** 提供数据，即跨多个流派的 246 张专辑。 数据是实际专辑和艺术家信息的组合，其中包括用于说明搜索操作的虚构字段，如 `price` 和 `margin`。 数据文件符合索引并且会上载到 Azure 搜索服务。 上载数据并为其编制索引后，可以对其发出查询。

**Program.cs** 执行以下操作:

* 打开控制台窗口。
* 使用服务 URL 和 `api-key` 连接到 Azure 搜索。
* 删除 `musicstoreindex`（如果存在）。
* 使用 schema.json 文件创建新的 `musicstoreindex`。
* 使用数据文件填充索引。
* 使用四个查询查询索引。 请注意，计分配置文件会指定为查询参数。 所有查询都会搜索相同的字词“best”。 第一个查询演示默认计分。 其余三个查询使用计分配置文件。

<a id="sub-5"></a>

## <a name="build-and-run-the-application"></a>构建并运行应用程序
若要排除连接或组件引用问题，请生成并运行应用程序，以确保没有需要先处理的问题。 应该会看到控制台应用程序在后台打开。 所有四个查询会按顺序执行，而不会暂停。 在许多系统上，整个程序会在 15 秒内执行。 如果控制台应用程序包含一条消息，指示“完成。 按 Enter 继续“，则表示程序已成功完成。 

若要比较查询运行，可以从控制台对查询结果执行标记-复制-粘贴操作，并将它们粘贴到 Excel 文件中。 

下图并行显示前三个查询的结果。 所有查询都使用相同的搜索字词“best”，该字词出现在许多专辑标题中。

   ![][10]

第一个查询使用默认计分。 因为搜索字词仅出现在专辑标题中，并且未指定其他任何条件，所以会按照找到的顺序返回专辑标题中具有“best”的项目。 

第二个查询使用计分配置文件，但请注意，该配置文件没有任何作用。 结果与第一个查询的结果相同。 这是因为计分配置文件会提升与查询无关的字段（“流派”）。 搜索字词“best”不存在于任何文档的任何“流派”字段中。 当计分配置文件没有任何作用时，结果会与默认计分相同。  

第三个查询是计分配置文件影响的第一个证据。 搜索字词仍是“best”，因此我们目前处理的是同一组专辑，但因为计分配置文件提供了可提升“排名”和“上次更新”的额外条件，所以列表中会有一些项目会推高。

下图显示第四个（也是最后一个）查询，该查询按“边际收益”提升。 “边际收益”字段不可搜索并且无法在搜索结果中返回。 “边际收益”值手动添加到电子表格，来帮助阐释具有较高边际收益的项目在搜索结果列表中的显示位置较靠前。 

   ![][9]

现在已试用计分配置文件，可尝试更改程序，以便使用不同的查询语法、计分配置文件或更丰富的数据。 下一部分中的链接将提供详细信息。

<a id="next-steps"></a>

## <a name="next-steps"></a>后续步骤
了解有关计分配置文件的详细信息。 有关详细信息，请参阅[将计分配置文件添加到 Azure 搜索索引](http://msdn.microsoft.com/library/azure/dn798928.aspx)。

了解有关搜索语法和查询参数的详细信息。 有关详细信息，请参阅[搜索文档（Azure 搜索 REST API）](http://msdn.microsoft.com/library/azure/dn798927.aspx)。

需要回顾并深入了解索引创建？ [观看本视频](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)以了解基本概念。

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG 



<!--HONumber=Feb17_HO3-->



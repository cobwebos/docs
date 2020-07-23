---
title: 在 Azure 门户中创建演示应用
titleSuffix: Azure Cognitive Search
description: 运行“创建演示应用(预览版)”向导为正常运行的 Web 应用生成 HTML 页面和脚本。 页面包含搜索栏、结果区域、边栏和自动提示支持。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 07/01/2020
ms.openlocfilehash: c6ab5c2cae2bb966c2b040b40dbf36e56a54411b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496746"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>快速入门：在门户中创建演示应用（Azure 认知搜索）

使用 Azure 门户的“创建演示应用”向导生成可在浏览器中运行的、可供下载的“localhost”样式 Web 应用。 根据其配置，生成的应用在首次使用时就能正常运行，并与远程索引建立实时只读连接。 默认应用可以包含搜索栏、结果区域、边栏筛选器和自动提示支持。

演示应用有助于你直观地了解索引在客户端应用中的工作方式，但其不适用于生产场景。 客户端应用应包括生成的 HTML 页未提供的安全性、错误处理和托管逻辑。 准备好创建客户端应用时，请参阅[使用 .NET SDK 创建第一个搜索应用](tutorial-csharp-create-first-app.md)以了解后续步骤。

## <a name="prerequisites"></a>先决条件

在开始之前，必须满足以下条件：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/)。

+ Azure 认知搜索服务。 [创建服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。 

+ [Microsoft Edge（最新版本）](https://www.microsoft.com/edge)或 Google Chrome。

+ 要用作所生成应用程序的基础的[搜索索引](search-what-is-an-index.md)。 

  本快速入门使用内置的“房地产”示例数据和索引，因为该示例包含缩略图（向导支持将图像添加到结果页）。 若要创建本练习中使用的索引，请运行“导入数据”向导，并选择“realestate-us-sample”数据源。

  ![示例数据的数据源页](media/search-create-app-portal/import-data-realestate.png)

索引可供使用后，转到下一步。

## <a name="start-the-wizard"></a>启动向导

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)。

1. [查找搜索服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)，然后在“概述”页中间的链接中，选择“索引”。 

1. 从现有索引的列表中选择“realestate-us-sample-index”。

1. 在索引页的顶部，选择“创建演示应用(预览版)”以启动向导。

1. 在第一个向导页上，选择“启用跨源资源共享(CORS)”以将 CORS 支持添加到索引定义。 此步骤是可选的，但如果不执行此步骤，本地 Web 应用无法连接到远程索引。

## <a name="configure-search-results"></a>配置搜索结果

向导将为呈现的搜索结果提供一个基本布局，其中包含缩略图、标题和说明的空间。 其中的每个元素由索引中一个提供数据的字段赋值。 

1. 在“缩略图”中，选择“realestate-us-sample”索引中的“thumbnail”字段。  此示例正好包含 URL 定址图像形式的缩略图，这些缩略图存储在名为 *thumbnail* 的字段中。 如果索引中没有图像，请将此字段留空。

1. 在“标题”中，选择能够表达每个文档的唯一性的字段。 在此示例中，列表 ID 是合理的选择。

1. 在“说明”中，选择一个提供详细信息的字段，这些信息可以帮助用户确定是否要单击进入特定的文档。

   ![示例数据的数据源页](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>添加边栏

搜索服务支持分面导航（通常以边栏的形式呈现）。 分面基于可筛选且可分面的字段，在索引架构中表示。

在 Azure 认知搜索中，分面导航是一种累积筛选体验。 在一个类别中选择多个筛选器会展开结果（例如，在“城市”中选择“西雅图”和“贝尔维尤”）。 跨类别选择多个筛选器会缩小结果范围。

> [!TIP]
> 可以在门户中查看整个索引架构。 在每个索引的概述页中，找到“索引定义(JSON)”链接。 符合分面导航要求的字段具有“filterable: true”和“facetable: true”属性。

接受当前所做的分面选择，然后转到下一页。


## <a name="add-typeahead"></a>添加自动提示功能

自动提示功能以自动完成和查询建议的形式提供。 向导支持查询建议。 根据用户提供的击键输入，搜索服务将返回可选作输入的“已完成”查询字符串列表。

在特定的字段定义中启用建议。 向导提供相应的选项用于配置要在建议中包含的信息量。 

以下屏幕截图显示了向导中的选项，同时显示了应用中呈现的页面。 其中展示了如何使用字段选项，以及如何使用“显示字段名称”在建议中包含或排除标签。

![查询建议配置](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>创建、下载和执行

1. 选择“创建演示应用”，生成 HTML 文件。

1. 出现提示时，请选择“下载应用”以下载文件。

1. 打开 文件。 应会看到类似于以下屏幕截图的页面。 输入一个搜索词，然后使用筛选器来缩小结果范围。 

基础索引由虚构生成的、已在不同文档中复制的数据构成，说明有时与图像不符。 如果基于自己的索引创建应用，则预期会获得更有整体性的体验。

![运行应用](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

默认应用对于初始探索和小任务非常有用，而尽早查看 API 有助于更深入地了解概念和工作流：

> [!div class="nextstepaction"]
> [使用 .NET SDK 创建索引](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
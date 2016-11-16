---
title: "使用 Azure 门户查询 Azure 搜索索引 | Microsoft Docs"
description: "在 Azure 门户的搜索资源管理器中发出搜索查询。"
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a23372112e17703a3399e1bdc9eaf73b85a1f80d


---
# <a name="query-your-azure-search-index-using-the-azure-portal"></a>使用 Azure 门户查询 Azure 搜索索引
> [!div class="op_single_selector"]
> * [概述](search-query-overview.md)
> * [门户](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

本指南介绍如何在 Azure 门户中查询 Azure 搜索索引。

开始本演练前，应已[创建 Azure 搜索索引](search-what-is-an-index.md)并[填充数据](search-what-is-data-import.md)。

## <a name="i-go-to-your-azure-search-blade"></a>I. 转到“Azure 搜索”边栏选项卡
1. 在 [Azure 门户](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. 选择你的 Azure 搜索服务

## <a name="ii-select-the-index-you-would-like-to-search"></a>II. 选择要搜索的索引
1. 选择要从“索引”磁贴中搜索的索引。

![](./media/search-explorer/pick-index.png)

## <a name="iii-click-on-the-search-explorer-tile"></a>III. 单击“搜索资源管理器”磁贴
![](./media/search-explorer/search-explorer-tile.png)

## <a name="iii-start-searching"></a>III. 开始搜索
1. 若要搜索“Azure 搜索”索引，请开始键入“查询字符串”字段中，然后按“搜索”。
   
   * 使用“搜索资源管理器”时，可以指定任何 [查询参数](https://msdn.microsoft.com/library/dn798927.aspx)
2. 在“结果”部分中，发出针对 Azure 搜索 REST API 的搜索请求后，查询的结果将显示在 HTTP 响应正文中接收到的原始 JSON 中。
3. 查询字符串将自动解析为相应的请求 URL，以便提交针对 Azure 搜索 REST API 的 HTTP 请求

![](./media/search-explorer/search-bar.png)




<!--HONumber=Nov16_HO2-->



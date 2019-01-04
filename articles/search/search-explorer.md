---
title: Azure 门户中用于查询索引的搜索浏览器 - Azure 搜索
description: 使用搜索浏览器等 Azure 门户工具在 Azure 搜索中查询索引。 使用高级语法输入搜索词或完全限定的搜索字符串。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 11f102fcb2a24f9062313f9a3234c29e70a3dfe0
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315651"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>如何使用搜索浏览器在 Azure 搜索中查询索引 

本文介绍如何使用搜索浏览器在 Azure 门户中查询现有 Azure 搜索索引。 可以使用搜索浏览器，向服务中的任何现有索引提交简单或完整的 Lucene 查询字符串。

## <a name="open-the-service-dashboard"></a>打开服务仪表板
1. 在 [Azure 门户](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)左侧的跳转栏中，单击“所有资源”。
2. 选择 Azure 搜索服务。

## <a name="select-an-index"></a>选择索引

选择要从“索引”磁贴中搜索的索引。

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>打开搜索浏览器

单击“搜索浏览器”磁贴，以滑动方式打开搜索栏和结果窗格。

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>开始搜索

使用“搜索浏览器”时，可以指定用于表述查询的[查询参数](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。

1. 在“查询字符串”中键入查询，然后按“搜索”。 

   查询字符串将自动解析为相应的请求 URL，以便提交针对 Azure 搜索 REST API 的 HTTP 请求。   
   
   可以使用任何有效的 Lucene 查询语法（不管是简单的还是完整的）来创建请求。 `*` 字符相当于空的或未指定的搜索，返回没有特定顺序的所有文档。

2. 在“结果”中，查询结果以原始 JSON 格式呈现，等同于以编程方式发出请求时，在 HTTP 响应正文中返回的有效负载。

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>后续步骤

以下资源提供更多的查询语法信息和示例。

 + [简单的查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene 查询语法示例](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [OData 筛选器表达式语法](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
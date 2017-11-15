---
title: "Azure 搜索服务 REST API 版本 2016-09-01-Preview | Microsoft Docs"
description: "Azure 搜索服务 REST API 版本 2016-09-01-Preview 包括试验功能，如同义词和 moreLikeThis 搜索。"
services: search
documentationcenter: na
author: mhko
manager: jlembicz
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 082c207f892fcc277d30d66c6165dd9920d3ab27
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Azure 搜索服务 REST API：版本 2016-09-01-Preview
本文是 `api-version=2016-09-01-Preview` 的参考文档。 此预览版通过提供以下实验功能扩展了当前公开发布的版本 [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx)：

* [同义词 API](search-synonyms.md)用于上传同义词映射并扩展搜索。
* [`moreLikeThis` 查询参数](search-more-like-this.md)用于查找与特定文档相关的文档。

请针对 API 预览版 `api-version=2016-09-01-Preview`，尝试使用这些试验功能。 以下示例说明在进行 more-like-this 查询时如何指定 API 预览版。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> 预览功能可用于目的为收集反馈的测试和试验，可能随时更改。 **我们强烈建议不要在生产应用程序中使用预览版 API。**

Azure 搜索服务在多个版本内可用。 有关详细信息，请参考[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

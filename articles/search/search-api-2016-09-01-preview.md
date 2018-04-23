---
title: Azure 搜索服务 REST API 版本 2016-09-01-Preview | Microsoft Docs
description: Azure 搜索服务 REST API 版本 2016-09-01-Preview 包括试验功能，如同义词和 moreLikeThis 搜索。
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 034c7c9c6e97ebb128860b6041089b311ffaf528
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
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

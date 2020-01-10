---
title: 重置技能（api 版本 = 2019-050-06-01.5.1）
titleSuffix: Azure Cognitive Search
description: 预览 REST API 在需要完全或部分重新处理技能组合时用于增量扩充。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832156"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>重置技能（api 版本 = 2019-05-06-01.5.1）

> [!IMPORTANT] 
> 增量扩充目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前没有门户或 .NET SDK 支持。

**重置技能**请求指定将在下一次索引器运行时处理的技能。 对于已启用缓存的索引器，你可以为索引器无法检测的技术更新显式请求处理。 例如，如果您对自定义技能进行了外部更改，例如对自定义技能的修订，则可以使用此 API 重新运行技能。 将使用缓存中的可重用数据以及每个更新的技能的新内容刷新输出，如知识存储或搜索索引。

可以使用 HTTP PUT 重置现有的[技能组合](https://docs.microsoft.com/rest/api/searchservice/create-skillset)，并指定要在请求 URI 上更新的技能组合的名称。 必须在请求上使用**api 版本 = 2019-05-06-01.5.1-Preview** 。

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

或者，使用 POST 并将索引器名称放入请求正文中：

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>请求标头  

 下表介绍必需和可选的请求标头。  

|请求标头|Description|  
|--------------------|-----------------|  
|Content-Type：|必需。 将其设置为 `application/json`|  
|api-key：|必需。 `api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务是唯一的。 **Reset 技能组合**请求必须包含设置为管理密钥（而不是查询密钥）的 `api-key` 标头。|  

还需要服务名称才能构造请求 URL。 你可以从 Azure 门户的服务概述页中获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[创建 Azure 认知搜索服务](https://docs.microsoft.com/azure/search/search-create-service-portal)。  

## <a name="request-body-syntax"></a>请求正文语法  

请求正文是一组技能名称。

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>响应  

对于成功的响应，返回“状态代码：204 无内容”。 

## <a name="see-also"></a>另请参阅

+ [搜索 REST Api](https://docs.microsoft.com/rest/api/searchservice)
+ [HTTP 状态代码](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [AI 扩充概述](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [配置缓存和增量扩充](search-howto-incremental-index.md)
+ [增量扩充](cognitive-search-incremental-indexing-conceptual.md)
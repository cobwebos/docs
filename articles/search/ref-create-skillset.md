---
title: 创建技能组合 (REST api-version=2017-11-11-Preview) - Azure 搜索 | Microsoft Docs
description: 技能组合是一组构成扩充管道的认知技能。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786906"
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>创建技能组合 (api-version=2017-11-11-Preview)

**适用范围：** api-version-2017-11-11-Preview

技能组合是一组用于处理自然语言和其他转换的认知技能。 技能包括命名实体提取、关键短语提取、将文本分块成逻辑页等。

若要使用技能组合，请在 Azure 搜索索引器中进行引用，然后运行索引器以导入数据、调用转换和扩充以及将输出字段映射到索引。 技能组合是高级资源，但它只能在索引器处理中运行。 由于技能组合是高级资源，因此，你只需设计一次，便可在多个索引器中进行引用。 

可通过 HTTP PUT 或 POST 请求在 Azure 搜索中表示技能组合。 对于 PUT，请求的正文是一个 JSON 架构，用于指定调用的技能。 技能通过输入-输出关联链接在一起，其中一个转换的输出变成另一个转换的输入。

技能组合必须具有至少一种技能。 理论上，技能的最大数量没有限制，但常见配置为三到五种。  


> [!NOTE]
> 认知搜索现推出公共预览版，目前免费提供技能组合执行。 我们日后会公布此功能的定价。

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>请求  
 所有服务请求都需要 HTTPS。 可使用 PUT 方法构造“创建技能组合”请求，并在 URL 中包含技能组合名称。 如果技能组合不存在，则创建它。 如果已经存在，则将其更新为新定义。 请注意，一次只能放置一个技能组合。  

 技能组合名称必须满足以下需求：

- 为小写
- 以字母或数字开头和结尾
- 不包含斜杠或点
- 少于 128 个字符 

技能组合名称以字母或数字开头后，名称其余部分可包含任何字母、数字和短划线，前提是短划线不是连续的。  

需要 api-version 参数。 唯一可用的版本为 `2017-11-11-Preview`。 有关所有版本的列表，请参阅 [Azure 搜索中的 API 版本](https://go.microsoft.com/fwlink/?linkid=834796)。 


### <a name="request-headers"></a>请求标头  

 下表介绍必需和可选的请求标头。  

|请求标头|说明|  
|--------------------|-----------------|  
|Content-Type：|必需。 将其设置为 `application/json`|  
|api-key：|必需。 `api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务是唯一的。 **创建技能组合**请求必须包含一个设置为管理密钥（而不是查询密钥）的 `api-key` 标头。|  

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。  

### <a name="request-body-syntax"></a>请求正文语法  

请求的正文包含技能组合定义，由一个或多个完全指定的技能以及可选名称和描述参数组成。  

构造请求负载的语法如下。 本文后面和[如何定义技能组合](cognitive-search-defining-skillset.md)中提供了一个请求示例。  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>请求示例
 以下示例创建用于扩充财务文档集合的技能组合。

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

请求的正文是一个 JSON 文档。 此特定技能组合以异步方式使用两种技能，独立处理 `contents` 的实质作为两种不同的转换。 或者，可将一个转换的输出指向另一个转换的输入。 有关详细信息，请参阅[如何定义技能组合](cognitive-search-defining-skillset.md)。

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ]
}
```

## <a name="response"></a>响应  

 对于成功的请求，应看到状态代码“201 Created”。  

 默认情况下，响应正文将包含已创建的技能组合定义的 JSON。 但是，如果 Prefer 请求标头设置为 return=minimal，响应正文将为空，并且成功状态代码将是“204 No Content”，而不是“201 Created”。 无论使用 PUT 还是 POST 创建技能组合都是如此。   

## <a name="see-also"></a>另请参阅

+ [认知搜索概述](cognitive-search-concept-intro.md)
+ [快速入门：尝试认知搜索](cognitive-search-quickstart-blob.md)
+ [教程：Azure Blob 的已扩充索引](cognitive-search-tutorial-blob.md)
+ [如何定义技能组合](cognitive-search-defining-skillset.md)
+ [如何映射字段](cognitive-search-output-field-mapping.md)
+ [如何定义自定义接口](cognitive-search-custom-skill-interface.md)
+ [示例：创建自定义技能](cognitive-search-create-custom-skill-example.md)
+ [预定义的技能](cognitive-search-predefined-skills.md)
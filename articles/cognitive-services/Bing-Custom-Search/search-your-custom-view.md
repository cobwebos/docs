---
title: 搜索自定义视图 - 必应自定义搜索
titlesuffix: Azure Cognitive Services
description: 介绍如何搜索 Web 的自定义视图。
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 235062c1b3e54843b5e64f4ef16091ae5d630894
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814349"
---
# <a name="call-your-custom-search"></a>调用自定义搜索

在第一次调用自定义搜索 API 以获取实例的搜索结果之前，需要先获取认知服务订阅密钥。 若要获取自定义搜索 API 的密钥，请参阅[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)。


## <a name="try-it-out"></a>试用

配置完自定义搜索体验后，可以在自定义搜索门户中测试配置。 

1. 登录[自定义搜索](https://customsearch.ai)。
2. 单击实例列表中的自定义搜索实例。
3. 单击“生产”选项卡。 
4. 在“终结点”选项卡下，选择一个终结点（例如，“Web API”）。 订阅决定了显示哪些终结点（有关订阅选项，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)）。 
5. 指定参数值。 

    下面列出了可设置的可能参数（实际列表视选择的终结点而定）。 有关这些参数的其他信息，请参阅[自定义搜索 API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) 参考。

    - **查询**：要搜索的搜索词。 仅适用于 Web 终结点、图像终结点、视频终结点和自动建议终结点。
    - **自定义配置 ID**：所选自定义搜索实例的配置 ID。 该字段是只读字段。
    - **市场**：结果的来源市场。 仅适用于 Web 终结点、图像终结点、视频终结点和托管 UI 终结点。
    - **订阅密钥**：测试时要使用的订阅密钥。 可以从下拉列表中选择一个密钥，也可以手动输入一个。  
      
    单击“其他参数”可显示以下参数：  
      
    - **安全搜索**：用于筛选成人内容网页的筛选器。 仅适用于 Web 终结点、图像终结点、视频终结点和托管 UI 终结点。
    - **用户界面语言**：用户界面字符串所用的语言。 例如，如果你在托管 UI 中启用图像和视频，“图像”和“视频”选项卡使用指定的语言。
    - **计数**：要在响应中返回的搜索结果数。 仅适用于 Web 终结点、图像终结点和视频终结点。
    - **偏移**：返回结果前要跳过的搜索结果数。 仅适用于 Web 终结点、图像终结点和视频终结点。

6. 指定所有必需选项后，单击“调用”即可在右侧窗格中查看 JSON 响应。 

如果选择“托管 UI”终结点，可以在底部窗格中测试搜索体验。

## <a name="next-steps"></a>后续步骤

- [使用 C# 调用自定义视图](./call-endpoint-csharp.md)
- [使用 Java 调用自定义视图](./call-endpoint-java.md)
- [使用 NodeJs 调用自定义视图](./call-endpoint-nodejs.md)
- [使用 Python 调用自定义视图](./call-endpoint-python.md)

- [使用 C# SDK 调用自定义视图](./sdk-csharp-quick-start.md)
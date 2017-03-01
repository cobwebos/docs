---
title: "从 DataMarket 建议 API 迁移到 Azure 认知服务建议 API| Microsoft Docs"
description: "Azure 机器学习建议 — 迁移到建议认知服务"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: ec9cc302-fef5-4b68-8f9b-fa73538d0424
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 8329cc800f685455be113521446e691b5798c1fc
ms.openlocfilehash: 072f92b186f32813eef9b17e742081b680ef86a3


---
# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>从 DataMarket 建议 API 迁移到 Azure 认知服务建议 API
本文介绍如何从 [Microsoft DataMarket 建议 API](https://datamarket.azure.com/dataset/amla/recommendations) 迁移到 [Microsoft Azure 认知服务建议 API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api)。

DataMarket 建议 API 将于 2016 年 12 月 31 日停止接受新客户，并于 2017 年 2 月 28 被弃用。

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>如何开始使用 Azure 认知服务建议 API？
要迁移到认知服务建议 API，请执行以下操作：

1. 如果你还没有 Azure 订阅，请[注册](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)一个。 
2. 从[快速入门指南](cognitive-services-recommendations-quick-start.md)获取分步说明以注册认知服务建议 API，并以编程方式使用它。 
3. 转到[认知服务建议 API 登录页面](https://www.microsoft.com/cognitive-services/en-us/recommendations-api)以了解该服务并查找文档。

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>我曾使用建议 UI 来生成模型。 认知服务建议 API 是否有类似工具？
当然！ 新的[建议 UI](http://recommendations-portal.azurewebsites.net/) 的 URL 是 http://recommendations-portal.azurewebsites.net。 

> [!NOTE]
> 你的 DataMarket 凭据在此处无效。 在 Azure 门户中注册订阅，获取使用新的[建议 UI](http://recommendations-portal.azurewebsites.net/) 所需的帐户密钥。
> 如果你没有帐户密钥，请参阅[快速入门指南](cognitive-services-recommendations-quick-start.md)的任务 1。
> 
> 

## <a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>新 API 的格式否与 DataMarket 建议 API 相同？
API 支持与 DataMarket 版本中支持的方案相同的方案和流程，但实际的 API 接口经过更新，符合 [Microsoft REST API 准则](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)。 API 更加一致，而且能与支持 Swagger 的工具更好地协作。

要了解每个 API，请查看 [API 资源管理器](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db)。
使用“*试用*”按钮测试 API 调用。 建议 API 使用的文件格式（目录和使用情况文件）未更改，因此你可以继续使用之前构建的相同文件和/或基础结构来生成这些文件。

## <a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>认知服务建议 API 中有哪些新功能？
在过去的两个月内，我们针对认知服务建议 API 发布了以下新功能：

* 用于训练和测试模型的建议 UI，无需编写单行代码
* 批处理计分，一次为你提供数千条建议
* 构建指标支持，用于查询建议模型的质量
* 业务规则支持
* 枚举并下载使用情况和目录文件的功能
* 排名构建支持，用于查询建议模型中的项目功能质量
* 附加功能，用于在目录中搜索产品

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>Microsoft 何时停止支持 DataMarket 建议 API？
[DataMarket 建议 API](https://datamarket.azure.com/dataset/amla/recommendations) 将于 2016 年 12 月 31 日之后停止接受新客户，并于 2017 年 2 月 28 完全被弃用。 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>如果我没有在认知服务建议 API 中重新创建模型所需的数据怎么办？
我们希望你能尽可能简单地进行这一过渡。 我们可以帮助你将旧模型从 DataMarket 帐户移至新的 Azure 认知服务建议 API 订阅。 请通过 [mlapi@microsoft.com](mailto://mlapi@microsoft.com) 联系我们。 我们需要你提供 DataMarket 订阅 ID 和认知服务订阅 ID，然后才能将模型与你的新帐户关联。




<!--HONumber=Dec16_HO2-->



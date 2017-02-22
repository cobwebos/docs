---
title: "如何创建媒体处理器 | Microsoft Docs"
description: "了解如何创建一个媒体处理器组件用来为 Azure 媒体服务编码、转换格式、加密或解密媒体内容。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: d1d05b46591fe4b72c92c59d357681c8e1cdb336
ms.openlocfilehash: c208660fc1439ca831ada6c9bb348dbc3eadc18c


---
# <a name="how-to-get-a-media-processor-instance"></a>如何：获取媒体处理器实例
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>概述
在媒体服务中，媒体处理器是完成特定处理任务（例如，对媒体内容进行编码、格式转换、加密或解密）的组件。 通常，当你创建一个任务以便对媒体内容进行编码、加密或格式转换时，就需要创建一个媒体处理器。

下表提供了每个可用媒体处理器的名称和说明。

| 媒体处理器名称 | 说明 | 更多信息 |
| --- | --- | --- |
| 媒体编码器标准版 |为按需编码提供标准功能。 |[简要介绍并比较 Azure 按需媒体编码器](media-services-encode-asset.md) |
| 媒体编码器高级工作流 |允许你使用媒体编码器高级工作流运行编码任务。 |[简要介绍并比较 Azure 按需媒体编码器](media-services-encode-asset.md) |
| Azure Media Indexer |使媒体文件和内容可搜索，以及生成隐藏字幕跟踪和关键字。 |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse（预览） |使你能够通过视频防抖动功能消除视频中的“晃动”。 也可使将内容制作为可用剪辑的速度加快。 |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Azure Media Encoder |已放弃 | |
| 存储解密 |已放弃 | |
| Azure 媒体包装器 |已放弃 | |
| Azure 媒体加密器 |已放弃 | |

## <a name="get-mediaprocessor"></a>获取 MediaProcessor
> [!NOTE]
> 使用媒体服务 REST API 时，需注意以下事项：
> 
> 访问媒体服务中的实体时，必须在 HTTP 请求中设置特定标头字段和值。 有关详细信息，请参阅[媒体服务 REST API 开发的设置](media-services-rest-how-to-use.md)。
> 
> 成功连接到 https://media.windows.net 后，将收到指定另一个媒体服务 URI 的 301 重定向。 必须按[使用 REST API 连接到媒体服务](media-services-rest-connect-programmatically.md)中所述，对新的 URI 执行后续调用。 
> 
> 

以下 REST 调用演示了如何按名称获取媒体处理器实例（在本例中为 **Media Encoder Standard**）。 

请求：

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net

响应：

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>后续步骤
了解如何获取媒体处理器实例后，请转到[如何对资产进行编码](media-services-rest-get-started.md)主题，其中说明了如何使用 Media Encoder Standard 对资产进行编码。




<!--HONumber=Feb17_HO3-->



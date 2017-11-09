---
title: "如何使用 REST 获取媒体处理器实例 | Microsoft 文档"
description: "了解如何创建一个媒体处理器组件用来为 Azure 媒体服务编码、转换格式、加密或解密媒体内容。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: 4ad90ad979c5bd74fc55155098c88d5c13cb12e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-a-media-processor-instance"></a>如何获取媒体处理器实例
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>概述
在媒体服务中，媒体处理器是完成特定处理任务（例如，对媒体内容进行编码、格式转换、加密或解密）的组件。 通常，创建一个任务以便对媒体内容进行编码、加密或格式转换时，就需要创建一个媒体处理器。

## <a name="azure-media-processors"></a>Azure 媒体处理器 

以下主题提供媒体处理器列表：

* [编码媒体处理器](scenarios-and-availability.md#encoding-media-processors)
* [分析媒体处理器](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>访问媒体服务中的实体时，必须在 HTTP 请求中设置特定标头字段和值。 有关详细信息，请参阅[媒体服务 REST API 开发的设置](media-services-rest-how-to-use.md)。

## <a name="connect-to-media-services"></a>连接到媒体服务

若要了解如何连接到 AMS API，请参阅[通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。 

>[!NOTE]
>成功连接到 https://media.windows.net 后，将收到指定另一个媒体服务 URI 的 301 重定向。 必须对这个新 URI 进行后续调用。

## <a name="get-a-media-processor"></a>获取媒体处理器

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


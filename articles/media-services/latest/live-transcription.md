---
title: 实时听录
titleSuffix: Azure Media Services
description: 了解 Azure 媒体服务实时脚本。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c3465e294af104c4d9c3b34960f5e95cf41e7cb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291409"
---
# <a name="live-transcription-preview"></a>实时脚本 (预览) 

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 媒体服务提供了不同协议中的视频、音频和文本。 使用 MPEG 破折号或 HLS/CMAF 发布实时流时，如果使用视频和音频，我们的服务将在 IMSC 1.1 兼容的 TTML 中提供转录文本。 此传递打包到 MPEG-4 第30部分， (ISO/IEC 14496-30) 片段。 如果通过 HLS/TS 使用传递，则文本将作为分块 VTT 传递。

开启活动脚本时，会收取额外费用。 请查看 [媒体服务定价页](https://azure.microsoft.com/pricing/details/media-services/)的实时视频部分中的定价信息。

本文介绍如何在使用 Azure 媒体服务流式传输实时事件时启用实时脚本。 继续操作之前，请确保熟悉媒体服务 v3 REST Api 的用法 (参阅 [本教程](stream-files-tutorial-with-rest.md) ，了解详细信息) 。 还应熟悉 [实时流式处理](live-streaming-overview.md) 的概念。 建议 [使用媒体服务进行实时流](stream-live-tutorial-with-api.md) 教程。

## <a name="live-transcription-preview-regions-and-languages"></a>实时脚本预览区域和语言

Live 脚本在以下区域中提供：

- Southeast Asia
- 西欧
- 北欧
- 美国东部
- 美国中部
- 美国中南部
- 美国西部 2
- 巴西南部

这是可转录的可用语言的列表，请使用 API 中的语言代码。

| 语言 | 语言代码 |
| -------- | ------------- |
| 加泰罗尼亚语  | ca-ES |
| 丹麦语（丹麦） | da-DK |
| 德语（德国） | de-DE |
| 英语（澳大利亚） | en-AU |
| 英语（加拿大） | en-CA |
| 英语（英国） | en-GB |
| 英语（印度） | en-IN |
| 英语（新西兰） | en-NZ |
| 英语（美国） | zh-CN |
| 西班牙语(西班牙) | es-ES |
| 西班牙语（墨西哥） | es-MX |
| 芬兰语（芬兰） | fi-FI |
| 法语（加拿大） | fr-CA |
| 法语（法国） | fr-FR |
| 意大利语（意大利） | it-IT |
| 荷兰语（荷兰） | nl-NL |
| 葡萄牙语（巴西） | pt-BR |
| 葡萄牙语(葡萄牙) | pt-PT |
| 瑞典语（瑞典） | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>创建实时事件和实时脚本

若要创建启用了脚本的实时事件，请使用 2019-05-01-preview API 版本发送 PUT 操作，例如：

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

操作具有以下主体 (，其中使用 RTMP 作为引入协议) 创建传递实时事件。 请注意，转录属性的添加。

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>开始或停止实时事件开始后的脚本

实时事件处于运行状态时，可以启动和停止实时脚本。 有关启动和停止实时事件的详细信息，请参阅 [通过媒体服务 V3 api 进行开发](media-services-apis-overview.md#long-running-operations)中的长时间运行的操作部分。

若要打开 live 转录或更新脚本语言，请修补 live 事件以包含 "转录" 属性。 若要关闭 live 转录，请删除实时事件对象中的 "转录" 属性。  

> [!NOTE]
> 不 **支持在实时事件期间多次** 开启或关闭脚本。

这是打开 live 转录的示例调用。

跳 ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>脚本传送和播放

查看 [动态打包概述](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) 一文，了解我们的服务如何使用动态打包来传送不同协议中的视频、音频和文本。 使用 MPEG 破折号或 HLS/CMAF 发布实时流时，如果使用视频和音频，我们的服务将在 IMSC 1.1 兼容的 TTML 中提供转录文本。 此传递将打包到14496-30 第30部分， (ISO/IEC) 片段。 如果通过 HLS/TS 使用传递，则文本将作为分块 VTT 传递。 您可以使用 web 播放器（如 [Azure Media Player](use-azure-media-player.md) ）播放流。  

> [!NOTE]
> 如果使用 Azure Media Player，请使用2.3.3 或更高版本。

## <a name="known-issues"></a>已知问题

对于预览版，以下是有关 live 脚本的已知问题：

- 应用需要使用预览版 Api，如 [媒体服务 V3 OpenAPI 规范](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)中所述。
-  (DRM) 保护的数字版权管理不适用于文本轨，只可能 AES 信封加密。

## <a name="next-steps"></a>后续步骤

* [媒体服务概述](media-services-overview.md)

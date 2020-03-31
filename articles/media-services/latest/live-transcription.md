---
title: 实时听录
titleSuffix: Azure Media Services
description: 了解 Azure 媒体服务的实时转录。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499874"
---
# <a name="live-transcription-preview"></a>实时转录（预览）

Azure 媒体服务以不同的协议提供视频、音频和文本。 当您使用 MPEG-DASH 或 HLS/CMAF 发布实时流时，以及视频和音频，我们的服务提供 IMSC1.1 兼容 TTML 中的转录文本。 交付被打包成 MPEG-4 第 30 部分（ISO/IEC 14496-30）片段。 如果使用通过 HLS/TS 传递，则文本将作为分块 VTT 传递。

本文介绍如何使用 Azure 媒体服务 v3 流式传输实时事件时启用实时转录。 在继续之前，请确保您熟悉媒体服务 v3 REST API 的使用（有关详细信息，请参阅[本教程](stream-files-tutorial-with-rest.md)）。 您还应熟悉[实时流式处理](live-streaming-overview.md)的概念。 建议[使用媒体服务教程完成"流实时](stream-live-tutorial-with-api.md)"。

> [!NOTE]
> 目前，实时转录仅在美国西部 2 区域作为预览功能提供。 它支持将英语口语转录为文本。 此功能的 API 引用位于下方 — 请看它是预览版，我们的 REST 文档不提供详细信息。

## <a name="creating-the-live-event"></a>创建实时事件

要创建实时事件，请将 PUT 操作发送到 2019-05-01 预览版，例如：

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

该操作具有以下正文（其中使用 RTMP 作为引入协议创建传递实时事件）。 请注意添加转录属性。 唯一允许的语言值是 en-US。

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

轮询实时事件的状态，直到它进入"正在运行"状态，这表明您现在可以发送贡献 RTMP 源。 现在，您可以按照本教程中相同的步骤，例如检查预览源和创建实时输出。

## <a name="transcription-delivery-and-playback"></a>转录传递和播放

查看[动态打包概述](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)文章，了解我们的服务如何使用动态打包以不同协议提供视频、音频和文本。 当您使用 MPEG-DASH 或 HLS/CMAF 发布实时流时，以及视频和音频，我们的服务提供 IMSC1.1 兼容 TTML 中的转录文本。 此交付被打包到 MPEG-4 第 30 部分（ISO/IEC 14496-30） 片段中。 如果使用通过 HLS/TS 传递，则文本将作为分块 VTT 传递。 可以使用 Web 播放器（如[Azure 媒体播放器）](use-azure-media-player.md)来播放流。  

> [!NOTE]
> 如果使用 Azure 媒体播放器，请使用版本 2.3.3 或更高版本。

## <a name="known-issues"></a>已知问题

对于预览，以下是实时转录的已知问题：

* 此功能仅在美国西部 2 中可用。
* 应用需要使用[媒体服务 v3 OpenAPI 规范](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)中描述的预览 API。
* 唯一支持的语言是英语。
* 使用内容保护，仅支持 AES 信封加密。

## <a name="next-steps"></a>后续步骤

* [媒体服务概述](media-services-overview.md)

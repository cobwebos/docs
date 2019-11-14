---
title: Azure 媒体服务实时脚本 |Microsoft Docs
description: 本文介绍 Azure 媒体服务的实时操作。
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 98084b9bb6f19d22c7995d3044bb32ceaa947dc5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040419"
---
# <a name="live-transcription-preview"></a>实时脚本（预览）

Azure 媒体服务以不同协议提供视频、音频和现文本。 使用 MPEG 破折号或 HLS/CMAF 发布实时流时，如果使用视频和音频，我们的服务将在 IMSC 1.1 兼容的 TTML 中提供转录文本，并打包到 MPEG-4 第30部分（ISO/IEC 14496-30）片段。 如果通过 HLS/TS 使用传递，则文本将作为分块 VTT 传递。 

本文介绍如何在使用 Azure 媒体服务 v3 流式传输实时事件时启用实时脚本。 在继续操作之前，请确保熟悉媒体服务 v3 REST Api 的使用（有关详细信息，请参阅[本教程](stream-files-tutorial-with-rest.md)）。 还应熟悉[实时流式处理](live-streaming-overview.md)的概念。 建议[使用媒体服务进行实时流](stream-live-tutorial-with-api.md)教程。 

> [!NOTE]
> 目前，只能在美国西部2区域中使用预览功能。 它支持将英语字词表达为文本。 此功能的 API 参考在此文档中提供，因为它处于预览阶段，我们的 REST 文档不提供详细信息。 

## <a name="creating-the-live-event"></a>创建实时事件 

若要创建实时事件，请将 PUT 操作发送到2019-05-01 版本，如： 

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

操作具有以下主体（其中，使用 RTMP 作为引入协议创建传递实时事件）。 请注意，转录属性的添加。 语言唯一允许的值为 en-us。 

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

你应轮询实时事件的状态，直到其进入 "正在运行" 状态，这表示你现在可以发送 RTMP 源。 你现在可以按照本教程中所述的步骤进行操作，如检查预览源并创建实时输出。 

## <a name="delivery-and-playback"></a>传递和播放 

查看[动态打包概述](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)一文，了解我们的服务如何使用动态打包来传送不同协议中的视频、音频和现文本。 使用 MPEG 破折号或 HLS/CMAF 发布实时流时，如果使用视频和音频，我们的服务将在 IMSC 1.1 兼容的 TTML 中提供转录文本，并打包到 MPEG-4 第30部分（ISO/IEC 14496-30）片段。 如果通过 HLS/TS 使用传递，则文本将作为分块 VTT 传递。 您可以使用 web 播放器（如[Azure Media Player](use-azure-media-player.md) ）播放流。  

> [!NOTE]
>  如果使用 Azure Media Player，请使用2.3.3 或更高版本。

## <a name="known-issues"></a>已知问题 

预览时，以下是实时脚本的已知问题 

* 此功能仅在美国西部2中可用。
* 应用程序需要使用预览版 Api，如[媒体服务 V3 OpenAPI 规范](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)规范中所述。
* 唯一受支持的语言是英语。
* 对于内容保护，只支持 AES 信封加密。

## <a name="next-steps"></a>后续步骤

[媒体服务概述](media-services-overview.md)
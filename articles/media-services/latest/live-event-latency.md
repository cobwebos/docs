---
title: Azure 媒体服务中的 LiveEvent 低延迟设置 | Microsoft Docs
description: 本主题概述 LiveEvent 低延迟设置，并说明如何设置低延迟。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 114efe668fba47e9d83741b8fa45e1f396725198
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291495"
---
# <a name="live-event-low-latency-settings"></a>直播活动低延迟设置

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文展示了如何在[直播活动](/rest/api/media/liveevents)上设置较低的延迟。 它还讨论了在各种播放器中使用低延迟设置时看到的典型结果。 结果因 CDN 和网络延迟而异。

若要使用新的 **LowLatency** 功能，请在 **LiveEvent** 上将 **StreamOptionsFlag** 设置为 **LowLatency**。 为 HLS 播放创建 [LiveOutput](/rest/api/media/liveoutputs) 时，将 [LiveOutput.Hls.fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) 设置为 1。 流启动并运行后，可以使用 [Azure Media Player](https://ampdemo.azureedge.net/)（AMP 演示页），并设置播放选项以使用“低延迟启发式配置文件”。

> [!NOTE]
> 目前，Azure Media Player 中的“低延迟启发式配置文件”设计用于在 MPEG-DASH 协议中播放采用 CSF 或 CMAF 格式（例如，`format=mdp-time-csf` 或 `format=mdp-time-cmaf`）的流。 

以下 .NET 示例展示了如何在 **LiveEvent** 上设置 **LowLatency**：

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

参阅完整示例：[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)。

## <a name="live-events-latency"></a>直播活动延迟

下表显示了媒体服务中的延迟的典型结果（当启用了 LowLatency 标志时），延迟指的是从贡献源到达服务之时到查看者看到播放器上的回放之时之间的时间。 若要以最佳方式使用低延迟，应将编码器设置下调至 1 秒“图片组”(GOP) 长度。 当使用较高的 GOP 长度时，可以在相同帧速率下最小化带宽消耗并降低比特率。 在动作较少的视频中特别有用。

### <a name="pass-through"></a>直通 

||启用 2 秒 GOP 低延迟|启用 1 秒 GOP 低延迟|
|---|---|---|
|**AMP 中的 DASH**|10 秒|8 秒|
|**本机 iOS 播放器上的 HLS**|14 秒|10 秒|

### <a name="live-encoding"></a>实时编码

||启用 2 秒 GOP 低延迟|启用 1 秒 GOP 低延迟|
|---|---|---|
|**AMP 中的 DASH**|14 秒|10 秒|
|**本机 iOS 播放器上的 HLS**|18 秒|13 秒|

> [!NOTE]
> 根据本地网络状况以及是否引入了 CDN 缓存层，端到端延迟可能有所不同。 应当测试你的确切配置。

## <a name="next-steps"></a>后续步骤

- [实时传送视频流概述](live-streaming-overview.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)

---
title: Azure 媒体服务中的 LiveEvent 延迟 | Microsoft Docs
description: 本主题概述了 LiveEvent 延迟，并展示了如何设置较低的延迟。
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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 30cd77799837f9b1ef08a9c609e518fd679b9b15
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871416"
---
# <a name="live-event-latency-in-media-services"></a>媒体服务中的实时事件延迟

本文展示了如何在[实时事件](https://docs.microsoft.com/rest/api/media/liveevents)上设置较低的延迟。 它还讨论了在各种播放器中使用低延迟设置时看到的典型结果。 结果因 CDN 和网络延迟而异。

若要使用新的 **LowLatency** 功能，请在 **LiveEvent** 上将 **StreamOptionsFlag** 设置为 **LowLatency**。 为 HLS 播放创建 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) 时，将 [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) 设置为 1。 流启动并运行后，可以使用 [Azure Media Player](https://ampdemo.azureedge.net/)（AMP 演示页），并设置播放选项以使用“低延迟启发式配置文件”。

> [!NOTE]
> 目前，Azure Media Player 中的“低延迟启发式配置文件”设计用于在 DASH 协议中播放流，或使用 CMAF 播放 HLS。 如果要通过带有 TS 的 HLS（例如，`format=m3u8-aapl` 或 `format=m3u8-aapl-v3`）针对 MacOS 或 iOS 设备进行操作，则不应使用此设置，因为 AMP 在这种情况下会直接使用 OS 提供的本机播放器。

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

查看完整示例：[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)。

## <a name="live-events-latency"></a>实时事件延迟

下表显示了媒体服务中的延迟的典型结果（当启用了 LowLatency 标志时），延迟指的是从贡献源到达服务之时到查看者看到播放器上的回放之时之间的时间。 若要以最佳方式使用低延迟，应将编码器设置下调至 1 秒“图片组”(GOP) 长度。 当使用较高的 GOP 长度时，可以在相同帧速率下最小化带宽消耗并降低比特率。 在动作较少的视频中特别有用。

### <a name="pass-through"></a>直通 

||启用 2 秒 GOP 低延迟|启用 1 秒 GOP 低延迟|
|---|---|---|
|AMP 中的 DASH|10 秒|8 秒|
|本机 iOS 播放器上的 HLS|14 秒|10 秒|

### <a name="live-encoding"></a>实时编码

||启用 2 秒 GOP 低延迟|启用 1 秒 GOP 低延迟|
|---|---|---|
|AMP 中的 DASH|14 秒|10 秒|
|本机 iOS 播放器上的 HLS|18 秒|13 秒|

> [!NOTE]
> 根据本地网络状况以及是否引入了 CDN 缓存层，端到端延迟可能有所不同。 应当测试你的确切配置。

## <a name="next-steps"></a>后续步骤

- [实时传送视频流概述](live-streaming-overview.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)


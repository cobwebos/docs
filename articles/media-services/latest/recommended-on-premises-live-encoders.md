---
title: 了解媒体服务建议的实时传送视频流本地编码器 -  Azure | Microsoft Docs
description: 了解媒体服务建议的实时传送视频流本地编码器
services: media-services
keywords: 编码;编码器;媒体
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 4d25e4cd94fec35f31594544b619aa054a35d58d
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54302334"
---
# <a name="recommended-live-streaming-encoders"></a>建议的实时传送视频流编码器

在媒体服务中，[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents)（通道）表示用于处理实时传送视频流内容的管道。 LiveEvent 通过以下两种方式之一接收实时输入流：

* 本地实时编码器将多比特率 RTMP 或平滑流式处理（分片 MP4）流发送到无法通过媒体服务进行实时编码的 LiveEvent。 引入流将通过 LiveEvent，而不会进行任何进一步处理。 这种方法称为**直通**。 实时编码器可以将单比特率流发送到直通通道，但并不建议此配置，因为它不允许对客户端进行自适应比特率流式处理。

  > [!NOTE]
  > 实时传送视频流时，使用直通方法是最经济的。

* 本地实时编码器采用以下格式之一将单比特率流发送至能够使用媒体服务执行实时编码的 LiveEvent：RTMP 或平滑流式处理（分片 MP4）。 然后，LiveEvent 将对传入的单比特率流执行实时编码，使之转换为多比特率（自适应）视频流。

若要详细了解如何使用媒体服务进行实时编码，请参阅[使用媒体服务 v3 的实时传送视频流](live-streaming-overview.md)。

## <a name="live-encoders-that-output-rtmp"></a>输出 RTMP 的实时编码器

媒体服务建议使用下列将 RTMP 作为输出的实时编码器之一。 支持的 URL 方案为 `rtmp://` 或 `rtmps://`。

> [!NOTE]
 > 通过 RTMP 流式处理时，检查防火墙和/或代理设置，确认出站 TCP 端口 1935 和 1936 已打开。<br/>
 通过 RTMPS 流式处理时，检查防火墙和/或代理设置，确认出站 TCP 端口 2935 和 2936 已打开。

- Adobe Flash 媒体实时编码器 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>输出分片 MP4 的实时编码器

媒体服务建议使用下列将多比特率平滑流式处理（分片 MP4）作为输出的实时编码器之一。 支持的 URL 方案为 `http://` 或 `https://`。

- Ateme TITAN Live
- Cisco 数字媒体编码器 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)

## <a name="how-to-become-an-on-premises-encoder-partner"></a>如何成为本地编码器合作伙伴

作为 Azure 媒体服务本地编码器合作伙伴，媒体服务通过向企业客户推荐编码器来推广你的产品。 若要成为本地编码器合作伙伴，必须验证本地编码器与媒体服务的兼容性。 为此，请完成以下验证：

### <a name="pass-through-liveevent-verification"></a>直通 LiveEvent 验证

1. 创建或访问 Azure 媒体服务帐户。
2. 创建并启动**直通** LiveEvent。
3. 配置编码器推送多比特率实时流。
4. 创建已发布的实时事件。
5. 运行实时编码器大约 10 分钟。
6. 停止实时事件。
7. 创建、启动流式处理终结点，使用诸如 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 之类的播放器来观看已存档的资产，以确保播放没有所有质量级别的明显问题（或者，在第 6 步之前的实时会话中，通过预览 URL 进行观看和验证）。
8. 记录资产 ID、为实时存档发布的流式处理 URL，以及实时编码器所使用的设置和版本。
9. 在创建每个示例后重置 LiveEvent 状态。
10. 为编码器所支持的所有配置重复（具有或无广告信号/字幕/不同编码速度）步骤 3-9。

### <a name="live-encoding-liveevent-verification"></a>实时编码 LiveEvent 验证

1. 创建或访问 Azure 媒体服务帐户。
2. 创建并启动**实时编码** LiveEvent。
3. 配置编码器推送单比特率实时流。
4. 创建已发布的实时事件。
5. 运行实时编码器大约 10 分钟。
6. 停止实时事件。
7. 创建、启动流式处理终结点，使用诸如 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 之类的播放器来观看已存档的资产，以确保播放没有所有质量级别的明显问题（或者，在第 6 步之前的实时会话中，通过预览 URL 进行观看和验证）。
8. 记录资产 ID、为实时存档发布的流式处理 URL，以及实时编码器所使用的设置和版本。
9. 在创建每个示例后重置 LiveEvent 状态。
10. 为编码器所支持的所有配置（有或没有广告信号/字幕/不同编码速度）重复步骤 3-9。

### <a name="longevity-verification"></a>使用寿命验证

1. 创建或访问 Azure 媒体服务帐户。
2. 创建并启动**直通**通道。
3. 配置编码器推送多比特率实时流。
4. 创建已发布的实时事件。
5. 运行实时编码器一周或更长时间。
6. 使用 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 等播放器不时观看实时传送视频流（或存档资产），以确保播放没有明显问题。
7. 停止实时事件。
8. 记录资产 ID、为实时存档发布的流式处理 URL，以及实时编码器所使用的设置和版本。

最后，通过电子邮件将记录的设置和实时存档参数作为通知发送到 Azure 媒体服务 (amsstreaming@microsoft.com)，告知所有自我验证检查已通过。 另请提供联系信息，以供后续跟踪之用。 有关此过程的任何问题，请联系 Azure 媒体服务团队。

## <a name="next-steps"></a>后续步骤

[使用媒体服务 v3 实时传送视频流](live-streaming-overview.md)

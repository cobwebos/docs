---
title: 媒体服务建议的实时传送视频流编码器 -  Azure | Microsoft Docs
description: 了解媒体服务建议的实时传送视频流本地编码器
services: media-services
keywords: 编码;编码器;媒体
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 01/17/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: cc55466dac7344053e0cce3cad5f8161cef5226a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438119"
---
# <a name="recommended-live-streaming-encoders"></a>建议的实时传送视频流编码器

在 Azure 媒体服务中，[实时事件](https://docs.microsoft.com/rest/api/media/liveevents)（通道）表示用于处理实时传送视频流内容的管道。 实时事件通过以下两种方式之一接收实时输入流。

* 本地实时编码器将多比特率 RTMP 或平滑流式处理（分片 MP4）流发送到无法通过媒体服务进行实时编码的实时事件。 引入流将通过实时事件，而不会进行任何进一步处理。 这种方法称为**直通**。 实时编码器可将单比特率流发送到直通通道。 我们不建议使用此配置，因为它不允许对客户端进行自适应比特率流式处理。

  > [!NOTE]
  > 实时传送视频流时，使用直通方法是最经济的。

* 本地实时编码器采用以下格式之一将单比特率流发送至能够使用媒体服务执行实时编码的实时事件：RTMP 或平滑流式处理（分片 MP4）。 然后，实时事件将对传入的单比特率流执行实时编码，使之转换为多比特率（自适应）视频流。

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

## <a name="configuring-on-premises-live-encoder-settings"></a>配置本地实时编码器设置

有关对实时事件类型有效的设置的信息，请参阅[事件类型比较](live-event-types-comparison.md)。

### <a name="playback-requirements"></a>播放要求

若要播放内容，音频和视频流都必须存在。 不支持播放纯视频流。

### <a name="configuration-tips"></a>配置提示

- 尽可能使用硬编码的 Internet 连接。
- 确定带宽要求时，可以认为它就是将流式处理比特率翻倍。 虽然不一定要这样考虑，但这种简单的经验法则有助于减轻网络拥塞的影响。
- 使用基于软件的编码器时，请关闭任何不需要的程序。
- 开始推送之后更改编码器配置会对事件造成负面影响。 配置更改可能会导致事件不稳定。 
- 请确保自己有充足的时间来设置事件。 对于大规模事件，我们建议在事件之前的一小时开始设置。

## <a name="becoming-an-on-premises-encoder-partner"></a>成为本地编码器合作伙伴

作为 Azure 媒体服务本地编码器合作伙伴，媒体服务通过向企业客户推荐编码器来推广你的产品。 若要成为本地编码器合作伙伴，必须验证本地编码器与媒体服务的兼容性。 为此，请完成以下验证。

### <a name="pass-through-live-event-verification"></a>直通实时事件验证

1. 在媒体服务帐户中，确保**流式处理终结点**正在运行。 
2. 创建并启动直通实时事件。 <br/> 有关详细信息，请参阅[实时事件状态和计费](live-event-states-billing.md)。
3. 获取引入 URL 并配置本地编码器以使用 URL 将多比特率实时流发送到媒体服务。
4. 获取预览 URL 并使用它验证来自编码器的输入是否实际接收。
5. 创建新的**资产**对象。
6. 创建**实时输出**并使用创建的资产名称。
7. 使用内置的流式处理策略类型创建流式处理定位符。
8. 列出流式处理定位器的路径，以取回要使用的 URL。
9. 获取要从中流式传输的**流式处理终结点**的主机名。
10. 将步骤 8 中的 URL 与步骤 9 中的主机名合并，获取完整的 URL。
11. 运行实时编码器大约 10 分钟。
12. 停止实时事件。 
13. 使用 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 等播放器观看存档的资产，确保以各种质量水平播放时不会出现明显的问题。 或者，在实时会话中通过预览 URL 进行观看和验证。
14. 记录资产 ID、为实时存档发布的流式处理 URL，以及实时编码器所使用的设置和版本。
15. 在创建每个示例后重置实时事件状态。
16. 对编码器支持的所有配置重复（有或无广告信号、字幕或不同编码速度）步骤 5 到 15。

### <a name="live-encoding-live-event-verification"></a>实时编码实时事件验证

1. 在媒体服务帐户中，确保**流式处理终结点**正在运行。 
2. 创建并启动实时编码实时事件。 <br/> 有关详细信息，请参阅[实时事件状态和计费](live-event-states-billing.md)。
3. 获取引入 URL，并配置编码器以将单比特率实时流推送到媒体服务。
4. 获取预览 URL 并使用它验证来自编码器的输入是否实际接收。
5. 创建新的**资产**对象。
6. 创建**实时输出**并使用创建的资产名称。
7. 使用内置的流式处理策略类型创建流式处理定位符。
8. 列出流式处理定位器的路径，以取回要使用的 URL。
9. 获取要从中流式传输的**流式处理终结点**的主机名。
10. 将步骤 8 中的 URL 与步骤 9 中的主机名合并，获取完整的 URL。
11. 运行实时编码器大约 10 分钟。
12. 停止实时事件。
13. 使用 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 等播放器观看存档的资产，确保以各种质量水平播放时不会出现明显的问题。 或者，在实时会话中通过预览 URL 进行观看和验证。
14. 记录资产 ID、为实时存档发布的流式处理 URL，以及实时编码器所使用的设置和版本。
15. 在创建每个示例后重置实时事件状态。
16. 对编码器支持的所有配置重复（有或无广告信号、字幕或不同编码速度）步骤 5 到 15。

### <a name="longevity-verification"></a>使用寿命验证

遵循[直通实时事件验证](#pass-through-live-event-verification)中的相同步骤（步骤 11 除外）。 <br/>运行实时编码器一周或更长时间，而不是 10 分钟。 使用 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 等播放器不时观看实时传送视频流（或存档的资产），确保播放没有明显问题。

### <a name="email-your-recorded-settings"></a>通过电子邮件发送记录的设置

最后，通过电子邮件将记录的设置和实时存档参数作为通知发送到 Azure 媒体服务 (amsstreaming@microsoft.com)，告知所有自我验证检查已通过。 另请提供联系信息，以方便跟进。 在此过程中如有任何问题，可以联系 Azure 媒体服务团队。

## <a name="next-steps"></a>后续步骤

[使用媒体服务 v3 实时传送视频流](live-streaming-overview.md)

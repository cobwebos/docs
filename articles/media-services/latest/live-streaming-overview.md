---
title: 使用 Azure 媒体服务实时传送视频流概述 | Microsoft Docs
description: 本主题概述了如何使用 Azure 媒体服务 v3 实时传送视频流。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: e9ecf1ba3022ca057fa09bad2413aa19d902ae23
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972173"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>使用 Azure 媒体服务 v3 实时传送视频流

使用 Azure 媒体服务传递实时流式处理事件时，通常涉及以下组件：

* 一个用于广播事件的相机。
* 一个实时视频编码器，用于将信号从相机（或其他设备，如笔记本电脑）转换为发送至媒体服务实时传送视频流服务的流。 信号可能还包括广告 SCTE-35 和 Ad-cues。 
* 可以通过媒体服务实时传送视频流服务引入、预览、打包、记录、加密内容并将其广播给客户，或者广播给 CDN 进行进一步分发。

本文详细地概述了使用媒体服务的实时传送视频流中涉及的主要组件并提供了其关系图。

## <a name="overview-of-main-components"></a>主要组件概述

在媒体服务中，[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 负责处理实时传送视频流内容。 LiveEvent 提供输入终结点（引入 URL），然后由你将该终结点提供给本地实时编码器。 LiveEvent 从实时编码器接收 RTMP 或平滑流式处理格式的实时输入流，并通过一个或多个 [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints) 使其可用于流式处理。 可以通过 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) 来控制实时流的发布、记录和 DVR 窗口设置。 LiveEvent 还提供预览终结点（预览 URL），用于在进一步处理和传送流之前对流进行预览和验证。 

使用媒体服务提供的**动态打包**，可通过 MPEG DASH、HLS、平滑流式处理流式传输格式预览和广播内容，无需以手动方式将其重新打包成这些流式传输格式。 可以使用任何与 HLS、DASH 或平滑流式处理兼容的播放器进行播放。 也可以使用 [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) 来测试流。

使用媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中的任意一个动态加密（**动态加密**）的内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。

也可以根据需要应用**动态筛选**，以便控制发送至播放器的曲目、格式、比特率的数目。 媒体服务还支持 ad-insertion。


## <a name="liveevent-types"></a>LiveEvent 类型

[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 可以是下述两种类型之一：实时编码和直通。 

### <a name="live-encoding-with-media-services"></a>使用媒体服务进行实时编码

![实时编码](./media/live-streaming/live-encoding.png)

本地实时编码器采用 RTMP 或平滑流式处理（分片 MP4）协议将单比特率流发送至能够使用媒体服务执行实时编码的 LiveEvent。 然后，LiveEvent 将对传入的单比特率流执行实时编码，使之转换为多比特率（自适应）视频流。 收到请求时，媒体服务会将该流传递给客户。

创建此类 LiveEvent 时，请指定 **Basic** (LiveEventEncodingType.Basic)。

### <a name="pass-through"></a>直通

![直通](./media/live-streaming/pass-through.png)

直通已经过优化，适合长时间运行的实时流或使用本地实时编码器进行的全天候线性实时编码。 本地编码器将多比特率 **RTMP** 或**平滑流式处理**（分片 MP4）发送到经配置可以进行**直通**传送的 LiveEvent。 **直通**传送是指引入的流会直接通过 **LiveEvent**，而不会经过任何进一步的处理。 

直通 LiveEvent 可以支持高达 4K 的分辨率，与平滑流式处理引入协议配合使用时还支持 HEVC 直通。 

创建此类 LiveEvent 时，请指定 **None** (LiveEventEncodingType.None)。

> [!NOTE]
> 当需要长时间处理多个事件，并且已经在本地编码器上进行了投入时，则可使用直通这种最经济的方法来实时传送视频流。 请参阅 [定价](https://azure.microsoft.com/pricing/details/media-services/) 详细信息。
> 

## <a name="liveevent-types-comparison"></a>LiveEvent 类型比较 

下表比较了两种 LiveEvent 类型的功能。

| 功能 | 直通 LiveEvent | 基本 LiveEvent |
| --- | --- | --- |
| 单比特率输入在云中被编码为多比特率 |否 |是 |
| 最大分辨率，层数 |4Kp30  |720p，6 层，30 fps |
| 输入协议 |RTMP、平滑流 |RTMP、平滑流 |
| 价格 |请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡 |请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/) |
| 最长运行时间 |全天候运行 |全天候运行 |
| 支持插入静态图像 |否 |是 |
| 支持通过 API 发出广告指示|否 |是 |
| 支持通过带内 SCTE35 发出广告指示|是 |是 |
| 直通 CEA 608/708 字幕 |是 |是 |
| 能够从贡献源出现的短时停顿中恢复 |是 |否（如果超过 6 秒而没有输入数据，LiveEvent 就会开始插入静态图像）|
| 支持非一致性输入 GOP |是 |否 - 输入必须是固定的 2 秒 GOP |
| 支持可变帧率输入 |是 |否 - 输入必须是固定的帧速率。<br/>轻微的帧率变化是容许的，例如在高速运动情况下出现的轻微帧率变化。 但是，编码器不能掉到 10 帧/秒的帧率。 |
| 输入源丢失时，会自动关闭 LiveEvent |否 |12 小时后，如果没有运行的 LiveOutput |

## <a name="liveevent-states"></a>LiveEvent 状态 

LiveEvent 的当前状态。 可能的值包括：

|省/直辖市/自治区|说明|
|---|---|
|**已停止**| 这是 LiveEvent 在创建后的初始状态（除非指定了自动启动）。此状态下不会发生计费。 在此状态下，可以更新 LiveEvent 属性，但不允许进行流式传输。|
|**正在启动**| LiveEvent 正在启动。 此状态下不会发生计费。 在此状态下，不允许进行更新或流式传输。 如果发生错误，则 LiveEvent 会返回到“已停止”状态。|
|**正在运行**| LiveEvent 能够处理实时流。 现在会计收使用费。 必须停止 LiveEvent 以防止进一步计费。|
|**正在停止**| 正在停止 LiveEvent。 此暂时性状态下不会发生计费。 在此状态下，不允许进行更新或流式传输。|
|**正在删除**| 正在删除 LiveEvent。 此暂时性状态下不会发生计费。 在此状态下，不允许进行更新或流式传输。|

## <a name="liveoutput"></a>LiveOutput

可以通过 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) 来控制实时流的发布、记录和 DVR 窗口设置。 LiveEvent 和 LiveOutput 的关系类似于传统媒体，其中频道 (LiveEvent) 具有恒定的内容流，而节目 (LiveOutput) 的范围限定为该 LiveEvent 上的一些定时事件。
可以通过设置 **ArchiveWindowLength** 属性，指定希望保留多少小时的 LiveOutput 录制内容。 **ArchiveWindowLength** 是一种表示存档时长的 ISO 8601 时段（数字视频记录器，简称 DVR）。 此值的设置范围是最短 5 分钟，最长 25 小时。 

**ArchiveWindowLength** 还决定了客户端能够从当前实时位置按时间向后搜索的最长时间。 超出指定时间长度后，LiveOutput 也能够运行，但落在时间窗口长度后面的内容将全部被丢弃。 此属性的值还决定了客户端清单能够增加多长时间。

每个 LiveOutput 与一个[资产](https://docs.microsoft.com/rest/api/media/assets)相关联，并将数据记录到已附加到媒体服务帐户的 Azure 存储的一个容器中。 若要发布 LiveOutput，必须为关联的资产创建 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)。 创建此定位符后，可以生成提供给客户端的流 URL。

一个 LiveEvent 最多支持三个并发运行的 LiveOutput，因此可以为同一传入流创建多个存档。 这样，便可以根据需要发布和存档事件的不同部分。 例如，你的业务要求是广播全天候的实时线性源，但你需要创建全天的节目“记录”，以便将其作为点播内容提供给客户，供其抽时间观看。  对于这种情况，请先创建一个用作主实时流的主 LiveOutput，其存档窗口较短，为 1 小时或不到 1 小时，供客户观看。 可以为此 LiveOutput 创建一个 StreamingLocator，然后将其作为“实时”源发布到应用程序或网站。  当源处于运行状态以后，可以在节目开始时通过编程方式创建另一个并发 LiveOutput（也可提前 5 分钟这样做，以便提供一些可供以后剪裁的句柄）。这第二个 LiveOutput 可以在节目或事件结束后 5 分钟停止，然后你就可以创建新的 StreamingLocator，以便将此节目作为点播资产发布到应用程序的目录中。  可以针对其他需要立即以点播方式共享的节目边界或突出显示内容多次重复此过程，同时第一个 LiveOutput 提供的“实时”源可以继续广播线性源。  另外，可以利用“动态筛选器”支持来剪裁 LiveOutput 提供的存档的头和尾（引入此支持功能是为了确保节目之间的“重叠安全性”），以便更准确地确定内容的开始和结束位置。 存档的内容也可提交到某个[转换](https://docs.microsoft.com/rest/api/media/transforms)，以便通过编码或帧级别子剪辑方式将其转换成多种可与其他服务联合使用的输出格式。

## <a name="streamingendpoint"></a>StreamingEndpoint

将流传输到 LiveEvent 后，可以通过创建资产、LiveOutput 和 StreamingLocator 来启动流式传输事件。 这会存档流，并使观看者可以通过 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 使用该流。

创建媒体服务帐户后，一个处于“已停止”状态的默认流式处理终结点会添加到帐户。 若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。

## <a name="billing"></a>计费

一旦 LiveEvent 的状态转换为“正在运行”，就会开始计费。 若要停止 LiveEvent 的计费，必须停止 LiveEvent。

> [!NOTE]
> 将 [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 上的 **LiveEventEncodingType** 设置为 Basic 后，媒体服务就会自动关闭在输入源丢失 12 小时后仍处于“正在运行”状态但却没有 LiveOutput 运行的 LiveEvent。 但是，LiveEvent 处于“正在运行”状态的时间段内，仍会进行计费。
>

下表显示 LiveEvent 状态如何映射到计费模式。

| LiveEvent 状态 | 是否计费？ |
| --- | --- |
| 正在启动 |否（暂时状态） |
| 正在运行 |是 |
| 正在停止 |否（暂时状态） |
| 已停止 |否 |

## <a name="next-steps"></a>后续步骤

[实时传送视频流教程](stream-live-tutorial-with-api.md)

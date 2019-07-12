---
title: Azure 媒体服务中的实时传送视频流概念 - 实时事件和实时输出 | Microsoft Docs
description: 本文概述了 Azure 媒体服务 v3 中的实时传送视频流概念。
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
ms.date: 06/19/2019
ms.author: juliako
ms.openlocfilehash: a951ebd46335ad4639b8499283ddd30f13edd64e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605657"
---
# <a name="live-events-and-live-outputs"></a>实时事件和实时输出

使用 Azure 媒体服务可将实时事件传送到 Azure 云中的客户。 若要配置媒体服务 v3 中的实时流式处理事件，您需要了解本文中讨论的概念。

> [!TIP]
> 从媒体服务 v2 Api 迁移的客户**Live 事件**实体替换**通道**v2 中并**Live 输出**替换**程序**.

## <a name="live-events"></a>实时事件

[实时事件](https://docs.microsoft.com/rest/api/media/liveevents)负责引入和处理实时视频源。 当您创建实时事件时，可用于从远程编码器发送实时信号创建主要和次要的输入终结点。 远程实时编码器将发送的输入终结点使用贡献到源[RTMP](https://www.adobe.com/devnet/rtmp.html)或[平滑流式处理](https://msdn.microsoft.com/library/ff469518.aspx)(分片 MP4) 输入协议。 将内容 RTMP 引入协议，可以以明文形式发送 (`rtmp://`) 或在网络上安全地加密 (`rtmps://`)。 对于平滑流式处理引入协议，支持的 URL 方案为 `http://` 或 `https://`。  

## <a name="live-event-types"></a>实时事件类型

[实时事件](https://docs.microsoft.com/rest/api/media/liveevents)可以是下述两种类型之一：直通和实时编码。 在创建使用过程中设置类型[LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -的本地实时编码器将多个比特率流发送。 引入的流将通过无需任何进一步的处理实时事件。 
* **LiveEventEncodingType.Standard** -的本地实时编码器发送单比特率流的实时事件和媒体服务创建多个比特率流。 如果贡献源为 720p 或更高分辨率**Default720p**预设将对一组 6 解析/比特率对进行编码。
* **LiveEventEncodingType.Premium1080p** -的本地实时编码器发送单比特率流的实时事件和媒体服务创建多个比特率流。 Default1080p 预设指定输出组解析/比特率对。 

### <a name="pass-through"></a>直通

![直通](./media/live-streaming/pass-through.svg)

使用直通**实时事件**，可以依赖本地实时编码器生成多比特率视频流，并将其作为贡献源发送到实时事件（使用 RTMP 或分段 MP4 协议）。 然后，实时事件会接受无需进一步处理的传入视频流。 此类传递实时事件适用于长时间运行的实时事件或 24x365 线性实时传送视频流。 创建此类实时事件时，请指定 None (LiveEventEncodingType.None)。

发送的贡献源的最高分辨率可为 4K，帧速率可为 60 帧/秒，采用 H.264/AVC 或 H.265/HEVC 视频编解码器，以及 AAC（AAC-LC、HE-AACv1 或 HE-AACv2）音频编解码器。  有关详细信息，请参阅[实时事件类型比较](live-event-types-comparison.md)一文。

> [!NOTE]
> 当需要长时间处理多个事件，并且已经在本地编码器上进行了投入时，则可使用直通这种最经济的方法来实时传送视频流。 请参阅 [定价](https://azure.microsoft.com/pricing/details/media-services/) 详细信息。
> 

请参阅 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126) 中的 .NET 代码示例。

### <a name="live-encoding"></a>实时编码  

![实时编码](./media/live-streaming/live-encoding.svg)

将实时编码与媒体服务配合使用时，需配置本地实时编码器，以便将单比特率视频作为贡献源发送到实时事件（使用 RTMP 或分段 MP4 协议）。 然后，您设置实时事件，以便将编码该传入的单比特率流式传输到[多个比特率视频流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，并使输出可供交付来播放设备通过协议，如 MPEG DASH、 HLS 和平滑流式处理。

如果你使用实时编码，可以发送贡献源仅在解决方法，到 30 帧的/第二，替换 H.264/AVC 视频编解码器和 AAC 帧速率在分辨率为 1080p （AAC LC、 HE-AACv1 或 He-aacv2） 音频编解码器。 请注意传递实时事件，可以支持分辨率不超过 4 K 在 60 帧/秒。 有关详细信息，请参阅[实时事件类型比较](live-event-types-comparison.md)一文。

分辨率和比特率从实时编码器输出中包含由预设确定。 如果使用**标准**实时编码器 (LiveEventEncodingType.Standard)，则*Default720p*预设指定一组 6 解析/位率对，将从在到 200 kbps 192 p 3.5Mbps 720p。 否则为如果使用**Premium1080p**实时编码器 (LiveEventEncodingType.Premium1080p)，则*Default1080p*预设指定一组 6 解析/位率对，将从在 3.5Mbps 1080p到 200 kbps 的 180 p。 有关信息，请参阅[系统预设](live-event-types-comparison.md#system-presets)。

> [!NOTE]
> 如果需要自定义实时编码预设，请打开支持票证，通过 Azure 门户。 你应当指定所需的分辨率和比特率的表。 不要验证只有一个图层在 （如果请求的标准的实时编码器的预设值） 720p 或 1080p （如果请求 Premium1080p 实时编码器的预设值），并最多 6 层。

## <a name="live-event-creation-options"></a>实时事件创建选项

创建实时事件时，可以指定以下选项：

* 实时事件的流式处理协议（目前支持 RTMP 和平滑流式处理协议）。<br/>运行实时事件或其关联的实时输出时，无法更改协议选项。 如果需要其他协议，应当为每个流式处理协议创建单独的实时事件。  
* 对引入和预览的 IP 限制。 可定义允许向该实时事件引入视频的 IP 地址。 允许的 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”）或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.252.0)”）。<br/>如果未指定 IP 地址并且没有规则定义，则不会允许任何 IP 地址。 若要允许任何 IP 地址，请创建一个规则并设置 0.0.0.0/0。<br/>IP 地址必须采用以下格式之一：具有 4 个数字、CIDR 地址范围的 IpV4 地址。
* 创建事件时，可以将其启动方式指定为自动启动。 <br/>如果将 autostart 设置为 true，则实时事件会在创建后启动。 只要实时事件开始运行，就会开始计费。 必须显式对实时事件资源调用停止操作才能停止进一步计费。 或者，可以在准备好开始流式传输后，启动事件。 

    有关详细信息，请参阅[实时事件状态和计费](live-event-states-billing.md)。

## <a name="live-event-ingest-urls"></a>实时事件引入 URL

创建实时事件后，可以获得要提供给实时本地编码器的引入 URL。 实时编码器使用这些 URL 来输入实时流。 有关详细信息，请参阅[建议的本地实时编码器](recommended-on-premises-live-encoders.md)。 

可以使用非虚 URL 或虚 URL。 

> [!NOTE] 
> 要使引入 URL 具有预测性，请设置“vanity”模式。

* 非虚 URL

    非虚 URL 是媒体服务 v3 中的默认模式。 可以快速获取实时事件，但只有在实时事件启动后，才会知道引入 URL。 如果停止/启动实时事件，此 URL 会更改。 <br/>非虚 URL 适用于这样的情况：最终用户希望使用应用进行流式处理，而应用希望尽快获取实时事件，并且可以使用动态引入 URL。
    
    如果客户端应用程序不需要预生成 Live 事件之前引入 URL 创建，只是让媒体服务自动生成的实时事件的访问令牌。
* 虚 URL

    对于使用硬件广播编码器且不希望在启动实时事件时重新配置其编码器的大型媒体广播者来说，虚模式是首选。 他们需要不随时间而改变的预测性引入 URL。
    
    若要指定此模式下，您可以设置`vanityUrl`到`true`在创建时 (默认值是`false`)。 您还需要将你自己的访问令牌 (`LiveEventInput.accessToken`) 可以在创建时。 指定令牌值，以避免在 URL 中的随机令牌。 访问令牌必须是有效的 GUID 字符串 （有或没有连字符）。 不能更新模式设置。

    访问令牌需要在数据中心保持唯一。 如果你的应用程序需要使用虚构的 URL，建议始终创建访问令牌 （而不是重复使用现有的任何 GUID） 的新 GUID 实例。 

    使用以下 Api 来启用虚 URL 和访问令牌设置为有效的 GUID (例如`"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`)。  
    
    |语言|启用虚 URL|设置访问令牌|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>实时引入 URL 命名规则

* 下面的随机  字符串是一个 128 位的十六进制数字（由 32 个 0-9 a-f 字符组成）。
* *你的访问令牌*-使用虚模式时设置的有效 GUID 字符串。 例如， `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` 。
* *流名称*-指示特定连接的流名称。 您使用的实时编码器通常添加流名称值。 可以配置实时编码器，以便使用任何名称来描述该连接，例如:"video1_audio1"，"video2_audio1"，"stream"。

#### <a name="non-vanity-url"></a>非虚 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>平滑流

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>虚 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>平滑流

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>实时事件预览 URL

实时事件开始后接收贡献源，可以使用其预览终结点以预览和验证进一步发布之前接收实时流。 检查预览流是很好后，可以使用实时事件，使实时流可供通过一个或多个 （预先创建） 流式处理终结点传递。 若要实现此目的，创建一个新[Live 输出](https://docs.microsoft.com/rest/api/media/liveoutputs)上实时事件。 

> [!IMPORTANT]
> 确保视频流向预览 URL，然后再继续操作！

## <a name="live-event-long-running-operations"></a>实时事件长时间运行的操作

有关详细信息，请参阅[长时间运行的操作](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>实时输出

将流传输到实时事件后，可以通过创建[资产](https://docs.microsoft.com/rest/api/media/assets)、[实时输出](https://docs.microsoft.com/rest/api/media/liveoutputs)和[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)来启动流式传输事件。 实时输出会存档流，并使观看者可通过[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)使用该流。  

> [!NOTE]
> 实时输出在创建时启动，在删除后停止。 删除实时输出不会删除基础资产和该资产中的内容。 

之间的关系**Live 事件**并将其**Live 输出**是类似于传统电视广播，其中频道 （Live 事件） 表示恒定的视频和录制 （实时流输出） 的特定时间段 （例如，晚上新闻从下午 6:30 到晚上 7:00） 作用域。 您可以录制电视节目使用数字视频录像机 (DVR) – 通过管理 Live 事件中的等效功能**archiveWindowLength**属性。 它是一个 ISO-8601 时间跨度持续时间（例如 PTHH:MM:SS），指定 DVR 的容量，最小可设置为 3 分钟，最大为 25 小时。

Live 输出对象就像磁带记录器，它会捕获和记录实时流式传输到你的 Media Services 帐户中的资产。 记录的内容将保存到你的帐户上附加的 Azure 存储帐户，并保存到资产资源定义的容器。 Live 输出还可以控制传出的实时流，例如存档录制 （例如，容量的云 DVR） 中保留多少流，该值指示查看器可以开始观看实时流的某些属性。 在磁盘上的存档是循环的存档文件"窗口"，仅保留 Live 输出的 archiveWindowLength 属性中指定的内容量。 超出此窗口的内容将自动从存储容器中丢弃，且不可恢复。 可以使用不同的存档长度和设置 Live 事件上创建多个实时输出 （最多三个最大值）。  

如果已发布实时输出**资产**使用**流式处理定位符**，实时事件 （最多 DVR 时段长度） 将继续流式处理定位符的过期或删除操作，直到可以查看具体取决于第一个。

有关详细信息，请参阅[使用云 DVR](live-event-cloud-dvr.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

[实时传送视频流教程](stream-live-tutorial-with-api.md)

---
title: IoT Edge Faq 上的实时视频分析-Azure
description: 本主题提供有关 IoT Edge 常见问题的实时视频分析的答案。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 0a6c1c0f26116227454fa0968264644ea7a43178
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260730"
---
# <a name="frequently-asked-questions-faqs"></a>常见问题 (FAQ)

本主题提供有关 IoT Edge 常见问题的实时视频分析的答案。

## <a name="general"></a>常规

图形拓扑定义中可以使用哪些系统变量？

|变量   |说明|
|---|---|
|[System.object](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|表示时间上的一刻，通常以日期和当天的时间表示。|
|GraphTopologyName   |表示 media graph 拓扑，它包含图形的蓝图。|
|GraphInstanceName|  表示 media graph 实例，保留参数值并引用拓扑。|

## <a name="configuration-and-deployment"></a>配置和部署

是否可以将媒体边缘模块部署到 Windows 10 设备？
    * 是的。 请参阅[Windows 10 上 Linux 容器](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/linux-containers)上的文章。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>从 IP 照相机和 RTSP 设置捕获

* 是否需要在设备上使用特殊的 SDK 来发送视频流？
    * 不能。 IoT Edge 上的实时视频分析支持使用 RTSP 视频流式处理协议（在大多数 IP 摄像机上支持）捕获媒体。
* 是否可以使用 RTMP 或平滑（如媒体服务实时事件）将媒体推送到 IoT Edge 上的实时视频分析？
    * 不能。 LVA 仅支持通过 IP 照相机捕获视频。
    * 支持通过 TCP/HTTP 的 RTSP 流式处理的任何照相机都应正常工作。 
* 是否可以在图形实例上重置或更新 RTSP 源 URL？
    * 是，当图形实例处于非活动状态时。  
* 是否有可在测试和开发过程中使用的 RTSP 模拟器？
    * 是的。 "快速入门" 和 "教程" 中提供了一个[RTSP 模拟器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)边缘模块来支持学习过程。 此模块是我们尽最大努力提供的，可能并非随时都可用。 强烈建议不要将其用于几个小时。 在制定生产部署计划之前，应投入实际的 RTSP 源进行测试。
* 你们是否支持 ONVIF 在边缘发现 IP 照相机？
    * 否，不支持 ONVIF 在边缘发现设备。

## <a name="streaming-and-playback"></a>流式处理和播放

* 是否可以使用媒体服务流式处理技术（例如 HLS 或破折号）播放从边缘记录到 AMS 的资产？
    * 是的。 记录的资产可以像 Azure 媒体服务中的任何其他资产那样进行流式处理。 要流式传输内容，你必须创建一个流式处理终结点，并且该终结点处于 "正在运行" 状态。 使用标准流式处理定位符创建过程，可以访问 HLS 或短划线清单，以便将其流式传输到任何支持的播放器框架。 有关创建发布 HLS 或短划线清单的详细信息，请参阅[动态打包](../latest/dynamic-packaging-overview.md)。
* 能否在存档资产上使用媒体服务的标准内容保护和 DRM 功能？
    * 是的。 所有标准动态加密内容保护和 DRM 功能都可用于从 media graph 记录的资产。
* 我可以使用哪些播放机查看记录的资产中的内容？
   * 支持符合 Apple HTTP Live Streaming （HLS）版本3或版本4的所有标准播放机。 此外，还支持任何能够兼容 MPEG 短线的播放机。
    建议用于测试的播放器包括：

    * [Azure Media Player](../latest/use-azure-media-player.md)
    * [HLS.js](https://hls-js.netlify.app/demo/)
    * [Video.js](https://videojs.com/)
    * [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
    * [Shaka 播放器](https://github.com/google/shaka-player)
    * [ExoPlayer](https://github.com/google/ExoPlayer)
    * [Apple native HTTP Live Streaming](https://developer.apple.com/streaming/)
    * HTML5 视频播放器中内置的边缘、Chrome 或 Safari
    * 支持 HLS 或短划线播放的商业播放机
* 对 media graph 资产进行流式处理的限制是什么？
    * 从媒体图形流式传输实时或记录的资产使用相同的高缩放基础结构和流式处理终结点，媒体服务支持媒体 & 娱乐、OTT 和广播客户的点播和实时流式处理。 这意味着，你可以快速轻松地启用 Azure CDN、Verizon 或 Akamai，以将你的内容交付给几个观众，或最多百万个观众，具体取决于你的方案。

    可以使用 Apple HTTP Live Streaming （HLS）或 MPEG 破折号传递内容。

## <a name="monitoring-and-metrics"></a>监视和度量值

* 能否使用事件网格在边缘上监视媒体图形？
    * 不能。 当前不支持事件网格。
* 是否可以使用 Azure Monitor 查看云中或边缘的媒体图形的运行状况、指标和性能？
    * 不能。
* 是否有任何工具可以更轻松地监视 Media Services IoT Edge 模块？
    * Visual Studio Code 支持 "Azure IoT Tools" 扩展，使你能够轻松监视 LVAEdge 模块终结点。 可以使用此工具快速开始监视 "events" 的 IoT 中心内置终结点，并查看从边缘设备路由到云的推理消息。 

    此外，还可以使用此扩展编辑 LVAEdge 模块的模块克隆，以修改 media graph 设置。

有关详细信息，请参阅[监视和日志记录](monitoring-logging.md)一文。

## <a name="billing-and-availability"></a>计费和可用性

* LiveVideo Analytics 如何 IoT Edge 计费？
    * 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="next-steps"></a>后续步骤

[快速入门：入门 - IoT Edge 上的实时视频分析](get-started-detect-motion-emit-events-quickstart.md)

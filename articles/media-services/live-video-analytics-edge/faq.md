---
title: IoT Edge 上的实时视频分析常见问题解答 - Azure
description: 本主题解答了有关 IoT Edge 上的实时视频分析的常见问题。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011509"
---
# <a name="frequently-asked-questions-faqs"></a>常见问题 (FAQ)

本主题解答了有关 IoT Edge 上的实时视频分析的常见问题。

## <a name="general"></a>常规

图形拓扑定义中可以使用哪些系统变量？

|变量   |说明|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|表示某个时刻，通常以日期和当天的时间表示。|
|System.GraphTopologyName   |表示媒体图形拓扑，保存图形的蓝图。|
|System.GraphInstanceName|  表示媒体图形实例，保存参数值并引用拓扑。|

## <a name="configuration-and-deployment"></a>配置和部署

是否可以将媒体边缘模块部署到 Windows 10 设备？
    * 是的。 请参阅有关 [Windows 10 上的 Linux 容器](/virtualization/windowscontainers/deploy-containers/linux-containers) 的文章。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>从 IP 相机和 RTSP 设置捕获

* 是否需要在设备上使用特殊的 SDK 来发送视频流？
    * 否。 IoT Edge 上的实时视频分析支持使用 RTSP 视频流式处理协议（大多数 IP 相机均支持此协议）来捕获媒体。
* 是否可以使用 RTMP 或 Smooth（如媒体服务实时事件）将媒体推送到 IoT Edge 上的实时视频分析？
    * 否。 LVA 仅支持使用 RTSP 从 IP 相机捕获视频。
    * 任何支持通过 TCP/HTTP 进行 RTSP 流式处理的相机都应正常工作。 
* 是否可以在图形实例上重置或更新 RTSP 源 URL？
    * 可以，在图形实例处于非活动状态时即可进行重置或更新。  
* 是否有可以在测试和开发期间使用的 RTSP 模拟器？
    * 是的。 可以在快速入门和教程中使用 [RTSP 模拟器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)边缘模块来助力学习过程。 此模块是我们尽最大努力提供的，可能并非随时都可用。 强烈建议使用时间不要超过几个小时。 在制定生产部署计划之前，应投入实际的 RTSP 源进行测试。
* 你们是否支持 ONVIF 在边缘发现 IP 照相机？
    * 否，不支持 ONVIF 在边缘发现设备。

## <a name="streaming-and-playback"></a>流式处理和播放

* 是否可以使用媒体服务流式处理技术（例如 HLS 或 DASH）播放从边缘录制到 AMS 的资产？
    * 是的。 可以像处理 Azure 媒体服务中的任何其他资产一样，对录制的资产进行流式处理。 要流式处理内容，必须创建流式处理终结点，并使其处于“正在运行”状态。 通过使用标准流式处理定位符创建过程，你可以访问 HLS 或 DASH 清单，以便将其流式传输到任何支持的播放器框架。 有关创建发布 HLS 或 DASH 清单的详细信息，请参阅[动态打包](../latest/dynamic-packaging-overview.md)。
* 是否可以在存档资产上使用媒体服务的标准内容保护和 DRM 功能？
    * 是的。 所有标准动态加密内容保护和 DRM 功能都可用于从媒体图形录制的资产。
* 我可以使用哪些播放器来查看录制的资产中的内容？
   * 支持兼容的 Apple HTTP Live Streaming (HLS) 版本 3 或版本 4 的所有标准播放器均受支持。 此外，还支持兼容 MPEG-DASH 播放的任何播放器。
    建议用于测试的播放器包括：

    * [Azure Media Player](../latest/use-azure-media-player.md)
    * [HLS.js](https://hls-js.netlify.app/demo/)
    * [Video.js](https://videojs.com/)
    * [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
    * [Shaka Player](https://github.com/google/shaka-player)
    * [ExoPlayer](https://github.com/google/ExoPlayer)
    * [Apple 本机 HTTP Live Streaming](https://developer.apple.com/streaming/)
    * HTML5 视频播放器中内置的 Microsoft Edge、Chrome 或 Safari
    * 支持 HLS 或 DASH 播放的商业播放器
* 对媒体图形资产进行流式处理存在哪些限制？
    * 从媒体图形流式处理实时资产或录制资产会使用大规模基础结构和流式处理终结点，媒体服务支持相同的大规模基础结构和流式处理终结点，以便对媒体和娱乐、OTT 以及广播客户进行按需和实时流式处理。 这意味着你可以快速轻松地启用 Azure CDN、Verizon 或 Akamai，以将你的内容传递给受众，受众规模可以少至几个观众，也可多达数百万个观众，具体取决于你的场景。

    可以使用 Apple HTTP Live Streaming (HLS) 或 MPEG-DASH 来传递内容。

## <a name="monitoring-and-metrics"></a>监视和指标

* 是否可以使用事件网格监视边缘上的媒体图形？
    * 否。 目前不支持事件网格。
* 是否可以使用 Azure Monitor 查看云端或边缘的媒体图形的运行状况、指标和性能？
    * 否。
* 是否有任何工具可用于简化监视媒体服务 IoT Edge 模块？
    * Visual Studio Code 支持“Azure IoT Tools”扩展，该扩展有助于轻松监视 LVAEdge 模块终结点。 可以使用此工具快速开始监视“事件”的 IoT 中心内置终结点，并查看从边缘设备路由到云的推理消息。 

    此外，还可以使用此扩展编辑 LVAEdge 模块的模块孪生，以修改媒体图形设置。

有关详细信息，请参阅[监视和日志记录](monitoring-logging.md)一文。

## <a name="billing-and-availability"></a>计费和可用性

* IoT Edge 上的实时视频分析如何计费？
    * 请参阅[定价页面](https://azure.microsoft.com/pricing/details/media-services/)了解详细信息。

## <a name="next-steps"></a>后续步骤

[快速入门：入门 - IoT Edge 上的实时视频分析](get-started-detect-motion-emit-events-quickstart.md)

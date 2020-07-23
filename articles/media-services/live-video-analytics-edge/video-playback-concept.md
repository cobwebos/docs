---
title: 视频播放-Azure
description: 占位符
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 1fc65a00b2aa1e82c5585583ee9e0ccb97e5168f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260170"
---
# <a name="video-playback"></a>视频播放 

## <a name="suggested-pre-reading"></a>建议的读前准备 

* [IoT Edge 上的实时视频分析概述](overview.md)
* [IoT Edge 上的实时视频分析术语](terminology.md)
* [媒体图概念](media-graph-concept.md)

## <a name="overview"></a>概述  

可以使用[media](media-graph-concept.md) graph 将视频记录到 Azure 媒体服务[资产](terminology.md#asset)中。 在本文档中，可以了解使用 Azure 媒体服务的现有流功能来播放资产所需执行的步骤。

## <a name="streaming-endpoint"></a>流式处理终结点 

可以使用 Azure 媒体服务通过基于 HTTP 的行业标准媒体流式处理协议（例如 HTTP Live Streaming （HLS）和 MPEG 破折号）将资产[流式传输](terminology.md#streaming)到视频播放机。 此媒体从录制内容到流格式的转换由[流式处理终结点](../latest/streaming-endpoint-concept.md)（需要在 Azure 媒体服务帐户中预配的资源）处理。

## <a name="streaming-policy"></a>流式处理策略 

Azure 媒体服务提供了不同的方法来保护视频流，如[通过媒体服务动态加密保护内容](../latest/content-protection-overview.md)一文中所述。 在高级别中，内容保护选项包括：

* **明文流式处理**–流式处理期间不应用加密的位置。
* **使用高级加密标准（AES-128）** -并实现一种方法，用于传递密钥以便仅将视频解密到经过身份验证的查看者。
* **使用数字 Rights Management （DRM）系统**–控制视频在强制实施这些策略的设备的使用、修改和传递。

若要实现内容保护，你可以在媒体服务帐户中定义和创建[流式处理策略](../latest/streaming-policy-concept.md)，并将其用于流式传输所有资产（假设所有流都具有相同的安全要求）。 你还可以使用任何预定义的策略（例如 Predefined_ClearStreamingOnly）。

## <a name="streaming-locator"></a>流式处理定位符  

在媒体服务帐户中启动了流式处理终结点，并定义了流策略后，可以通过 HLS 或短划线协议继续从资产流式传输录制的媒体。 Web 播放机和移动应用需要指向该 HLS 或短线流的 URL。 可以使用[流式处理定位符](../latest/streaming-locators-concept.md)生成此 URL。 如本文中所述，在[创建流式处理定位符和生成 url](../latest/create-streaming-locator-build-url.md)示例中所述，流式处理 URL 是从流式处理终结点、流策略和流式处理定位符组成的。

## <a name="content-recorded-using-file-sink"></a>使用文件接收器录制的内容  

如[media graph 文件接收器](media-graph-concept.md#file-sink)中所述，您可以使用 media graph 将视频录制到使用 media graph 中的文件接收器的边缘设备的本地文件系统。 文件[接收器生成可](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4)生成的文件，可以使用 HTML5 [ &lt; 视频 &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video)元素播放此类内容。 

## <a name="next-steps"></a>后续步骤

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->

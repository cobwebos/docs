---
title: 视频播放 - Azure
description: 占位符
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 17b1f93c18dfb013916c4c0d3756fb97a73e2675
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87042967"
---
# <a name="video-playback"></a>视频播放 

## <a name="suggested-pre-reading"></a>建议的读前准备 

* [IoT Edge 上的实时视频分析概述](overview.md)
* [IoT Edge 上的实时视频分析术语](terminology.md)
* [媒体图概念](media-graph-concept.md)

## <a name="overview"></a>概述  

可以使用[媒体图](media-graph-concept.md)将视频记录到 Azure 媒体服务[资产](terminology.md#asset)中。 在本文档中，你可以了解使用 Azure 媒体服务的现有流式处理功能播放资产所需采取的步骤。

## <a name="streaming-endpoint"></a>流式处理终结点 

你可以使用 Azure 媒体服务通过行业标准的基于 HTTP 的媒体流式处理协议（例如 HTTP Live Streaming (HLS) 和 MPEG-DASH）将资产[流式传输](terminology.md#streaming)到视频播放器。 媒体从录制内容到流式处理格式的这种转换由[流式处理终结点](../latest/streaming-endpoint-concept.md)处理，这是你需要在 Azure 媒体服务帐户中提供的资源。

## <a name="streaming-policy"></a>流式处理策略 

Azure 媒体服务为你提供了多种方法来保护视频流，如[使用媒体服务动态加密保护内容](../latest/content-protection-overview.md)一文中所述。 内容保护的选项大致包括：

* 明文流式处理 - 流式处理期间不应用加密。
* 使用高级加密标准 (AES-128) - 并实现一种方法，将仅用于解密视频的密钥传递给经过身份验证的观看者。
* 使用数字版权管理 (DRM) 系统 - 控制实施这些策略的设备对视频的使用、修改和交付。

若要实现内容保护，你可以在媒体服务帐户中定义并创建[流式处理策略](../latest/streaming-policy-concept.md)，并将其用于流式处理所有资产（假设所有流都具有相同的安全性要求）。 你也可以使用任何预定义的策略（例如 Predefined_ClearStreamingOnly）。

## <a name="streaming-locator"></a>流式处理定位符  

在媒体服务帐户中启动流式处理终结点并定义流式处理策略后，可以继续通过 HLS 或 DASH 协议从资产中流式传输录制的媒体。 Web 播放器和移动应用需要指向该 HLS 或 DASH 流的 URL。 你可以使用[流式处理定位符](../latest/streaming-locators-concept.md)生成此 URL。 如本文中所述以及[创建流式处理定位符并生成 URL](../latest/create-streaming-locator-build-url.md) 示例中显示，流式处理 URL 由流式处理终结点、流式处理策略和流式处理定位符组成。

## <a name="content-recorded-using-file-sink"></a>使用文件接收器记录的内容  

如[媒体图文件接收器](media-graph-concept.md#file-sink)中所述，你可以通过媒体图中的文件接收器使用媒体图将视频录制到边缘设备的本地文件系统中。 文件接收器生成 [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) 文件，可以使用 HTML5 [&lt;video&gt;](https://developer.mozilla.org/docs/Web/HTML/Element/video) 元素来播放此类内容。 

## <a name="next-steps"></a>后续步骤

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->

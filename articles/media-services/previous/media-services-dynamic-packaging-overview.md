---
title: Azure 媒体服务动态打包概述 | Microsoft 文档
description: 本文概述 Microsoft Azure 媒体服务动态打包。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 079094965775c140c0343da98e40fd008995d45a
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901184"
---
# <a name="dynamic-packaging"></a>动态打包

> [!div class="op_single_selector" title1="选择你使用的媒体服务的版本："]
> * [第 3 版](../latest/dynamic-packaging-overview.md)
> * [第 2 版](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 <br/>查看最新版本：[媒体服务 v3](https://docs.microsoft.com/azure/media-services/latest/)。 另请参阅[从 v2 到 v3 的迁移指南](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure 媒体服务可用于向多种客户端技术（例如，iOS、XBOX、Silverlight、Windows 8）传送多种媒体源文件格式、媒体流格式和内容保护格式。 这些客户端可识别不同的协议，例如，iOS 需要 HTTP 实时流 (HLS) V4 格式，Silverlight 和 Xbox 需要平滑流。 如果有一组自适应比特率（多码率）MP4（ISO 基媒体 14496-12）文件或平滑流文件要提供给了解 MPEG DASH、HLS 或平滑流的客户端，则应利用媒体服务动态打包。

使用动态打包时，需要创建一个包含一组自适应比特率文件文件或自适应比特率平滑流式处理文件的资产。 然后，点播流服务器会确保你以选定的协议按清单或分段请求中的指定格式接收流。 因此，只需以单一存储格式存储文件并为其付费，媒体服务服务就会基于客户端的请求构建并提供相应响应。

下图显示传统编码和静态打包工作流。

![静态编码](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

下图显示了动态打包工作流。

![动态编码](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>常见方案

1. 上传一个输入文件（称为夹层文件）。 例如，H.264、MP4 或 WMV（有关受支持格式的列表，请参阅[Media Encoder Standard 支持的格式](media-services-media-encoder-standard-formats.md)）。
2. 将夹层文件编码为 H.264 MP4 自适应比特率集。
3. 通过创建点播定位符来发布包含自适应比特率 MP4 集的资产。
4. 生成用于访问和流式传输内容的流 URL。

## <a name="preparing-assets-for-dynamic-streaming"></a>准备用于动态流式传输的资产

若要为动态流式处理准备资产，可以使用以下选项：

- [上传主文件](media-services-dotnet-upload-files.md)。
- [使用 Media Encoder Standard 编码器生成 H.264 MP4 自适应比特率集](media-services-dotnet-encode-with-media-encoder-standard.md)。
- [流式传输内容](media-services-deliver-content-overview.md)。

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>动态打包支持的音频编解码器

动态打包支持可处理的文件，其中包含通过[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) （AAC-LC、AAC V1、AAC v2）编码的音频、[杜比数字加上](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)（增强的 AC 3 或 E-E-ac3）、杜比 Atmos 或[DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) （DTS EXPRESS、DTS LBR、dts hd、dts hd 无损）。 对杜比 Atmos 内容的流式处理支持标准流格式（CSF）或常见媒体应用程序格式（CMAF）零散的标准，以及通过 CMAF 的 HTTP Live Streaming （HLS）等标准。

> [!NOTE]
> 动态打包不支持包含 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) 音频（它是旧编解码器）的文件。

## <a name="media-services-learning-paths"></a>媒体服务学习路径

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


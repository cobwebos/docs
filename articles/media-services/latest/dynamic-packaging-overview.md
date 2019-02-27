---
title: Azure 媒体服务动态打包概述 | Microsoft 文档
description: 本主题提供媒体服务中的动态打包概述。
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
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: d1d07402bca5f01cf63d0b039c085e46bb0f0d62
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447916"
---
# <a name="dynamic-packaging"></a>动态打包

Microsoft Azure 媒体服务可用于向多种客户端技术（例如，iOS 和 XBOX）传送多种媒体源文件格式、媒体流格式和内容保护格式。 这些客户端可识别不同的协议，例如，iOS 需要 HTTP Live Streaming (HLS) 格式，Xbox 需要平滑流式处理。 如果有一组自适应比特率（多码率）MP4（ISO 基媒体 14496-12）文件或平滑流式处理文件要提供给了解 HLS、MPEG DASH、平滑流式处理的客户端，则应利用媒体服务动态打包。

使用动态打包，只需要创建一个包含一组自适应比特率 MP4 文件的资产。 然后，点播流服务器会确保你以选定的协议按清单或分段请求中的指定格式接收流。 因此，只需以单一存储格式存储文件并为其付费，然后媒体服务服务就会基于客户端的请求构建并提供相应响应。

下图显示传统编码和静态打包工作流。

![静态编码](./media/dynamic-packaging-overview/media-services-static-packaging.png)

下图显示了动态打包工作流。

![动态编码](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="dynamic-packaging-workflow"></a>动态打包工作流

1. 上传一个输入文件（称为夹层文件）。 例如，H.264、MP4 或 WMV（有关受支持格式的列表，请参阅[Media Encoder Standard 支持的格式](media-encoder-standard-formats.md)）。
2. 将夹层文件编码为 H.264 MP4 自适应比特率集。
3. 发布包含自适应比特率 MP4 集的资产。
4. 生成用于访问和流式传输内容的流 URL。

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>动态打包支持的音频编解码器

动态打包支持 MP4 文件，其中包含使用 [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC、HE-AAC v1、HE-AAC v2)、[Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)（增强版 AC-3 或 E-AC3）或 [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)（DTS Express、DTS LBR、DTS HD、DTS HD 无损）编码的音频。

> [!NOTE]
> 动态打包不支持包含 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) 音频（它是旧编解码器）的文件。

## <a name="next-steps"></a>后续步骤

[上传、编码、流式处理视频](stream-files-tutorial-with-api.md)

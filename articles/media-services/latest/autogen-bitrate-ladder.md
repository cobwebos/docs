---
title: 使用 Azure 媒体服务中的标准编码器，通过自动生成的比特率阶梯对视频进行编码 | Microsoft Docs
description: 本主题介绍如何基于输入分辨率和比特率，使用媒体服务中的标准编码器并通过自动生成的比特率阶梯对输入视频进行编码。 不会超过输入分辨率和比特率。 例如，如果输入在 3Mbps 时为 720p，则输出最多会保持 720p，并且会以低于 3Mbps 的速率开始。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6e447c04f4a94f2fb534ecb0605595a90816431e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638290"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>使用自动生成的比特率阶梯进行编码

## <a name="overview"></a>概述

本文介绍了如何基于输入分辨率和比特率，使用媒体服务中的标准编码器将输入视频编码为自动生成的比特率阶梯（比特率-分辨率对）。 此内置编码器设置或预设绝不会超过输入分辨率和比特率。 例如，如果输入在 3 Mbps 时为 720p，则输出最多会保持 720p，并且会以低于 3 Mbps 的速率开始。

### <a name="encoding-for-streaming"></a>编码用于流式处理

在“转换”中使用“AdaptiveStreaming”预设时，会获取适合通过流式处理协议（例如 HLS 和 DASH）传递的输出。 使用此预设时，此服务可智能地确定要生成的视频层数，并确定比特率和分辨率。 输出内容包含 MP4 文件，其中无 AAC 编码的音频和 H.264 编码的视频交错。

若要查看有关如何使用此预设的示例，请参阅[流式传输文件](stream-files-dotnet-quickstart.md)。

## <a name="output"></a>输出

此部分显示媒体服务编码器生成的输出视频层的三个示例，是使用“AdaptiveStreaming”预设进行编码得来的。 在所有情况下，输出包含仅音频 MP4 文件，其中立体声音频以 128 kbps 进行编码。

### <a name="example-1"></a>示例 1
高度为“1080”，帧速率为“29.970”的源生成 6 个视频层：

|层|高度|宽度|比特率 (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>示例 2
高度为“720”，帧速率为“23.970”的源生成 5 个视频层：

|层|高度|宽度|比特率 (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>示例 3
高度为“360”，帧速率为“29.970”的源生成 3 个视频层：

|层|高度|宽度|比特率 (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [流式传输文件](stream-files-dotnet-quickstart.md)

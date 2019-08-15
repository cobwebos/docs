---
title: 使用现有播放器播放内容 - Azure | Microsoft 文档
description: 本主题列出了可以用来播放内容的现有播放器。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0da8ce396ce07b7c9859fcfccb5cb524c0ce21c1
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "69015295"
---
# <a name="playing-your-content-with-existing-players"></a>使用现有播放器播放内容
Azure 媒体服务支持多种常用的流式处理格式，如平滑流、HTTP 实时流和 MPEG-Dash。 本主题列出了可用于测试流的现有播放器。

### <a name="the-azure-portal-media-services-content-player"></a>Azure 门户媒体服务内容播放器
**Azure** 门户提供可用于测试视频的内容播放器。

单击所需的视频（确保它[已发布](media-services-portal-publish.md)），并单击门户底部的“播放”按钮。

请注意以下事项：

* **媒体服务内容播放器**从默认的流式处理终结点播放。 如果要从非默认流式处理终结点播放，请使用其他播放器。 例如 [Azure Media Player](https://aka.ms/azuremediaplayer)。

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

使用 [Azure Media Player](https://aka.ms/azuremediaplayer)按以下列任意格式播放内容（清除或受保护）：

* 平滑流式处理
* MPEG DASH
* HLS
* 渐进式 MP4

### <a name="flash-player"></a>Flash Player

#### <a name="aes-encrypted-with-token"></a>带令牌的 AES 加密

[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

#### <a name="playready-with-token"></a>带令牌的 PlayReady

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH 播放器

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>其他
若要测试 HLS URL，还可以使用：

* iOS 设备上的“Safari”或
* Windows 上的“3ivx HLS 播放器”。

## <a name="developing-video-players"></a>开发视频播放器
有关如何开发自己的播放器的信息，请参阅[开发视频播放器](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png

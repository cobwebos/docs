---
title: 使用现有播放器播放内容 - Azure | Microsoft 文档
description: 本主题列出了可以用来播放内容的现有播放器。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako
ms.openlocfilehash: 48f373b013b1192c353352b801876d706d91dd28
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23030512"
---
# <a name="playing-your-content-with-existing-players"></a>使用现有播放器播放内容
Azure 媒体服务支持多种常用的流式处理格式，如平滑流、HTTP 实时流和 MPEG-Dash。 本主题指出了可用来测试流的现有播放器。

### <a name="the-azure-portal-media-services-content-player"></a>Azure 门户媒体服务内容播放器
**Azure** 门户提供可用于测试视频的内容播放器。

单击所需的视频（确保它[已发布](media-services-portal-publish.md)），并单击门户底部的“播放”按钮。

请注意以下事项：

* **媒体服务内容播放器**从默认的流式处理终结点播放。 如果要从非默认流式处理终结点播放，请使用其他播放器。 例如 [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
使用 [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)按以下列任意格式播放内容（清除或受保护）：

* 平滑流
* MPEG DASH
* HLS
* 渐进式 MP4

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>带令牌的 AES 加密
[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Silverlight 播放器
#### <a name="monitoring"></a>监视
[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### <a name="playready-with-token"></a>带令牌的 PlayReady
[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH 播放器
[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>其他
若要测试 HLS URL，还可以使用：

* iOS 设备上的“Safari”或
* Windows 上的“3ivx HLS 播放器”。

## <a name="developing-video-players"></a>开发视频播放器
有关如何开发自己的播放器的信息，请参阅[开发视频播放器](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png

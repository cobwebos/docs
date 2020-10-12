---
title: 如何将 Shaka player 用于 Azure 媒体服务
description: 本文介绍如何在 Azure 媒体服务中使用 Shaka 播放器
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: e4be3d4508d342c77507cf824fb036e32084b617
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329731"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>如何将 Shaka player 用于 Azure 媒体服务

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>概述

Shaka Player 是自适应媒体的开源 JavaScript 库。 它在浏览器中播放自适应媒体格式 (如虚线和 HLS) ，而无需使用插件或 Flash。 相反，Shaka Player 使用开放 web 标准媒体源扩展和加密媒体扩展。

我们建议使用 [Mux.js](https://github.com/videojs/mux.js/) ，而不是它，Shaka player 将支持 HLS CMAF 格式，但不支持 HLS TS。

可在 [Shaka player 文档](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html)中找到其官方文档。

## <a name="sample-code"></a>示例代码
本文中的示例代码可在 [Azure 示例/3rdparty-示例](https://github.com/Azure-Samples/media-services-3rdparty-player-samples)中找到。

## <a name="implementing-the-player"></a>实现播放器

如果需要实现自己的播放机实例，请按照以下说明操作：

1. 创建一个 `index.html` 将在其中承载播放机的文件。 添加以下代码行 (可以将版本替换为较新版本（如果适用）) ：

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. 添加包含以下代码的 JavaScript 文件：

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. 将替换 `manifestUrl` 为资产流式处理定位符中的 HLS 或短线 URL，该 URL 可在 Azure 门户的 "流式处理定位符" 页中找到。
    ![流式处理定位符 Url](media/how-to-shaka-player/streaming-urls.png)

1. 使用) 运行服务器 (例如 `npm http-server` ，播放机应正在运行 .。。

## <a name="set-up-captions"></a>设置标题

### <a name="set-up-vod-captions"></a>设置 VOD 标题

运行以下代码行，并将替换 `captionUrl` 为您的. vtt 目录 (vtt 文件必须位于同一主机中，以避免 CORS 错误) ， `lang` 用两个字母代码作为语言，同时 `type` 包含 `caption` 或 `subtitle` ：

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>设置实时流标题

实时流中的启用字幕已配置添加以下代码行：

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>设置令牌身份验证

运行以下代码行，并 `token` 将替换为令牌字符串：

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>设置 AES-128 加密

Shaka Player 目前不支持 AES-128 加密。

指向 GitHub [问题](https://github.com/google/shaka-player/issues/850) 的链接，以遵循此功能的状态。

## <a name="set-up-drm-protection"></a>设置 DRM 保护

Shaka Player 使用加密媒体扩展 (EME) ，这需要使用安全 URL 才能使用。 因此，在测试任何受 DRM 保护的内容时，必须使用 https。 如果站点使用 https，则清单和每个段还需要使用 https。 这是因为混合内容要求。

 (的 URL 管理) 其许可证服务器 () 的 URL 的优先顺序：

1. 用于调试的 ClearKey config 应覆盖其他所有内容。  (应用程序仍可以指定 ClearKey 许可证服务器。 ) 
2. 如果存在应用程序配置的服务器（如果有），则应覆盖清单中的任何内容。
3. 仅当未指定其他内容时，才使用清单提供的许可证服务器。

若要为 Widevine 或 PlayReady 指定许可证服务器 URL，我们可以使用以下代码：

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

所有 FairPlay 内容都需要设置服务器证书。 它在播放机配置中设置：

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

有关详细信息，请参阅 [Shaka PLAYER DRM protection 文档](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html)。

## <a name="next-steps"></a>后续步骤

* [使用 Azure Media Player](../azure-media-player/azure-media-player-overview.md)
* [快速入门：加密内容](encrypt-content-quickstart.md)

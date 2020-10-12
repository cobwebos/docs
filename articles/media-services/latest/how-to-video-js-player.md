---
title: 如何在 Azure 媒体服务中使用 Video.js 播放器
description: 本文介绍如何将 HTML 视频对象和 JavaScript 用于 Azure 媒体服务
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
ms.openlocfilehash: 99e17db99842546b7f595d62f82f8bacb0ee6a8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329697"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>如何在 Azure 媒体服务中使用 Video.js 播放器

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>概述

Video.js 是为 HTML5 世界打造的 web 视频播放器。 它在浏览器中播放自适应媒体格式 (如虚线和 HLS) ，而无需使用插件或 Flash。 Video.js 使用开放 web 标准 MediaSource 扩展和加密媒体扩展。 此外，它还支持在桌面和移动设备上播放视频。

可在中找到它的官方文档 [https://docs.videojs.com/](https://docs.videojs.com/) 。

## <a name="sample-code"></a>示例代码
本文中的示例代码可在 [Azure 示例/3rdparty-示例](https://github.com/Azure-Samples/media-services-3rdparty-player-samples)中找到。

## <a name="implement-the-player"></a>实现播放器

1. 创建一个 `index.html` 将在其中承载播放机的文件。 添加以下代码行 (可以将版本替换为较新版本（如果适用）) ：

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. 添加 `index.js` 包含以下代码的文件：

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. 将替换 `manifestUrl` 为资产流式处理定位符中的 HLS 或短线 URL，可在 Azure 门户的 "流式处理定位符" 页中找到。
    ![流式处理定位符 Url](media/how-to-shaka-player/streaming-urls.png)

4. `protocolType`将替换为以下选项：

    - HLS 协议的 "application/x-型"
    - 短划线协议的 "应用程序/短线 + xml"

### <a name="set-up-captions"></a>设置标题

运行 `addRemoteTextTrack` 方法，并替换：

- `subtitleKind` 具有 `"captions"` 、、 `"subtitles"` `"descriptions"` 或 `"metadata"`  
- `caption` 对于 vtt 文件路径 (vtt 文件必须位于同一主机中，以避免 CORS 错误) 
- `subtitleLang` 对于语言为 BCP 47 的代码，例如 `"eng"` 英语或 `"es"` 西班牙语
- `subtitleLabel` 具有所需的标题显示名称

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>设置令牌身份验证

必须在请求的标头的授权字段中设置令牌。 为了避免出现 CORS 问题，必须仅在其 URL 中将此标记设置为的请求 `'keydeliver'` 。 下面的代码行应完成以下工作：

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

然后，必须将上述函数附加到 `videojs.Hls.xhr.beforeRequest` 事件。

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>设置 AES-128 加密

Video.js 支持 AES-128 加密，无需任何其他配置。 

> [!NOTE] 
> 目前不能播放加密和 HLS/短划线 CMAF 内容的 [问题](https://github.com/videojs/video.js/issues/6717) 。

### <a name="set-up-drm-protection"></a>设置 DRM 保护

为了支持 DRM 保护，你必须添加 [videojs-contrib-eme](https://github.com/videojs/videojs-contrib-eme) 官方扩展。 CDN 版本也起作用。

1. 在 `index.js` 上面详细的文件中，必须在 `videoJS.eme();` 添加视频源 *之前* 通过添加来初始化 EME 扩展：

   ```javascript
    videoJS.eme();
   ```

2. 现在可以定义 DRM 服务的 Url，并定义相应许可证的 Url，如下所示：

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>获取许可证 URL

为了获取许可证 URL，你可以：

- 请参阅 DRM 提供商配置
- 或者，如果使用的是示例，请在 `output.json` *setup-vod.ps1* 运行 VODs PowerShell 脚本或实时流的 *start-live.ps1* 脚本时，查阅生成的文档。 你还会发现此文件中的孩子。

#### <a name="using-tokenized-drm"></a>使用标记化 DRM

为了支持标记化 DRM 保护，你必须将以下行添加到 `src` 播放器的属性中：

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>后续步骤

- [使用 Azure Media Player](../azure-media-player/azure-media-player-overview.md)  
- [快速入门：加密内容](encrypt-content-quickstart.md)

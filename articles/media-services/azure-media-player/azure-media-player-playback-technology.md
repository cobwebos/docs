---
title: Azure Media Player 播放技术
description: 详细了解用于播放视频或音频的播放技术。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b9772f89f78f21cfb0f2f9baa7c6b9915fbacf37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91275687"
---
# <a name="playback-technology-tech"></a>播放技术（简称“技术”） #

播放技术是指用于播放视频或音频的特定浏览器或插件技术。

- **azureHtml5JS**：将 MSE 和 EME 标准与视频元素结合使用，以无插件方式播放 DASH 内容，支持 Azure 媒体服务提供的 AES-128 位信封加密内容或 DRM 通用加密内容（在浏览器支持的情况下使用 PlayReady 和 Widevine）
- **flashSS**：使用 Flash Player 技术播放平滑内容，支持 Azure 媒体服务提供的 AES-128 位信封解密 - 需要 Flash 版本 11.4 或更高版本
- **html5FairPlayHLS**：通过 HLS 将基于浏览器的播放技术中特有的 Safari 与视频元素配合使用。 此技术是播放 Azure 媒体服务中受 FairPlay 保护的内容所必需的，已于 2016 年 10 月 19 日添加到 techOrder 中
- **silverlightSS**：使用 Silverlight 技术播放平滑内容，支持 Azure 媒体服务提供的受 PlayReady 保护的内容。
- **html5**：将基于浏览器的播放技术与视频元素配合使用。  在 Apple iOS 或 Android 设备上，此技术允许播放 HLS 流，针对 AES-128 位信封加密或 DRM 内容提供了一些基本支持（在浏览器支持的情况下使用 FairPlay）。

## <a name="tech-order"></a>技术顺序 ##

为了确保你的资产可在各种设备上播放，建议使用以下技术顺序：`techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]`。此顺序为默认设置，可以直接在 `<video>` 上设置，也可以通过编程方式在以下选项中设置：

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

或

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>兼容性矩阵 ##

如果对 Azure 媒体服务提供的流式处理内容使用建议的技术顺序，则需遵循以下兼容性播放矩阵的要求

| 浏览器        | OS                                                       | 预期技术 (Clear)  | 预期技术 (AES)  | 预期技术 (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11      | Windows 10、Windows 8.1、Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11IE 9-101  | Windows 7、Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | 不支持                |
| Microsoft Edge           | Xbox One<sup>1</sup>（2015 年 11 月更新）                   | azureHtml5JS           | azureHtml5JS         | 不支持                |
| Chrome 37+     | Windows 10、Windows 8.1、macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47+    | Windows 10、Windows 8.1、macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10、Windows 8.1、macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightSS (PlayReady)    |
| Firefox 35-41  | Windows 10、Windows 8.1                                  | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| Safari         | iOS 6+                                                   | html5                  | html5（无令牌）3    | 不支持                |
| Safari 8+      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Mountain Lion<sup>1</sup>                           | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| Chrome 37+     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37+     | Android 4.02                                             | html5                  | html5（无令牌）<sup>3</sup>    | 不支持                |
| Firefox 42+    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | 不支持                |
| IE 8           | Windows                                                  | 不支持          | 不支持        | 不支持                |

<sup>1</sup> 配置不受支持或未经测试；列为参考，供用来完成相关设置。

<sup>2</sup> 若要在 Android 设备上成功进行播放，需要同时满足设备功能、图形支持、编解码器渲染、OS 支持等方面的要求。 由于 Android 是一个开源平台，允许手机制造商更改 Google 提供的 Vanilla Android OS，因此会导致 Android 空间中出现一些碎片，某些设备可能因为缺少功能而不受支持。 另外，某些 Android 设备并非支持所有编解码器。  

<sup>3</sup> 在不支持令牌的情况下，可以使用代理来添加此功能。 请查看此[博客](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)，详细了解此解决方案。

> [!NOTE]
> 如果所选的预期技术要求安装某个插件（例如 Flash），而用户的计算机上未安装它，则 AMP 会继续在技术列表中检查下一项技术的功能以及源类型和保护信息。 例如，如果尝试在 OS X Yosemite 上的 Safari 8 中观看未受保护的点播流，但未安装 Flash 和 Silverlight，则 AMP 会选择使用本机 Html5 技术进行播放。<br/><br/>新的浏览器技术每天都在涌现，因此可能会影响到此矩阵。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
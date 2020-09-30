---
title: Azure Media Player URL 重写工具
description: Azure Media Player 会重写来自 Azure 媒体服务的给定 URL，以便为 SMOOTH、DASH、HLS v3 和 HLS v4 提供流。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b037eec13fda0b5ec16a4f2f53ad2a64fb5f8da1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275551"
---
# <a name="url-rewriter"></a>URL 重写工具 #

默认情况下，Azure Media Player 会重写来自 Azure 媒体服务的给定 URL，以便为 SMOOTH、DASH、HLS v3 和 HLS v4 提供流。 例如，如果提供了如下所示的源，Azure Media Player 会确保尝试播放所有上述协议：

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

但是，如果不想使用 URL 重写程序，可以通过将 `disableUrlRewriter` 属性添加到参数来实现此目的。 这意味着传递到源的所有信息都将直接传递给播放机而不进行修改。  下面是将两个源添加到播放机的示例：一个为 DASH 流，一个为 SMOOTH 流

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

或

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

此外，可以根据需要使用 `streamingFormats` 参数指定一个特定的流格式，以便 Azure Media Player 将流格式重写为该格式。 选项包括 `DASH`、`SMOOTH`、`HLSv3`、`HLSv4`、`HLS`。 HLS 与 HLSv3 和 v4 之间的区别在于 HLS 格式支持播放 FairPlay 内容。 v3 和 v4 不支持 FairPlay。 如果没有适用于特定协议的传送策略，则这很有用。  下面是没有为资产启用 DASH 协议时的示例。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

或

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

以上两种方法在多种情况下可以根据你的特定资产相互结合使用。

> [!NOTE]
> Widevine 保护信息仅在 DASH 协议上保留。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
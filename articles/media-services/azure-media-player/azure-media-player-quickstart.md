---
title: Azure Media Player 快速入门
description: 了解设置 Azure Media Player 的基本步骤。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727474"
---
# <a name="azure-media-player-quickstart"></a>Azure Media Player 快速入门
Azure Media Player 易于设置。 只需几分钟便可实现 Azure 媒体服务帐户中的媒体内容的基本播放。 本部分展示了基本步骤，未提供详细信息。 后面的部分介绍了有关如何安装和配置 Azure Media Player 的具体信息。  只需要将以下包括项添加到文档的 `<head>`：

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
>  不应当在生产中使用`latest`版本，因为此版本可能会根据需要进行更改。 请将 `latest` 替换为 Azure Media Player 的某个版本；例如将 `latest` 替换为 `1.0.0`。 可以从[此处](azure-media-player-changelog.md)查询 Azure Media Player 版本。

## <a name="use-the-video-element"></a>使用视频元素

接下来，只需照常使用 `<video>` 元素，但要附带包含任何选项的 `data-setup` 特性。 这些选项可以在有效 JSON 对象中包含任何 Azure 媒体服务选项。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

如果不想使用自动安装，可以省略 `data-setup` 特性并手动初始化视频元素。

```html
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
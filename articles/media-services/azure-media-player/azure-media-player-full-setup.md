---
title: Azure Media Player 完全安装
description: 了解如何设置 Azure Media Player。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 15f5918748df80cec01ccf89835a0ef51da64529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296240"
---
# <a name="azure-media-player-full-setup"></a>Azure Media Player 完全设置 #

Azure Media Player 易于设置。 只需几分钟即可直接从 Azure 媒体服务帐户获取媒体内容的基本播放。 版本的示例目录中也提供了[示例](https://github.com/Azure-Samples/azure-media-player-samples)。


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>步骤1：在页面页眉中包含 JavaScript 和 CSS 文件 ##

使用 Azure Media Player，你可以访问 CDN 托管版本中的脚本。 通常建议在结束正文标记之前放置 JavaScript `<body>` ，而不是将其放 `<head>` 在结尾处，但 Azure Media Player 包含 "HTML5 Shiv"，它需要位于旧版 IE 版本的开头，才能将视频标记视为有效元素。

> [!NOTE]
> 如果已在使用 HTML5 shiv （如 [Modernizr](https://modernizr.com/) ），则可以在任意位置包含 Azure Media Player JavaScript。 但请确保 Modernizr 的版本包含视频的 shiv。

### <a name="cdn-version"></a>CDN 版本 ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
>  不应当在生产中使用`latest`版本，因为此版本可能会根据需要进行更改。 替换 `latest` 为 Azure Media Player 的版本。 例如，将 `latest` 替换为 `2.1.1`。 可以从[此处](azure-media-player-changelog.md)查询 Azure Media Player 版本。

> [!NOTE]
> 自 `1.2.0` 发布后，不再需要将位置包括到回退方面 (它会自动从 azuremediaplayer.min.js 文件) 的相对路径中选取位置。 您可以通过在上述脚本后面的中添加以下脚本来修改回退方面的位置 `<head>` 。

> [!NOTE]
> 由于 Flash 和 Silverlight 插件的性质，swf 和 xap 文件应在域中托管，而不包含任何敏感信息或数据，这会自动为你处理 Azure CDN 托管版本。

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>步骤2：将 HTML5 视频标记添加到页面 ##

使用 Azure Media Player，可以使用 HTML5 视频标记嵌入视频。 然后，Azure Media Player 将读取标记并使其在所有浏览器中工作，而不只是支持 HTML5 视频的浏览器。 除了基本标记外，Azure Media Player 需要一些额外的部分。

1. `<data-setup>`上的属性 `<video>` 指示 Azure Media Player 在页面准备就绪时自动设置视频，并从属性读取 JSON) 格式的任何 (。
1. `id`对于同一页面上的每个视频，都应使用属性：。
1. 该 `class` 属性包含两个类：
    - `azuremediaplayer` 应用 Azure Media Player UI 功能所需的样式
    - `amp-default-skin` 将默认外观应用于 HTML5 控件
1. `<source>`包含两个必需的属性
    - `src` 属性可以包括从 Azure 媒体服务中添加的 **. ism/清单* 文件，Azure Media Player 自动向播放机添加破折号、光滑和 HLS 的 url
    - `type` 特性是所需的流 MIME 类型。 与 *". ism/manifest"* 关联的 MIME 类型是 *"application/vnd.apple.mpegurl. vnd.ms-sstr + xml"*
1. 上的 *可选* `<data-setup>` 属性 `<source>` 会告诉 Azure Media Player 是否有来自 Azure 媒体服务的流的任何唯一传递策略（包括但不限于加密类型 (AES 或 PlayReady、Widevine 或 FairPlay) 和令牌）。

包含/排除属性、设置、源和跟踪，与 HTML5 视频完全相同。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

默认情况下，较大的 "播放" 按钮位于左上角，因此它不会涵盖海报中感兴趣的部分。 如果希望将大容量游戏居中，可以向元素添加其他 `amp-big-play-centered` `class` `<video>` 。

### <a name="alternative-setup-for-dynamically-loaded-html"></a>动态加载的 HTML 的替代设置 ###

如果网页或应用程序 (ajax、appendChild 等 ) 动态加载视频标记，使页面加载时可能不存在，则需要手动设置播放机，而不是依赖于数据安装属性。 若要执行此操作，请首先从标记中删除数据安装属性，这样就不会在播放机初始化时产生混淆。 接下来，请在加载 Azure Media Player JavaScript 之后、视频标记加载到 DOM 之后，运行以下 JavaScript。

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
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
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

函数中的第一个参数 `amp` 是视频标记的 ID。 将其替换为你自己的。

第二个参数是选项对象。 它允许你设置其他选项，例如，可以对数据安装属性进行设置。

第三个参数是 "就绪" 回调。 Azure Media Player 初始化后，它将调用此函数。 在准备好的回调中，"this" 对象指的是播放机实例。

你还可以将引用传递给元素本身，而不是使用元素 ID。

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
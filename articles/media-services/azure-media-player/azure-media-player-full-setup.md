---
title: Azure 媒体播放器完整设置
description: 了解如何设置 Azure 媒体播放器。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727237"
---
# <a name="azure-media-player-full-setup"></a>Azure 媒体播放器完整设置 #

Azure Media Player 易于设置。 只需几分钟，就能直接从 Azure 媒体服务帐户获得媒体内容的基本播放。 [示例](https://github.com/Azure-Samples/azure-media-player-samples)也提供在版本的示例目录中。


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>第 1 步：将 JavaScript 和 CSS 文件包含在页面头 ##

使用 Azure 媒体播放器，可以从 CDN 托管版本访问脚本。 现在通常建议将 JavaScript 放在最终正文标记`<body>`之前，而不是`<head>`放在 中，但 Azure 媒体播放器包含一个"HTML5 Shiv"，它需要在旧 IE 版本中处于头脑中，才能将视频标记视为有效元素。

> [!NOTE]
> 如果您已经在使用像[Modernizr](http://modernizr.com/)这样的 HTML5 shiv，则可以在任何地方包括 Azure 媒体播放器 JavaScript。 但是，请确保您的 Modernizr 版本包含视频的 shiv。

### <a name="cdn-version"></a>CDN 版本 ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> 不应在**NOT**`latest`生产中使用版本，因为可能会按需更改。 替换为`latest`Azure 媒体播放器的版本。 例如，将 `latest` 替换为 `2.1.1`。 可以[在此处](azure-media-player-changelog.md)查询 Azure 媒体播放器版本。

> [!NOTE]
> 自`1.2.0`发布以来，不再需要将位置包含到回退技术（它会自动从 azuremediaplayer.min.js 文件的相对路径中选取位置）。 您可以通过在上述脚本`<head>`之后添加以下脚本来修改回退技术的位置。

> [!NOTE]
> 由于 Flash 和 Silverlight 插件的性质，swf 和 xap 文件应托管在没有任何敏感信息或数据的域上 - Azure CDN 托管版本会自动为您保管。

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>第 2 步：向页面添加 HTML5 视频标记 ##

使用 Azure 媒体播放器，可以使用 HTML5 视频标记嵌入视频。 然后，Azure 媒体播放器将读取该标记，使其在所有浏览器中工作，而不仅仅是支持 HTML5 视频的浏览器。 除了基本标记之外，Azure 媒体播放器还需要一些额外的部件。

1. 上`<data-setup>`的属性`<video>`告诉 Azure 媒体播放器在页面准备就绪时自动设置视频，并从属性中读取任何（以 JSON 格式）。
1. 属性`id`：应在同一页面上的每个视频使用和唯一。
1. 该`class`属性包含两个类：
    - `azuremediaplayer`应用 Azure 媒体播放器 UI 功能所需的样式
    - `amp-default-skin`将默认外观应用于 HTML5 控件
1. 包括`<source>`两个必需的属性
    - `src`属性可以包括 Azure 媒体服务中的 **.ism/清单*文件，Azure 媒体播放器会自动向播放器添加 DASH、SMOOTH 和 HLS 的 URL
    - `type`属性是流的所需 MIME 类型。 与 *".ism/清单"* 关联的 MIME 类型是 *"应用程序/vnd.ms-sstr_xml"*
1. 如果*optional*`<data-setup>`Azure 媒体`<source>`服务中的流有任何唯一的传递策略，包括但不限于加密类型（AES 或 PlayReady、Widevine 或 FairPlay）和令牌，则 上的可选属性将告诉 Azure 媒体播放器。

完全按照 HTML5 视频的格式包括/排除属性、设置、源和跟踪。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

默认情况下，大型播放按钮位于左上角，因此不会遮盖海报的有趣部分。 如果您希望将大型播放按钮居中，则可以向`amp-big-play-centered``class``<video>`元素添加其他内容。

### <a name="alternative-setup-for-dynamically-loaded-html"></a>动态加载 HTML 的替代设置 ###

如果您的网页或应用程序动态加载视频标记（ajax、附加儿童等），以便在页面加载时可能不存在，则需要手动设置播放器，而不是依赖数据设置属性。 为此，请先从标记中删除数据设置属性，这样在初始化播放器时不会出现混淆。 接下来，在 Azure 媒体播放器 JavaScript 加载后，并在视频标记加载到 DOM 之后，运行以下 JavaScript。

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

函数中`amp`的第一个参数是视频标记的 ID。 用你自己的替换它。

第二个参数是选项对象。 它允许您设置其他选项，如使用数据设置属性。

第三个参数是"就绪"回调。 Azure 媒体播放器初始化后，它将调用此函数。 在就绪回调中，"此"对象引用播放器实例。

也可以传递对元素本身的引用，而不是使用元素 ID。

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>后续步骤 ##

- [Azure 媒体播放器快速入门](azure-media-player-quickstart.md)
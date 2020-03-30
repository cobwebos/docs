---
title: 在应用中嵌入视频索引器小部件
titleSuffix: Azure Media Services
description: 了解如何在应用中嵌入视频索引器小部件。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: 4d92bd3709c5f56db0095ca1be2caa0e9c78b42f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336821"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>在应用中嵌入视频索引器小部件

本文演示如何在应用中嵌入视频索引器小部件。 视频索引器支持将三种类型的小部件嵌入到你的应用程序中：*认知见解*、*播放器*和*编辑器*。

从版本 2 开始，小部件基本 URL 包括指定帐户的区域。 例如，美国西部区域中的帐户将生成：`https://wus2.videoindexer.ai/embed/insights/...`。

## <a name="widget-types"></a>小组件类型

### <a name="cognitive-insights-widget"></a>认知见解小组件

认知见解小组件包括从视频索引过程中提取的所有视觉见解。 认知见解微件支持以下可选 URL 参数：

|“属性”|定义|描述|
|---|---|---|
|`widgets` | 用逗号分隔的字符串 | 允许您控制要呈现的见解。<br/>示例：`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`仅呈现人和关键字 UI 见解。<br/>可用选项：人物、动画人物、关键字、标签、情感、情感、主题、关键帧、脚本、ocr、扬声器、场景和命名实体。|
|`controls`|用逗号分隔的字符串|允许您控制要呈现的控件。<br/>示例：`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download`仅呈现搜索选项和下载按钮。<br/>可用选项：搜索、下载、预设、语言。|
|`language`|短语言代码（语言名称）|控制见解语言。<br/>示例： `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>或 `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | 短语言代码 | 控制 UI 的语言。 默认值为 `en`。 <br/>示例：`locale=de`。|
|`tab` | 默认选择选项卡 | 控制默认情况下呈现的 **"见解"** 选项卡。 <br/>示例：`tab=timeline`在选择 **"时间轴"** 选项卡时呈现见解。|

### <a name="player-widget"></a>播放器小组件

您可以使用播放机小部件使用自适应比特率流式传输视频。 播放机小部件支持以下可选 URL 参数。

|“属性”|定义|描述|
|---|---|---|
|`t` | 从开始开始的秒数 | 使玩家从指定的时间点开始播放。<br/> 示例：`t=60`。 |
|`captions` | 语言代码 | 在小部件加载期间以指定语言获取标题，以在 **"字幕"** 菜单上可用。<br/> 示例：`captions=en-US`。 |
|`showCaptions` | 布尔值 | 使播放器与已启用的字幕一起加载。<br/> 示例：`showCaptions=true`。 |
|`type`| | 激活音频播放器外观（视频部分被删除）。<br/> 示例：`type=audio`。 |
|`autoplay` | 布尔值 | 指示播放器在加载时是否应开始播放视频。 默认值为 `true`。<br/> 示例：`autoplay=false`。 |
|`language` | 语言代码 | 控制玩家语言。 默认值为 `en-US`。<br/>示例：`language=de-DE`。|

### <a name="editor-widget"></a>编辑器小部件

您可以使用编辑器小部件创建新项目并管理视频的见解。 编辑器小部件支持以下可选 URL 参数。

|“属性”|定义|描述|
|---|---|---|
|`accessToken`<sup>*</sup> | String | 提供对仅用于嵌入小部件的帐户中的视频的访问。<br> 编辑器小部件需要参数`accessToken`。 |
|`language` | 语言代码 | 控制玩家语言。 默认值为 `en-US`。<br/>示例：`language=de-DE`。 |
|`locale` | 短语言代码 | 控制见解语言。 默认值为 `en`。<br/>示例：`language=de`。 |

<sup>*</sup>业主应`accessToken`谨慎。

## <a name="embedding-public-content"></a>嵌入公共内容

1. 登录[视频索引器](https://www.videoindexer.ai/)网站。
2. 选择要使用的视频。
3. 选择视频下方显示的 **"嵌入"** 按钮。

    ![视频索引器中的嵌入按钮](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    选择 **"嵌入"** 按钮后，可以选择要嵌入应用的小部件。
4. 选择您想要的小部件类型（**认知见解**、**播放器**或**编辑器**）。
 
5. 复制嵌入代码，然后将其添加到应用。

    ![嵌入应用程序代码_视频索引器](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> 如果您在共享视频 URL 时出现问题，请将`location`参数添加到链接中。 参数应设置为[存在视频索引器的 Azure 区域](regions.md)。 例如：`https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`。

## <a name="embedding-private-content"></a>嵌入专用内容

要嵌入专用视频，必须在 iframe`src`的属性中传递访问令牌：

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
要获取认知见解小部件内容，请使用以下方法之一：

- [获取见解小工具](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)API。<br/>
- [获取视频访问令牌](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?)。 将其作为查询参数添加到 URL。 指定此 URL`src`作为 iframe 的值，如前面所示。

要在嵌入式小部件中提供编辑见解功能，必须传递包含编辑权限的访问令牌。 使用[获取见解小工具](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)或[获取视频访问令牌](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?)与`&allowEdit=true`。

## <a name="widgets-interaction"></a>小组件交互

"认知见解"微件可以与应用上的视频进行交互。 本部分说明如何实现此交互。

![认知见解小部件视频索引器](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>跨域通信

要让视频索引器小部件与其他组件通信，视频索引器服务：

- 使用跨源通信HTML5方法`postMessage`。
- 跨 VideoIndexer.ai 域验证消息。

如果您实现自己的播放器代码并与认知见解小部件集成，您有责任验证来自VideoIndexer.ai的消息的来源。

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>在应用或博客中嵌入小部件（推荐）

本节演示如何实现两个视频索引器小部件之间的交互，以便当用户选择应用上的见解控件时，播放器将跳转到相关时刻。

1. 复制播放器小组件嵌入代码。
2. 复制认知见解嵌入代码。
3. 添加[转存进程文件](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)，以便处理两个小组件之间的通信：<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

现在，当用户选择应用上的见解控件时，播放器将跳转到相关时刻。

有关详细信息，请参阅[视频索引器 - 嵌入两个小工具演示](https://codepen.io/videoindexer/pen/NzJeOb)。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>嵌入认知见解小组件并使用 Azure Media Player 来播放内容

本节演示如何使用[AMP 插件](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)实现认知见解微件和 Azure 媒体播放器实例之间的交互。

1. 为 AMP 播放器添加视频索引器插件：<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. 使用视频索引器插件实例化 Azure 媒体播放器。

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance.
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. 复制认知见解嵌入代码。

您现在可以与 Azure 媒体播放器进行通信。

有关详细信息，请参阅 Azure[媒体播放器 + VI 见解演示](https://codepen.io/videoindexer/pen/rYONrO)。

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>嵌入视频索引器认知见解小部件，并使用其他视频播放器

如果使用 Azure 媒体播放器以外的视频播放器，则必须手动操作视频播放器以实现通信。

1. 插入视频播放器。

    例如，标准 HTML5 播放器：

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. 嵌入认知见解小组件。
3. 通过侦听“消息”事件实现播放器的通信。 例如：

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

有关详细信息，请参阅 Azure[媒体播放器 + VI 见解演示](https://codepen.io/videoindexer/pen/YEyPLd)。

## <a name="adding-subtitles"></a>添加字幕

如果使用自己的[Azure 媒体播放器](https://aka.ms/azuremediaplayer)嵌入视频索引器见解，则可以使用 方法`GetVttUrl`获取隐藏字幕（字幕）。 您还可以从视频索引器 AMP 插件`getSubtitlesUrl`调用 JavaScript 方法（如前面所示）。

## <a name="customizing-embeddable-widgets"></a>自定义可嵌入式小组件

### <a name="cognitive-insights-widget"></a>认知见解小组件

您可以选择所需的见解类型。 为此，请将它们指定为添加到您获取的嵌入代码（从 API 或 Web 应用）的以下 URL 参数的值： `&widgets=<list of wanted widgets>`。

可能的价值观是：**人**，**关键字**，**情绪**，**成绩单**和**搜索**。

例如，如果要嵌入仅包含人员并搜索见解的小部件，iframe 嵌入 URL 如下所示：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

iframe 窗口的标题也可自定义，只需为 iframe URL 提供 `&title=<YourTitle>` 即可。 （它自定义 HTML\<标题>值）。

例如，如果需要为 iframe 窗口提供标题“MyInsights”，则 URL 将如下所示：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

请注意，仅当你需要在新窗口中打开见解时，此选项才适用。

### <a name="player-widget"></a>播放器小组件

如果嵌入视频索引器播放器，则可通过指定 iframe 的大小来选择播放器的大小。

例如：

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

默认情况下，视频索引器播放器已基于视频脚本自动生成隐藏字幕。 使用上传视频时选择的源语言从视频中提取脚本。

如果要使用其他语言进行嵌入，可以添加到`&captions=< Language | "all" | "false" >`嵌入播放器 URL。 如果需要所有可用语言的标题，请使用 值`all`。 如果需要默认显示字幕，则可传递 `&showCaptions=true`。

然后，嵌入 URL 将如下所示：

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

如果要禁用标题，可以将`captions`参数值传递给`false`。

#### <a name="autoplay"></a>自动播放
默认情况下，播放器将开始播放视频。 您可以选择不通过传递到`&autoplay=false`前面的嵌入 URL。

## <a name="code-samples"></a>代码示例

请参阅包含视频索引器 API 和小工具示例[的代码示例](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets)回购：

| 文件/文件夹                       | 描述                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | 在自定义 Azure 媒体播放器中加载视频索引器视频。                        |
| `azure-media-player-vi-insights`  | 使用自定义 Azure 媒体播放器嵌入 VI 见解。                             |
| `control-vi-embedded-player`      | 嵌入 VI 播放器并从外部控制它。                                    |
| `custom-index-location`           | 从自定义外部位置嵌入 VI 见解（可以是客户 Blob）。     |
| `embed-both-insights`             | VI 洞察的基本用法包括玩家和见解。                            |
| `embed-insights-with-AMP`         | 使用自定义 Azure 媒体播放器嵌入 VI 见解小部件。                      |
| `customize-the-widgets`           | 嵌入六个小部件与自定义选项。                                     |
| `embed-both-widgets`              | 嵌入 VI 播放器和见解，并在他们之间进行沟通。                      |
| `url-generator`                   | 根据用户指定的选项生成小部件自定义嵌入 URL。             |
| `html5-player`                    | 使用默认 HTML5 视频播放器嵌入 VI 见解。                           |

## <a name="next-steps"></a>后续步骤

有关如何查看和编辑视频索引器见解的信息，请参阅[查看和编辑视频索引器见解](video-indexer-view-edit.md)。

此外，请查看[视频索引器代码笔](https://codepen.io/videoindexer/pen/eGxebZ)。

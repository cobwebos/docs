---
title: 将 Azure 视频索引器小组件嵌入应用程序 | Microsoft Docs
description: ''
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 4ce1eedac69b06f491510be93fcfbdfbc879ff7e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398126"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>将视频索引器小组件嵌入应用程序

视频索引器支持将两类小组件嵌入应用程序：**认知见解**和**播放器**。 

* **认知见解**小组件包括从视频索引过程中提取的所有视觉见解。 
    见解小组件支持以下可选的 URL 参数：

    |名称|定义|Description|
    |---|---|---|
    |widgets|用逗号分隔的字符串|用于控制要呈现的见解。 <br/>示例：**widgets=people,brands** 将只呈现人物和品牌 UI 见解<br/>可用选项：People、Keywords、Annotations、Brands、Sentiments、Transcript、Search | 
* **播放器**小组件用于通过自适应比特率来流式传输视频。

    播放器小组件支持以下可选的 URL 参数：

    |名称|定义|Description|
    |---|---|---|
    |t|距离开始的秒数|让播放器从给定时间点开始播放。<br/>示例：t=60|
    |captions|语言代码|在小组件加载过程中提取给定语言的字幕，使之在字幕菜单中可用。<br/>示例：captions=en-Us|
    |showCaptions|布尔值|使播放器与已启用的字幕一起加载。<br/>示例：showCaptions=true|
    |type||激活音频播放器外观（视频部件已删除）。<br/>示例：type=audio|
    |autoplay|布尔值|决定播放器是否会在加载后开始播放视频（默认为 true）。<br/>示例：autoplay=false|
    |语言|语言代码|控制播放器控件本地化（默认为 en-US）<br/>示例：language=de-DE|

## <a name="embedding-public-content"></a>嵌入公共内容

1. 登录到[视频索引器](https://api-portal.videoindexer.ai/)帐户。 
2. 单击显示在视频下面的“嵌入”按钮。

    ![小组件](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    单击按钮以后，一个嵌入模式会显示在屏幕上，你可以在其中选择要嵌入到应用程序中的具体小组件。
    选择一个小组件（**播放器**或**认知见解**），生成可以粘贴在应用程序中的嵌入式代码。
 
3. 选择所需小组件的类型（**认知见解**或**播放器**）。
4. 复制嵌入代码并将其添加到应用程序。 

    ![小组件](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>嵌入专用内容

可以从嵌入弹出窗口（如上一部分所示）获取嵌入代码，但仅限**公用**视频。 

若要嵌入**专用**视频，需在 **iframe** 的 **src** 属性中传入访问令牌：

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<VideoId>/?accessToken=<accessToken>
    
使用[**获取见解小组件**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) API 获取认知见解小组件内容，或者使用[**获取视频访问令牌**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?)并将其作为查询参数添加到 URL，如上所示。 将此 URL 指定为 **iframe** 的 **src** 值。

若要在嵌入式小组件中提供编辑见解功能（就像我们在 Web 应用程序中拥有的功能一样），需传递具有编辑权限的访问令牌。 请使用[**获取见解小组件**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?)或[**获取视频访问令牌**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?)并设置 **&allowEdit=true**。 

## <a name="widgets-interaction"></a>小组件交互

**认知见解**小组件可以与应用程序的视频交互。 本部分说明如何实现此交互。

![小组件](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>跨域通信

为了让视频索引器小组件与其他组件通信，视频索引器服务会执行以下操作：

- 使用跨域通信 HTML5 方法 **postMessage**； 
- 跨 VideoIndexer.ai 域验证消息。 

如果选择实施你自己的播放器代码并完成与**认知见解**小组件的集成，则你有责任验证来自 VideoIndexer.ai 的消息的域。

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>在应用程序/博客中嵌入两种类型的小组件（推荐） 

本部分介绍如何在两个视频索引器小组件之间实现交互，这样当某个用户单击应用程序中的见解控件时，播放器就会跳到相关的时刻。

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. 复制**播放器**小组件嵌入代码。
2. 复制**认知见解**嵌入代码。
3. 添加[**转存进程文件**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)，以便处理两个小组件之间的通信：

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

现在，当某个用户单击应用程序中的见解控件时，播放器就会跳到相关的时刻。

有关详细信息，请参阅[此演示](https://api-portal.videoindexer.ai/demo-all-widgets)。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>嵌入认知见解小组件并使用 Azure Media Player 来播放内容

本部分介绍如何使用 [AMP 插件](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)来实现**认知见解**小组件与 Azure Media Player 实例之间的交互。
 
1. 为 AMP 播放器添加视频索引器插件。

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. 使用视频索引器插件实例化 Azure Media Player。

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
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

        // Init your AMP instance
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
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. 复制**认知见解**嵌入代码。

现在应该可以与 Azure Media Player 通信了。

有关详细信息，请参阅[此演示](https://api-portal.videoindexer.ai/demo-your-amp)。

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>嵌入视频索引器认知见解小组件并使用你自己的播放器（可以是任何播放器）

如果使用自己的播放器，则需自行负责播放器的操作以实现通信。 

1. 插入视频播放器。

    例如，标准 HTML5 播放器

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. 嵌入认知见解小组件。
3. 通过侦听“消息”事件实现播放器的通信。 例如：

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>


有关详细信息，请参阅[此演示](https://api-portal.videoindexer.ai/demo-your-player)。

## <a name="adding-subtitles"></a>添加字幕

如果将视频索引器见解嵌入到你自己的 AMP 播放器中，则可使用 **GetVttUrl** 方法获取隐藏式字幕。 也可从视频索引器 AMP 插件 **getSubtitlesUrl**（如前所示）调用 javascript 方法。 

## <a name="customizing-embeddable-widgets"></a>自定义可嵌入式小组件

### <a name="cognitive-insights-widget"></a>认知见解小组件
可以选择所需见解的类型，方法是将其指定为以下 URL 参数的值，此参数已添加到从 API 或 Web 应用程序获取的嵌入代码：

**&widgets=** \<所需小组件的列表>

可用值：people、keywords、sentiments、transcript、search。

例如，如果需要嵌入的小组件仅包含人物和搜索见解，则 iframe 嵌入 URL 将如下所示：https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?widgets=people,search

iframe 窗口的标题也可自定义，只需为 iframe URL 提供 **&title=**<YourTitle> 即可。 （它会自定义 html \<title> 值）。
例如，如果需要为 iframe 窗口提供标题“MyInsights”，则 URL 将如下所示：https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?title=MyInsights。 请注意，仅当你需要在新窗口中打开见解时，此选项才适用。

### <a name="player-widget"></a>播放器小组件
如果嵌入视频索引器播放器，则可通过指定 iframe 的大小来选择播放器的大小。

例如：

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/{id}” frameborder="0" allowfullscreen />

默认情况下，视频索引器播放器会根据从视频提取的视频脚本以及在视频上传时选定的源语言来自动生成隐藏式字幕。

若要在嵌入时使用其他语言，则可向嵌入播放器 URL 添加 **&captions=< Language | ”all” | “false” >**；或者，若要使用所有可用的语言字幕，则可使用“all”作为值。
如果需要默认显示字幕，则可传递 **&showCaptions=true**

然后，嵌入 URL 将如下所示：https://www.videoindexer.ai/embed/player/9a296c6ec3/?captions=italian。 若要禁用字幕，则可传递“false”作为 captions 参数的值。

自动播放 - 播放器会按照默认设置开始播放视频。 可以选择不自动播放，只需向上述嵌入 URL 传递 &autoplay=false 即可。

## <a name="next-steps"></a>后续步骤

若要了解如何查看和编辑视频索引器见解，请参阅[此](video-indexer-view-edit.md)文。

## <a name="see-also"></a>另请参阅

[视频索引器概述](video-indexer-overview.md)

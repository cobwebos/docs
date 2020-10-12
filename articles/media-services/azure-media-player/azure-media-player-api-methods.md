---
title: Azure Media Player API 方法
description: 使用 Azure Media Player API 可以通过 JavaScript 与视频交互，无论浏览器是通过 HTML5 视频、Flash、Silverlight 还是任何其他受支持的播放技术播放视频。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81727263"
---
# <a name="api"></a>API #

使用 Azure Media Player API 可以通过 JavaScript 与视频交互，无论浏览器是通过 HTML5 视频、Flash、Silverlight 还是任何其他受支持的播放技术播放视频。

## <a name="referencing-the-player"></a>引用播放机 ##

若要使用 API 函数，需要访问 player 对象。 幸运的是，很容易获得。 只需确保视频标记具有 ID。 嵌入代码的示例具有一个 ID `vid1` 。 如果一个页面上有多个视频，请确保每个视频标记都具有唯一的 ID。

`var myPlayer = amp('vid1');`

> [!NOTE]
> 如果尚未通过数据安装属性或其他方法初始化播放机，这也会初始化播放器。

## <a name="wait-until-the-player-is-ready"></a>等待播放机准备就绪 ##

Azure Media Player 设置视频和 API 所花的时间取决于所使用的播放技术。 HTML5 的加载速度通常比 Flash 或 Silverlight 要快得多。 出于此原因，应该使用播放机的 "就绪" 函数触发需要播放机的 API 的任何代码。

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OR

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API 方法 ##

现在，你可以访问准备就绪的播放机，可以控制视频、获取值或响应视频事件。 Azure Media Player API 函数名称将尝试遵循 [HTML5 媒体 API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)。 主要区别在于，getter/setter 函数用于视频属性。

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>注册事件 ##
应在第一次初始化播放机之后直接注册事件，以确保所有事件都已正确报告给应用程序，并且应在就绪事件外完成。

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>后续步骤 ##

<!---Some context for the following links goes here--->
- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
---
title: Azure 媒体播放器 API 方法
description: Azure 媒体播放器 API 允许您通过 JavaScript 与视频进行交互，无论浏览器是通过 HTML5 视频、Flash、Silverlight 或任何其他受支持的播放技术播放视频。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727263"
---
# <a name="api"></a>API #

Azure 媒体播放器 API 允许您通过 JavaScript 与视频进行交互，无论浏览器是通过 HTML5 视频、Flash、Silverlight 或任何其他受支持的播放技术播放视频。

## <a name="referencing-the-player"></a>引用播放器 ##

要使用 API 函数，您需要访问播放器对象。 幸运的是，这是很容易得到。 您只需确保您的视频标签具有 ID。 示例嵌入代码的 ID 为`vid1`。 如果一个页面上有多个视频，请确保每个视频标记都具有唯一的 ID。

`var myPlayer = amp('vid1');`

> [!NOTE]
> 如果玩家尚未通过数据设置属性或其他方法初始化，这也将初始化该播放器。

## <a name="wait-until-the-player-is-ready"></a>等待玩家准备就绪 ##

设置视频和 API 所需的时间因正在使用的播放技术而异。 HTML5 的加载速度通常比闪存或银光快得多。 因此，玩家的"就绪"功能应用于触发任何需要玩家 API 的代码。

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

现在，您可以访问现成的播放器，您可以控制视频、获取值或响应视频事件。 Azure 媒体播放器 API 函数名称尝试遵循[HTML5 媒体 API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)。 主要区别是 getter/setter 函数用于视频属性。

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>注册活动 ##
事件应在首次初始化播放器后直接注册，以确保所有事件都适当地报告给应用程序，并且应在就绪事件之外完成。

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>后续步骤 ##

<!---Some context for the following links goes here--->
- [Azure 媒体播放器快速入门](azure-media-player-quickstart.md)
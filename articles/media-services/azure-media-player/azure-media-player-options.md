---
title: Azure Media Player 选项
description: Azure Media Player 嵌入代码只是一个 HTML5 视频标记，因此对于很多选项，你可以使用标准标记特性来设置选项。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 9ab79a60f55fad6a45fadb6bf8890d2879caac62
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043584"
---
# <a name="options"></a>选项 #

## <a name="setting-options"></a>设置选项 ##

Azure Media Player 嵌入代码只是一个 HTML5 视频标记，因此对于很多选项，你可以使用标准标记特性来设置选项。

`<video controls autoplay ...>`

此外，还可以使用数据安装属性提供[JSON](http://json.org/example.html)格式的选项。 这也是如何设置不标准给视频标记的选项的方式。

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

最后，如果未使用数据安装属性来触发播放机安装程序，则可以使用播放机选项作为 JavaScript 安装函数中的第二个参数传递对象。

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> 仅在设置源之前的第一个初始化中设置构造函数中的选项。  如果希望修改同一已初始化 Azure Media Player 元素上的选项，则必须在更改源之前更新选项。 您可以使用更新 JavaScript 中的选项 `myPlayer.options({/*updated options*/});` 。 请注意，仅更改的选项会受到影响，所有以前设置的选项都将保留。

## <a name="individual-options"></a>单个选项 ##

> [!NOTE]
>视频标记属性只能为 true 或 false （布尔值），只需包含属性（不带等号）即可将其打开，或排除它以将其关闭。 例如，若要打开控件：错误 `<video controls="true" ...>` 正确 `<video controls ...>` 的用户遇到的最大问题是，使用 false 作为值（如 controls = "false"），该值实际执行相反的操作，并将值设置为 true，因为属性仍包含在内。

### <a name="controls"></a>controls ###

"控件" 选项用于设置播放机是否具有用户可与之交互的控件。 如果不使用控件，开始视频播放的唯一方法是使用自动播放特性或通过 API。

`<video controls ...>` 或 `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

如果自动播放为 true，则在页面加载后（无需任何用户交互），视频将立即开始播放。

> [!NOTE]
> Windows Phone、Apple iOS 和 Android 等移动设备不支持此选项。 移动设备阻止自动播放功能，以防止使用者的每月数据计划（通常是昂贵的）。 在这种情况下，用户需要按下 "触摸/单击" 才能启动视频。

`<video autoplay ...>`或`{ "autoplay": true }`

### <a name="poster"></a>广告 ###
海报属性设置视频开始播放前显示的图像。 这通常是视频或自定义标题屏幕的帧。 一旦用户单击 "播放"，图像就会消失。

`<video poster="myPoster.jpg" ...>` 或 `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

Width 属性设置视频的显示宽度。

`<video width="640" ...>` 或 `{ "width": 640 }`

### <a name="height"></a>高度 ###

"高度" 属性设置视频的显示高度。

`<video height="480" ...>` 或 `{ "height": 480 }`

### <a name="plugins"></a>插件 ###

插件 JSON 确定与该实例一起加载的插件，以便你可以配置插件可能具有的任何选项。

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

有关插件开发和使用的详细信息，请参阅[编写插件](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>其他选项 ###

可以 `<video>` 通过使用采用 JSON 的参数对标记设置其他选项 `data-setup` 。
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

此值显式设置为 false。 如果将设置为 false，则将允许在平台之间呈现 Azure Media Player 的外观。  而且，与名称相反，仍将启用触摸。

### <a name="fluid"></a>液体 ###

如果将此选项设置为 "true"，则视频元素会获得父容器的完整宽度，并将调整高度以适应具有标准16:9 纵横比的视频。

`<video ... data-setup='{"fluid": true}'>`

`fluid`选项替代显式 `width` 和 `height` 设置。 此选项仅在 Azure Media Player 版本 `2.0.0` 及更高版本中可用。

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed`选项控制用户可使用的 playbackSpeed 控件和播放速度设置集。 `playbackSpeed`采用对象。 若要在控件条上启用播放速度控制， `enabled` 需要将对象的属性设置为 true。 启用标记的播放速度的示例：

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


该设置的其他属性 `playbackSpeed` 由[PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions)对象提供。

在 JavaScript 中设置播放速度选项的示例：

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

此选项仅在 Azure Media Player 版本2.0.0 和更高版本中可用。

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

`staleDataTimeLimitInSec`选项是一种优化，可让你配置要在 mediaSource 缓冲区中保留的陈旧数据的数量。 此项已默认禁用。

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

如果设置为 true，则可在实时流和实时存档中显示实时 CEA 字幕。 不需要标签属性，如果不包含该属性，播放机将回退到默认标签。

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

此选项仅在2.1.1 和更高版本 Azure Media Player 中可用。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)

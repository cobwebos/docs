---
title: Azure 媒体播放器选项
description: Azure 媒体播放器嵌入代码只是一个 HTML5 视频标记，因此对于许多选项，您可以使用标准标记属性来设置选项。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727159"
---
# <a name="options"></a>选项 #

## <a name="setting-options"></a>设置选项 ##

Azure 媒体播放器嵌入代码只是一个 HTML5 视频标记，因此对于许多选项，您可以使用标准标记属性来设置选项。

`<video controls autoplay ...>`

或者，您可以使用数据设置属性以[JSON](http://json.org/example.html)格式提供选项。 这也是如何设置不标准到视频标记的选项。

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

最后，如果您不使用数据设置属性来触发播放器设置，则可以将具有播放器选项的对象作为 JavaScript 设置函数中的第二个参数传递。

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> 构造函数中的选项仅在设置源之前在第一个初始化上设置。  如果要修改同一初始化 Azure 媒体播放器元素上的选项，则必须在更改源之前更新这些选项。 您可以使用 更新 JavaScript 中的选项`myPlayer.options({/*updated options*/});`。 请注意，只有更改的选项才会受到影响，所有其他以前设置的选项将保持不变。

## <a name="individual-options"></a>单个选项 ##

> [!NOTE]
>视频标记属性只能是真或假（布尔），您只需包括属性（无等于符号）来打开它，或排除它来将其关闭。 例如，打开控件：错误`<video controls="true" ...>`正确`<video controls ...>`遇到的最大问题是，使用 false 作为值（例如控件="false"）将这些值设置为 false，该值实际上执行相反操作并将值设置为 true，因为属性仍包含在内。

### <a name="controls"></a>controls ###

控件选项设置播放器是否具有用户可以与之交互的控件。 如果没有控件，启动视频播放的唯一方法是使用自动播放属性或通过 API。

`<video controls ...>` 或 `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

如果自动播放为 true，则视频将在加载页面后立即开始播放（无需用户进行任何交互）。

> [!NOTE]
> Windows 手机、Apple iOS 和 Android 等移动设备不支持此选项。 移动设备阻止自动播放功能，以防止消费者每月数据计划过度使用（通常成本高昂）。 在这种情况下，需要用户触摸/单击才能启动视频。

`<video autoplay ...>`或`{ "autoplay": true }`

### <a name="poster"></a>海报 ###
海报属性设置在视频开始播放之前显示的图像。 这通常是视频或自定义标题屏幕的框架。 用户一点击播放，图像就会消失。

`<video poster="myPoster.jpg" ...>` 或 `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

宽度属性设置视频的显示宽度。

`<video width="640" ...>` 或 `{ "width": 640 }`

### <a name="height"></a>height ###

高度属性设置视频的显示高度。

`<video height="480" ...>` 或 `{ "height": 480 }`

### <a name="plugins"></a>插件 ###

插件 JSON 确定哪些插件加载了 AMP 实例，允许您配置插件可能具有的任何选项。

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

有关插件开发和使用的详细信息，请参阅[编写插件](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>其他选项 ###

可以使用采用 JSON 的`<video>``data-setup`参数在标记上设置其他选项。
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>本机控制ForTouch ####

这显式设置为 false。 通过将设置为 false，它将允许跨平台呈现 Azure 媒体播放器外观。  此外，与名称相反，仍可启用触摸。

### <a name="fluid"></a>流体 ###

通过将此选项设置为 true 视频元素，将采用父容器的完整宽度，并且高度将进行调整，以适应具有标准 16：9 纵横比的视频。

`<video ... data-setup='{"fluid": true}'>`

`fluid`选项覆盖显式`width`和`height`设置。 此选项仅在 Azure 媒体播放器版本`2.0.0`和更高版本中可用。

### <a name="playbackspeed"></a>播放速度 ###

`playbackSpeed`选项控制播放速度控制和一组可供用户使用的播放速度设置。 `playbackSpeed`获取对象。 为了在控制栏上启用播放速度控制，需要将对象的`enabled`属性设置为 true。 在标记中启用播放速度的示例：

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


`playbackSpeed`设置的其他属性由[PlaybackSpeedOptions](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions)对象提供。

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

此选项仅在 Azure 媒体播放器版本 2.0.0 及更高版本中可用。

### <a name="staledatatimelimitinsec"></a>陈旧的数据时间限值 ###

该`staleDataTimeLimitInSec`选项是一个优化，允许您配置要在 mediaSource 缓冲区中保留多少秒的过时数据。 此项已默认禁用。

### <a name="cea708captionssettings"></a>cea708 标题设置 ###

启用为 true 的设置允许您在实时流和实时存档中显示实时 CEA 字幕。 标签属性不是必需的，如果未包含，播放器将回退到默认标签。

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

此选项仅在 Azure 媒体播放器版本 2.1.1 及更高版本中可用。

## <a name="next-steps"></a>后续步骤 ##

- [Azure 媒体播放器快速入门](azure-media-player-quickstart.md)
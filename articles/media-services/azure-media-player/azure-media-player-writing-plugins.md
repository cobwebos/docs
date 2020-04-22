---
title: 为 Azure 媒体播放器编写插件
description: 了解如何使用 JavaScript 使用 Azure 媒体播放器编写插件
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727107"
---
# <a name="writing-plugins-for-azure-media-player"></a>为 Azure 媒体播放器编写插件 #

插件是为扩展或增强播放器而编写的 JavaScript。 您可以编写更改 Azure 媒体播放器外观、功能甚至将其与其他服务接口的插件。 您可以通过两个简单的步骤执行此操作：

## <a name="step-1"></a>步骤 1 ##

将 JavaScript 写入这样的函数中：

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

可以直接在标记中的`<script>`HTML 页或外部 JavaScript 文件中编写代码。 如果执行后者，请确保在 AMP 脚本*之后的*HTML 页中`<head>`包含 JavaScript 文件。

示例：

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>步骤 2 ##
使用 JavaScript 以以下两种方式之一初始化插件：

方法 1：

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

方法 2：

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

插件选项不是必需的，包括它们只是允许使用插件的开发人员配置其行为，而无需更改源代码。

有关创建插件的灵感和更多示例，请查看我们的[画廊](azure-media-player-plugin-gallery.md)

>[!NOTE]
> 插件代码在查看器的播放器体验的生命周期内动态更改 DOM 中的项目，它永远不会对播放器的源代码进行永久性更改。 在这里，了解浏览器的开发人员工具将派上用场。 例如，如果要更改播放器中元素的外观，可以通过其类名称查找其 HTML 元素，然后从那里添加或更改属性。 以下是[更改 HTML 属性](http://www.w3schools.com/js/js_htmldom_html.asp)的一个很好的资源。

### <a name="integrated-plugins"></a>集成插件 ###

 目前有两个插件烘焙到AMP：[时间提示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)和[热键](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)。 这些插件最初被开发为玩家的模块化插件，但现在被包含在玩家源代码中。

### <a name="plugin-gallery"></a>插件库 ###

[插件库](http//:aka.ms/ampplugins)有几个插件，社区已经为诸如时间线标记、缩放、分析等功能做出了贡献。 该页面提供对插件的访问和有关如何设置插件的说明，以及显示插件操作的演示。 如果您创建了一个很酷的插件，你认为应该包含在我们的画廊，请随时提交它，以便我们可以检查出来。

## <a name="next-steps"></a>后续步骤 ##

- [Azure 媒体播放器快速入门](azure-media-player-quickstart.md)
---
title: 正在为 Azure Media Player 编写插件
description: 了解如何使用 JavaScript 编写带有 Azure Media Player 的插件
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: 7902dfdf81d8e44921a5218d56effc90f433f02d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857403"
---
# <a name="writing-plugins-for-azure-media-player"></a>正在为 Azure Media Player 编写插件 #

插件是为扩展或增强播放机而编写的 JavaScript。 你可以编写插件来更改 Azure Media Player 的外观、其功能，甚至使其与其他服务交互。 可以通过两个简单的步骤来执行此操作：

## <a name="step-1"></a>步骤 1 ##

在函数中编写 JavaScript，如下所示：

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

您可以直接在 HTML 页的`<script>`标记或外部 JavaScript 文件中编写代码。 如果执行后一项操作，请确保在*您的 HTML 页面*的`<head>`中包含 JavaScript 文件，然后再添加 AMP 脚本。

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
通过以下两种方式之一，用 JavaScript 初始化插件：

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

不需要插件选项，其中包括仅允许开发人员使用插件配置其行为，而无需更改源代码。

有关创建插件的灵感和更多示例，请查看我们的[库](azure-media-player-plugin-gallery.md)

>[!NOTE]
> 插件代码在查看器的播放机体验的生存期内动态更改 DOM 中的项，而永远不会对播放机的源代码进行永久更改。 这就是了解浏览器的开发人员工具的便利。 例如，如果想要更改播放机中某个元素的外观，可以通过其类名称找到其 HTML 元素，然后添加或更改其中的属性。 下面是有关[更改 HTML 属性](http://www.w3schools.com/js/js_htmldom_html.asp)的有用资源。

### <a name="integrated-plugins"></a>集成插件 ###

 目前有两个融入到 AMP 的插件：[时间提示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)和[热键](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)。 这些插件最初开发为播放机的模块化插件，但现在已包含在播放机源代码中。

### <a name="plugin-gallery"></a>插件库 ###

[插件库](https://aka.ms/ampplugins)包含多个插件，社区已为时间线标记、缩放、分析等功能提供了这些插件。 页面提供对插件的访问以及有关如何设置插件的说明，以及演示如何进行操作的演示。 如果您创建的是您认为应该包含在库中的冷插件，则可随意提交，以便我们可以将其签出。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)

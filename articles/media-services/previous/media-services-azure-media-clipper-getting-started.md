---
title: Azure 媒体剪辑器入门 | Microsoft 文档
description: 开始使用 Azure 媒体剪辑器工具，用于基于 AMS 资产生成视频剪辑
services: media-services
keywords: 剪辑;子剪辑;编码;媒体
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ac64d97aeeef6147aa62658c9ee440bf058f4db1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783286"
---
# <a name="create-clips-with-azure-media-clipper"></a>使用 Azure 媒体剪辑器创建剪辑
本部分介绍开始使用 Azure 媒体剪辑器的基本步骤。 以下部分介绍如何配置 Azure 媒体剪辑器的具体信息。

- 首先，将 Azure 媒体播放器和 Azure 媒体剪辑器的以下链接添加到文档的开头。 我们建议在 URL 中显式指定剪辑器和 Azure 媒体播放器的版本。 请不要在生产中使用这些资源的最新版本，因为它们可能会根据需要进行更改。

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- 接下来，将以下类添加到想要从中实例化剪辑器的 div 元素中。

```javascript
<div id="root" class="azure-subclipper" />
```

（可选）若要启用深色主题，请添加深色皮肤类：

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- 接下来，使用以下 API 调用实例化剪辑器：

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

初始化方法调用的参数是：
- `selector` {REQUIRED, string}：应从中呈现小组件的匹配 HTML 元素的 CSS 选择器。
- `restVersion` {REQUIRED, string}：要面向的 Azure 媒体服务 REST API 版本。 REST 版本定义小组件生成的输出格式。 目前仅支持 2.0 版本。
- `submitSubclipCallback` {REQUIRED, promise}：单击小组件的“提交”按钮时所调用的回调函数。 回调函数应预期小组件生成的输出（呈现作业配置或筛选器定义）。 有关详细信息，请参阅 Submit 子剪辑回调。
- `logLevel` {OPTIONAL, {'info', 'warn', 'error'}}：在浏览器的控制台中显示的日志记录级别。 默认值：错误
- `minimumMarkerGap` {OPTIONAL, int}：子剪辑的最小大小（以秒为单位）。 注意：该值应大于或等于 6，后者也是默认值。
- `singleBitrateMp4Profile` {OPTIONAL, JSON object}：供小组件生成的呈现作业配置所用的单比特率 MP4 配置文件。 如未提供，则它使用[默认单比特率 MP4 配置文件](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p)。
- `multiBitrateMp4Profile` {OPTIONAL, JSON object}：供小组件生成的呈现作业配置所用的多比特率 MP4 配置文件。 如未提供，则它使用[默认多比特率 MP4 配置文件](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p)。
- `keymap` {OPTIONAL, json object}：允许自定义小组件的键盘快捷方式。 有关详细信息，请参阅[可自定义的键盘快捷方式](media-services-azure-media-clipper-keyboard-shortcuts.md)。
- `assetsPanelLoaderCallback` {OPTIONAL, promise}：每当用户滚动到窗格底部时，将资产的新页面（异步）加载到资产窗格所调用的回调函数。 有关详细信息，请参阅资产窗格加载程序回调。
- `height` {OPTIONAL, number}：小组件的总高度（最小高度为 600 px（不含资产窗格）和 850 px（含资产窗格））。
- `subclippingMode` (OPTIONAL, {'all', 'render', 'filter'})：所允许的子剪辑模式。 默认值为全部。
- `filterAssetsTypes` (OPTIONAL, bool)：借助 filterAssetsTypes，可以显示/隐藏资产窗格中的筛选器下拉列表。 默认值为 true。
- `speedLevels` (OPTIONAL, array)：speedLevels 允许为视频播放器设置不同的速度级别，请参阅 [Azure 媒体播放器文档](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions)，以了解详细信息。
- `resetOnJobDone` (OPTIONAL, bool)：当作业成功提交时，resetOnJobDone 允许剪辑器将子剪辑器重置为初始状态。
- `autoplayVideo` (OPTIONAL, bool)：autoplayVideo 允许剪辑器自动播放加载的视频。 默认值为 true。
- `language` {OPTIONAL, string}：设置小组件语言的语言。 如未指定，则小组件将基于浏览器语言尝试本地化消息。 如果在浏览器中未检测到任何语言，则小组件默认为英语。 有关详细信息，请参阅[配置本地化](media-services-azure-media-clipper-localization.md)部分。
- `languages` {OPTIONAL, JSON}：语言参数将默认语言字典替换为由用户定义的自定义字典。 有关详细信息，请参阅[配置本地化](media-services-azure-media-clipper-localization.md)部分。
- `extraLanguages` (OPTIONAL, JSON)：extraLanaguages 参数向默认字典添加新语言。 有关详细信息，请参阅[配置本地化](media-services-azure-media-clipper-localization.md)部分。

## <a name="typescript-definition"></a>TypeScript 定义
可以在[此处](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts)查看剪辑器的 [TypeScript](https://www.typescriptlang.org/) 定义文件。

## <a name="azure-media-clipper-api"></a>Azure 媒体剪辑器 API
本部分介绍剪辑器提供的 API 图面。

- `ready(handler)`：提供在剪辑器完全加载并可使用时立即运行 JavaScript 的方法。
- `load(assets)`：将资产列表加载到小组件时间线中（不能与 assetsPanelLoaderCallback 一同使用）。 请参阅此[文章](media-services-azure-media-clipper-load-assets.md)以了详细解如何将资产加载到剪辑器。
- `setLogLevel(level)`：设置在浏览器的控制台中显示的日志记录级别。 可能的值为：`info`、`warn`、`error`。
- `setHeight(height)`：设置小组件的总高度，单位为像素（最小高度为 600 px（不含资产窗格）和 850 px（含资产窗格））。
- `version`：获取小组件版本。

## <a name="next-steps"></a>后续步骤
查看配置 Azure 媒体剪辑器的以下步骤：
- [将资产加载到 Azure 媒体剪辑器](media-services-azure-media-clipper-load-assets.md)
- [配置自定义键盘快捷方式](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [从剪辑器提交剪辑作业](media-services-azure-media-clipper-submit-job.md)
- [配置本地化](media-services-azure-media-clipper-localization.md)
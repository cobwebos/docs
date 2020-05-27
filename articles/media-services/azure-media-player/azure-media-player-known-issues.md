---
title: Azure Media Player 已知问题
description: 当前版本有下列已知问题。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 05/11/2020
ms.openlocfilehash: 4d0d56dc4d6e7fa7765439e7387d67bb80a6ec9f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124629"
---
# <a name="known-issues"></a>已知问题 #

当前版本有下列已知问题：

## <a name="azure-media-player"></a>Azure Media Player ##

- 编码器配置不当可能会导致播放问题
- 时间戳大于 2 ^ 53 的流可能会出现播放问题。
  - 缓解：使用 90-kHz 视频和 44.1-kHz 音频时间刻度
- 在没有用户交互的情况下，不会在移动设备上自动播放。 它被平台阻止。
- 寻找附近的中断可能会导致播放失败。
- 下载大型演示可能会导致 UI 锁定。
- 演示结束后，不能再次自动播放同一源。
  - 若要在某个源结束后进行重播，需要再次设置该源。
- 空清单可能会导致播放器出现问题。
  - 当启动实时流时，如果在清单中找不到足够的区块，就会出现此问题。
- 播放位置可能位于 UI 拖动条外部。
- 事件排序并非在所有技术中都一致。
- 缓冲的属性并非在不同技术中一致。
- nativeControlsForTouch 必须为 false（默认值），以避免“对象不支持属性或方法‘setControls’”
- poster 现在必须是绝对 URL
- 当使用设备的高对比度模式时，UI 中可能会出现轻微的美观问题
- 在完全解码的字符串中包含“%”或“+”的 URL 在设置源时可能会出现问题

## <a name="ad-insertion"></a>广告插入 ##

- 当浏览器中安装了广告阻止程序时，插入广告（点播或直播）可能会遇到问题
- 移动设备在播放广告时可能会出现问题。
- Azure Media Player 目前不支持 MP4 Midroll 广告。

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- 在实时内容的 DVR 窗口中，当内容完成时，时间线会继续延长，直到寻找到该区域或到达演示末尾。
- 启用了 MSE 的 Firefox 中的实时演示有一些问题

- 纯音频的资产不会通过 AzureHtml5JS 技术播放。
  - 如果想要播放没有音频的资产，可以使用 [Azure 媒体服务资源管理器工具](https://aka.ms/amse)来插入空白音频
  - 有关如何插入无声音频的说明，可查看[此文](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)

## <a name="flash"></a>闪烁 ##

- 由于 Adobe 的缓存逻辑中有一个 bug，AES 内容无法在 Flash 版本 30.0.0.134 中播放。 Adobe 已修复了此问题，并在 30.0.0.154 中发布了修补程序
- 出现 http 技术故障（例如 404 网络超时）时，播放器所需的恢复时间比其他技术故障要长。
- 单击采用 flashSS 技术的视频区域不会播放/暂停播放器。
- 如果用户已安装了 Flash 但未提供在站点上加载它的权限，则可能会出现一个无限旋转的图标。 这是因为播放器认为插件已安装并且可用，并认为该插件正在运行内容。 JavaScript 代码已发送，但浏览器设置阻止了插件执行，直到用户接受提示来允许插件为止。 在所有浏览器中都会发生此情况。  

## <a name="silverlight"></a>Silverlight ##

- 缺少的功能
- 出现 http 技术故障（例如 404 网络超时）时，播放器所需的恢复时间比其他技术故障要长。
- Mac 上的 Safari 和 Firefox 通过 Silverlight 进行的播放需要显式为源定义 `"http://` 或 `https://`。
- 如果此技术缺少某个 API，通常会返回 null。
- 如果用户已安装了 Flash 但未提供在站点上加载它的权限，则可能会出现一个无限旋转的图标。 这是因为播放器认为插件已安装并且可用，并认为该插件正在运行内容。 JavaScript 代码已发送，但浏览器设置阻止了插件执行，直到用户接受提示来允许插件为止。 在所有浏览器中都会发生此情况。  

## <a name="native-html5"></a>原生 HTML5 ##

- Html5 技术仅为首次设置的源发送 canplaythrough 事件。
- 此技术仅支持浏览器已实现的功能。  此技术中可能缺少某些功能。  
- 如果此技术缺少某个 API，通常会返回 null。
- 采用此技术时隐藏式字幕和字幕出现问题。 采用此技术时它们可能不可用或不可查看。
- 在 HLS/Html5 技术方案中，带宽有限会导致音频可播放但视频不可播放。

### <a name="microsoft"></a>Microsoft ###

- 由于与 ECMAScript 5 不兼容，IE8 播放功能目前无法使用
- 在 IE 和某些版本的 Edge 中，不能通过 Tab 键切换到相关按钮并选择它或使用 F/F 热键来进入全屏。

## <a name="google"></a>Google ##

- 同一清单中有多个编码配置文件会在 Chrome 中导致一些播放问题，因此不建议这样使用。
- Chrome 无法播放带有 AzureHtml5JS 的 HE-AAC。 请按照 [bug 跟踪器](https://bugs.chromium.org/p/chromium/issues/detail?id=534301)上的详细说明进行操作。
- 从 Chrome v58 起，必须通过 https:// 协议加载/播放 Widevine 内容，否则播放会失败。

## <a name="mozilla"></a>Mozilla ##

- 当使用 AzureHtml5JS 时，音频流切换要求各个音频流具有相同的编解码器专用数据。 Firefox 平台具有此要求。

## <a name="apple"></a>苹果 ##

- 默认情况下，Mac 上的 Safari 通常会通过“停止插件以省电”设置启用“节能程序”模式。当认为 Flash 和 Silverlight 之类的插件对用户无益时，此设置会阻止这些插件。 此阻止功能不会阻止插件存在，只会阻止相关功能。 如果采用默认 techOrder，则可能会导致在尝试播放时出现问题
  - 缓解措施 1：如果视频播放器位于“前端和中间”（从文档左上角开始的 3000 x 3000 像素边界内），则应当仍然可以播放。
  - 缓解措施 2：将 Safari 的 techOrder 更改为 ["azureHtml5JS", "html5"]。 此缓解措施意味着不能获得 FlashSS 技术中提供的所有功能。
- 通过 Silverlight 播放 PlayReady 内容可能会在 Safari 中导致播放问题。
- AES 和受限令牌内容不能使用 iOS 和较旧的 Android 设备播放。
  - 若要实现此方案，必须向你的服务添加代理。
- IOS 手机的默认外观出现透视问题。
- iPhone 播放始终在原生播放器全屏中进行。
  - 功能（包括隐藏式字幕）在此非内联播放中可能不会持久保存。
- 当实时演示结束时，iOS 设备将无法正确结束演示。
- iOS 不允许 DVR 功能。
- iOS 音频流切换只能通过 iOS 原生播放器 UI 完成，并要求各个音频流具有相同的编解码器专用数据
- 早期版本的 Safari 在播放实时流时可能会出现问题。

## <a name="older-android"></a>旧版 Android ##

- AES 和受限令牌内容不能使用 iOS 和较旧的 Android 设备播放。
  - 若要实现此方案，必须向你的服务添加代理。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
---
title: Azure Media Player 已知问题
description: 当前版本具有以下已知问题。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 05/11/2020
ms.openlocfilehash: 4d0d56dc4d6e7fa7765439e7387d67bb80a6ec9f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124629"
---
# <a name="known-issues"></a>已知问题 #

当前版本具有以下已知问题：

## <a name="azure-media-player"></a>Azure Media Player ##

- 错误配置的编码器可能会导致播放问题
- 时间戳大于 2 ^ 53 的流可能出现播放问题。
  - 缓解：使用 90-kHz 视频和 44.1-kHz 音频刻度
- 无需用户交互即可在移动设备上自动播放。 它被平台阻止。
- 查找附近的中断可能会导致播放失败。
- 下载大型演示可能会导致 UI 锁定。
- 演示结束后，不能再次自动播放同一源。
  - 若要在结束后重播源，则需要再次设置源。
- 空清单可能会导致播放机出现问题。
  - 当启动实时流时，如果在清单中找不到足够的块区，就会出现此问题。
- 播放位置可能位于 UI 拖动条外部。
- 在所有方面中，事件顺序不一致。
- 缓冲属性在方面中不一致。
- nativeControlsForTouch 必须为 false （默认值），以避免 "对象不支持属性或方法 ' setControls '"
- 海报现在必须是绝对 url
- 当使用设备的高对比度模式时，UI 中可能出现美观问题
- 完全解码的字符串中包含 "%" 或 "+" 的 Url 在设置源时可能存在问题

## <a name="ad-insertion"></a>广告插入 ##

- 当在浏览器中安装 ad-阻止程序时，广告可能会遇到插入（点播或实时）的问题
- 移动设备在播放广告时可能会出现问题。
- Midroll 目前不 Azure Media Player 支持广告。

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- 在实时内容的 DVR 窗口中，当内容完成后，时间线将继续增长，直至查找到该区域或到达演示结尾。
- Firefox 中启用了 MSE 的实时演示文稿有一些问题

- 仅限音频的资产将无法通过 AzureHtml5JS 技术播放。
  - 如果想要播放不带音频的资产，可以通过使用[Azure 媒体服务资源管理器工具](https://aka.ms/amse)插入空白音频来实现此目的。
  - 可在[此处](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)找到有关如何插入静态音频的说明

## <a name="flash"></a>Flash ##

- 由于 Adobe 的缓存逻辑中的 bug，AES 内容不会播放到 Flash 版本30.0.0.134。 Adobe 修复了此问题，并在30.0.0.154 中发布了该问题
- 技术和 http 故障（例如404网络超时），播放机所需的恢复时间比其他方面要长。
- 单击 "视频" 区域，flashSS 技术不会播放/暂停播放机。
- 如果用户已安装了闪存但未提供在站点上加载它的权限，则可能会发生无限旋转。 这是因为播放机认为插件已安装并且可用，并认为该插件正在运行内容。 JavaScript 代码已发送，但浏览器设置阻止了插件执行，直到用户接受允许插件的提示。 这种情况可能发生在所有浏览器中。  

## <a name="silverlight"></a>Silverlight ##

- 缺少的功能
- 技术和 http 故障（例如404网络超时），播放机所需的恢复时间比其他方面要长。
- 在带有 Silverlight 的 Mac 上播放 Safari 和 Firefox 需要 `"http://` 为源显式定义或 `https://` 。
- 如果此技术缺少 API，则它通常将返回 null。
- 如果用户已安装了闪存但未提供在站点上加载它的权限，则可能会发生无限旋转。 这是因为播放机认为插件已安装并且可用，并认为该插件正在运行内容。 JavaScript 代码已发送，但浏览器设置阻止了插件执行，直到用户接受允许插件的提示。 这种情况可能发生在所有浏览器中。  

## <a name="native-html5"></a>本机 HTML5 ##

- Html5 技术只为第一组源发送 canplaythrough 事件。
- 此技术仅支持浏览器实现了哪些功能。  此技术中可能缺少某些功能。  
- 如果此技术缺少 API，则它通常将返回 null。
- 此技术上出现标题和副标题问题。 它们可能在此技术上可用或不可查看。
- 在 HLS/Html5 技术方案中带宽有限会导致音频播放不带视频。

### <a name="microsoft"></a>Microsoft ###

- 由于与 ECMAScript 5 不兼容，IE8 播放当前不起作用
- 在 IE 和某些版本的 Edge 中，不能通过 tab 键切换到按钮并选择它或使用 F/F 热键来进入全屏。

## <a name="google"></a>Google ##

- 同一清单中的多个编码配置文件在 Chrome 中有一些播放问题，因此不建议使用。
- Chrome 无法播放带有 AzureHtml5JS 的 AAC。 遵循[bug 跟踪](https://bugs.chromium.org/p/chromium/issues/detail?id=534301)器的详细信息。
- 从 Chrome v58 中，widevine 内容必须通过 https://协议加载/播放，否则播放将会失败。

## <a name="mozilla"></a>Mozilla ##

- 当使用 AzureHtml5JS 时，音频流切换要求音频流具有相同的编解码器专用数据。 Firefox 平台需要此。

## <a name="apple"></a>Apple ##

- 默认情况下，Mac 上的 Safari 使用 "停止插件以节省电能" 设置，默认情况下会启用 "节能程序" 模式，这会在其认为不符合用户的情况下阻止诸如 Flash 和 Silverlight 的插件。 此块不会阻止插件现有，只会阻止功能。 给定默认 techOrder，这可能会导致在尝试播放时出现问题
  - 缓解1：如果视频播放器为 "正面和中心" （从文档左上角开始的 3000 x 3000 像素边界内），则它仍应播放。
  - 缓解措施2：将 Safari 的 techOrder 更改为 ["azureHtml5JS"，"html5"]。 此缓解措施不会获得 FlashSS 技术中提供的所有功能。
- 通过 Silverlight 播放 PlayReady 内容可能会在 Safari 中播放问题。
- AES 和受限令牌内容不会使用 iOS 和较旧的 Android 设备播放。
  - 若要实现此方案，必须将代理添加到服务。
- IOS 手机的默认外观通过显示。
- iPhone 播放始终在本机播放机全屏中进行。
  - 功能（包括标题）在此非内联播放中可能不会持久保存。
- 当实时演示文稿结束时，iOS 设备将无法正确结束演示。
- iOS 不允许 DVR 功能。
- iOS 音频流切换只能通过 iOS 本机播放机 UI 完成，并要求音频流具有相同的编解码器专用数据
- 早期版本的 Safari 可能会在播放实时流时出现问题。

## <a name="older-android"></a>旧版 Android ##

- AES 和受限令牌内容不会使用 iOS 和较旧的 Android 设备播放。
  - 若要实现此方案，必须将代理添加到服务。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
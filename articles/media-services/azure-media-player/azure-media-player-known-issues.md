---
title: Azure 媒体播放器已知问题
description: 当前版本具有以下已知问题。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727211"
---
# <a name="known-issues"></a>已知问题 #

当前版本具有以下已知问题：

## <a name="azure-media-player"></a>Azure Media Player ##

- 配置不正确的编码器可能会导致播放问题
- 时间戳大于 2^53 的流可能有播放问题。
  - 缓解：使用 90 kHz 视频和 44.1 kHz 音频时间刻度
- 没有用户交互，在移动设备上无法自动播放。 它被平台屏蔽了。
- 查找接近不连续性可能会导致播放失败。
- 下载大型演示文稿可能会导致 UI 锁定。
- 演示文稿结束后，无法再次自动播放同一源。
  - 要在源结束后重播该源，需要再次设置源。
- 空清单可能会导致玩家出现问题。
  - 启动实时流时可能会出现此问题，并且在清单中找不到足够的块。
- 播放位置可能位于 UI 搜索栏之外。
- 活动订购并非在所有技术中都一致。
- 缓冲属性在技术人员之间不一致。
- 本机控制ForTouch必须为 false（默认值），以避免"对象不支持属性或方法'集控制'"
- 海报现在必须是绝对的 URL
- 使用设备高对比度模式时，UI 中可能会出现轻微的美观问题
- 在完全解码的字符串中包含"%"或"+"的 URL 在设置源时可能有问题

## <a name="ad-insertion"></a>广告插入 ##

- 在浏览器中安装广告拦截器时，广告可能插入问题（按需或实时）
- 移动设备在播放广告时可能会出现问题。

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- 在"实时内容"的 DVR 窗口中，当内容完成时，时间线将继续增长，直到查找区域或到达演示文稿的末尾。
- 启用了 MSE 的 Firefox 中的实时演示有一些问题
- 仅音频或视频的资产不会通过 AzureHtml5JS 技术播放。
  - 如果要在没有音频或视频的情况下播放资产，可以使用[Azure 媒体服务资源管理器工具](https://aka.ms/amse)插入空白音频或视频来执行此操作
    - 有关如何插入静音音频的说明[可在此处](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)找到

## <a name="flash"></a>闪烁 ##

- 由于 Adobe 缓存逻辑中的 Bug，AES 内容在 Flash 版本 30.0.0.134 中不会播放。 Adobe 已修复该问题，并在 30.0.0.154 中发布了此问题
- 技术和 http 故障（如 404 网络超时），播放器需要比其他技术更长的时间来恢复。
- 点击视频区域与flashS技术不会播放/暂停播放器。
- 如果用户安装了 Flash，但未授予在站点上加载 Flash 的权限，则可能发生无限旋转。 这是因为播放器认为插件已安装并可用，并且认为插件正在运行内容。 JavaScript 代码已发送，但浏览器设置已阻止插件执行，直到用户接受允许插件的提示。 这可能发生在所有浏览器中。  

## <a name="silverlight"></a>Silverlight ##

- 缺少的功能
- 技术和 http 故障（如 404 网络超时），播放器需要比其他技术更长的时间来恢复。
- 使用 Silverlight 在 Mac 上播放的 Safari `"http://` `https://`和 Firefox 需要显式定义或为源进行定义。
- 如果缺少此技术 API，它通常会返回 null。
- 如果用户安装了 Flash，但未授予在站点上加载 Flash 的权限，则可能发生无限旋转。 这是因为播放器认为插件已安装并可用，并且认为插件正在运行内容。 JavaScript 代码已发送，但浏览器设置已阻止插件执行，直到用户接受允许插件的提示。 这可能发生在所有浏览器中。  

## <a name="native-html5"></a>本机 HTML5 ##

- Html5 技术只发送第一组源的可播放事件。
- 这项技术仅支持浏览器已经实现的内容。  在这项技术中可能缺少一些功能。  
- 如果缺少此技术 API，它通常会返回 null。
- 关于这项技术的标题和字幕，有问题。 他们可能或可能不会在这个技术可用或查看。
- HLS/Html5 技术场景中的带宽有限会导致音频播放，无需视频。

### <a name="microsoft"></a>Microsoft ###

- 由于与 ECMAScript 5 不兼容，IE8 播放当前不起作用
- 在 IE 和某些版本的 Edge 中，无法通过选项卡输入按钮并选择该按钮或使用 F/f 热键输入全屏。

## <a name="google"></a>Google ##

- 同一清单中的多个编码配置文件在 Chrome 中具有一些播放问题，不建议使用。
- Chrome 无法使用 AzureHtml5JS 播放 HE-AAC。 按照[错误跟踪器](https://bugs.chromium.org/p/chromium/issues/detail?id=534301)的详细信息。
- 自 Chrome v58 起，必须通过https://协议加载/播放宽文内容，否则播放将失败。

## <a name="mozilla"></a>Mozilla ##

- 音频流交换机要求音频流在使用 AzureHtml5JS 时具有相同的编解码器专用数据。 火狐平台需要这个。

## <a name="apple"></a>苹果 ##

- Mac 上的 Safari 通常默认启用节能模式，设置"停止插件以节省电量"，当用户认为 Flash 和 Silverlight 等插件对用户不利时，该模式会阻止插件。 此块不会阻止插件的存在，只有功能。 给定默认的 TechOrder，这可能会在尝试播放时出现问题
  - 缓解 1：如果视频播放器是"正面和中心"（在从文档左上角开始的 3000 x 3000 像素边界内），它仍应播放。
  - 缓解 2：将 Safari 的技术订单更改为 ["azureHtml5JS"，"html5"]。 这种缓解意味着无法获取 FlashSS 技术中提供的所有功能。
- 通过 Silverlight 播放就绪内容在 Safari 中播放时可能会出现问题。
- AES 和受限令牌内容不会使用 iOS 和较旧的 Android 设备播放。
  - 为了实现此方案，必须向服务添加代理。
- iOS Phone 的默认外观显示通过。
- iPhone 播放始终出现在本机播放器全屏中。
  - 功能（包括字幕）可能不会保留在此非内联播放中。
- 实时演示结束时，iOS 设备将无法正确结束演示。
- iOS 不允许使用 DVR 功能。
- iOS 音频流切换只能通过 iOS 本机播放器 UI 完成，并且要求音频流具有相同的编解码器专用数据
- 旧版本的 Safari 可能会在播放实时流时出现问题。

## <a name="older-android"></a>较旧的安卓 ##

- AES 和受限令牌内容不会使用 iOS 和较旧的 Android 设备播放。
  - 为了实现此方案，必须向服务添加代理。

## <a name="next-steps"></a>后续步骤 ##

- [Azure 媒体播放器快速入门](azure-media-player-quickstart.md)
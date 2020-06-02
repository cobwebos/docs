---
title: Azure Media Player 更改日志
description: Azure Media Player 更改日志。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: a1a55ceec2679034125ddd202402cabcbf71e17e
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698304"
---
# <a name="changelog"></a>更改日志 #

## <a name="224-official-update-february-22-2019"></a>2.2.4（正式更新，2019 年 2 月 22 日） ##

### <a name="bug-fixes-224"></a>Bug 修复 2.2.4 ###

- [Bug 修复][AMP][辅助功能] 删除了显示错误屏幕时提供的可访问虚拟选项卡
- [Bug 修复][AMP] 修复了 IE11 和 Edge 的热键“M”
- [Bug 修复][AMP] 修复了 CEA708 字幕的异常
- [Bug 修复][AMP] 修复了 Edge 浏览器的视频冻结问题

### <a name="changes-224"></a>更改 2.2.4 ###

- [更改][AMP] 发生片段解密错误时，播放器会重试目前片段和各种片段以恢复播放
- [更改][AMP] 提高 AMP 对重叠的视频或音频片段的容错能力

## <a name="223-official-update-january-9-2019"></a>2.2.3（正式更新，2019 年 1 月 9 日） ##

### <a name="features"></a>功能 ###

- [功能][HLS] 针对 Safari HLS 播放添加了音频轨道菜单

### <a name="bug-fixes-223"></a>Bug 修复 2.2.3 ###

- [Bug 修复][AMP][辅助功能] 在现场广播播放期间，无法使用键盘选择“实时”按钮
- [Bug 修复][AMP] 修复了因 MSE 测试失败而造成的误报 0x0400003 错误
- [Bug 修复][AMP] 修复了启动实时传送流时视频可能冻结的问题

### <a name="changes-223"></a>更改 2.2.3 ###

- [更改][AMP] 在日志中添加了更多信息以改善诊断结果
- [更改][AMP] 当相同屏幕分辨率下有多个可用的比特率时，所有比特率都可供选择

## <a name="222-official-update"></a>2.2.2（正式更新） ##

### <a name="bug-fixes-222"></a>Bug 修复 2.2.2 ###

- [Bug 修复][AMP] 播放器在遇到暂时性网络中断时立即停止播放
- [Bug 修复][AMP][辅助功能] 无法通过键盘访问错误对话框
- [Bug 修复][AMP] 播放仅限音频的资产时，旋转图标一直显示，但不显示不受支持错误

### <a name="changes-222"></a>更改 2.2.2 ###

- [更改][AMP] 为广告 UI 添加了本地化字符串

## <a name="221-official-update"></a>2.2.1（正式更新） ##

### <a name="features-221"></a>功能 2.2.1 ###

- [功能][CMAF] 添加了对 HLS CMAF 的支持

### <a name="bug-fixes"></a>Bug 修复 ###

- [Bug 修复][AMP] 重试逻辑中未清除的计时器生成播放错误
- [Bug 修复][AMP][Firefox] 实时节目停止时，Firefox 和 Chrome 上不触发已结束的事件
- [Bug 修复][AMP] setsource 后显示控件，即使已在播放器选项中将控件设置为 false

### <a name="changes"></a>更改 ###

- [更改][实时字幕] 已将 CEA 字幕的 API 名称从 608 更改为 708。 有关详细信息，请参阅 [CEA708 字幕设置](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0（正式发行版） ##

### <a name="features-220"></a>功能 2.2.0 ###

- [功能][Azurehtml5JS][Flash][LiveCaptions] Azurehtml5JS 和 FlashSS 技术提供 CEA 708 字幕支持，使用户能够获得清晰的 AES 内容。

### <a name="bug-fixes-220"></a>Bug 修复 2.2.0 ###

- [Bug 修复] Flash 版本检测在 Chrome/Edge 中不起作用

### <a name="changes-220"></a>更改 2.2.0 ###

- [更改][AMP][启发] 已将启发式配置文件名称从 QuickStartive 更改为 LowLatency
- [更改][Flash] 更改了 Flash 播放器中的版本检测，方便用户使用新的 Adobe Flash 更新来播放 AES 内容。

## <a name="219-official-hotfix"></a>2.1.9（正式修补程序） ##

### <a name="bug-fixes-219"></a>Bug 修复 2.1.9 ###

- [Bug 修复][实时] 当实时传送流转换到视频点播/实时存档时发生异常

### <a name="changes-219"></a>更改 2.1.9 ###

- [更改][Flash][AES] 已将 Flash 技术逻辑修改为不使用 sharedbytearrays 来进行 AES 解密，因为从 Flash 30 开始，Adobe 已阻止此用法。 请注意，由于 v30 中的 bug，只有在 Adobe 部署新版 Flash 后，播放才能正常进行。 有关更多详细信息，请参阅[已知问题](azure-media-player-known-issues.md)

## <a name="218-official-update"></a>2.1.8（正式更新） ##

### <a name="bug-fixes-218"></a>Bug 修复 2.1.8 ###

- [Bug 修复] 旋转图标有时不显示后期搜寻和预先播放
- [Bug 修复] 启用静音选项时，播放器在启动时不静音
- [Bug 修复] 当控件设置为 false 时显示音量滑块
- [Bug 修复] 当用户跳到实时边缘时，有时重复播放
- [Bug 修复][Firefox] 播放器有时在加载时引发 JavaScript 异常
- [Bug 修复][辅助功能] 使用键盘控件选择“播放”/“暂停”/“音量”按钮时，按钮失去焦点外框
- [Bug 修复] 修复了在播放器上处置内存泄漏的问题
- [Bug 修复] 在播放器发生错误后调用 src() 并不重置源
- [Bug 修复][实时] 当用户在广播结束后单击“实时”按钮时，AMP 处于持续加载状态
- [Bug 修复][Chrome] 当浏览器最小化到后台时，播放器挂起且播放失败。

### <a name="changes-218"></a>更改 2.1.8 ###

- [更改] 更新了使用目前不支持的 Flash 30 播放 AES 内容时会显示的 0x0600001 错误。 有关更多详细信息，请参阅[已知问题](azure-media-player-known-issues.md)
- [更改] 针对当清单请求返回 404 或返回空白清单时的实时场景添加了额外的重试。

## <a name="217-official-update"></a>2.1.7（正式更新） ##

### <a name="features-217"></a>功能 2.1.7 ###

- [功能][AzureHtml5JS] 添加了配置选项以在媒体源缓冲区中刷新过时的数据

### <a name="bug-fixes-217"></a>Bug 修复 2.1.7 ###

- [Bug 修复][辅助功能][屏幕阅读器] 删除了未设置标题时播放器包含的空白标题
- [Bug 修复][UWA] 在通用 Windows 应用中播放时，AMP 引发异常
- [Bug 修复][OSX] setActiveTextTrack() 在 OSx 上的 Safari 中不正常工作
- [Bug 修复][实时] 在处置并重新初始化播放器后，单击实时边缘会生成异常
- [Bug 修复][皮肤] 特定资产的当前时间被截断
- [Bug 修复][DRM] 提供了修复措施以便在支持多个 CENC DRM 的浏览器中支持播放

### <a name="changes-217"></a>更改 2.1.7 ###

- [更改][示例][辅助功能] 已将语言标记添加到所有示例

## <a name="216-official-update"></a>2.1.6（正式更新） ##

### <a name="bug-fixes-216"></a>Bug 修复 2.1.6 ###

- [Bug 修复] AMP 针对特定资产显示错误的持续时间
- [Bug 修复][FairPlay-HLS] Fairplay 错误未传播到 UI
- [Bug 修复] AMP 2.1.5 中忽略自定义启发式属性。

### <a name="changes-216"></a>更改 2.1.6 ###

- [更改][FairPlayDRM] 删除了 FairPlay 的证书请求和许可证请求的超时，以便与 PlayReady 和 Widevine 实现保持奇偶性
- [更改] 对启发式功能进行了其他改进以对抗模糊内容

### <a name="features-216"></a>功能 2.1.6 ###

- [功能] 添加了 mpd-time-cmaf 格式支持

## <a name="215-official-hotfix"></a>2.1.5（正式修补程序） ##

### <a name="bug-fixes-215"></a>Bug 修复 2.1.5 ###

- [Bug 修复][字幕] 播放器不正确呈现 VTT 样式
- [Bug 修复][辅助功能]“实时”按钮没有 aria 标签

## <a name="214-official-update"></a>2.1.4（正式更新） ##

### <a name="bug-fixes-214"></a>Bug 修复 2.1.4 ###

- [Bug 修复][辅助功能][焦点] 用户无法通过 Tab 键将焦点置于添加到控制条全屏按钮右侧的自定义按钮上
- [Bug 修复][IE11][音量条] 在全屏模式下的 IE11 中，通过 Tab 键切换到音量弹出框会使整个视频屏幕闪烁
- [Bug 修复][皮肤|刷新] 控制条和音量条弹出框之间显示间距
- [Bug 修复][AMP][字幕] 现有播放器上的源更改时，不会清除已嵌入的旧轨道
- [Bug 修复][辅助功能][讲述人] 屏幕阅读器不正确阅读音量控制
- [Bug 修复][FlashSS] 有时不会从 Flash 技术触发播放事件
- [Bug 修复][AMP][焦点] 播放器拥有焦点且处于全屏模式时，播放/暂停需要单击两次
- [Bug 修复][AMP][皮肤] 特定资产的进度条显示错误的持续时间
- [Bug 修复][广告][广告管家] VAST 分析器不处理没有进度事件的 VAST 文件
- [Bug 修复][SDN][AMP 2.1.1] 修复了 Hive SDN 插件支持的问题
- [Bug 修复][辅助功能] 当用户的焦点位于音量按钮时，讲述人却念出“午夜静音按钮”

### <a name="changes-214"></a>更改 2.1.4 ###

- [更改][辅助功能][辅助技术] 按钮现在具有 aria-live 属性，可改善辅助技术的体验
- [更改][辅助功能][音量按钮|讲述人] 已通过修改按 Tab 键的功能和滑块行为，改善了音量按钮的辅助功能。 这些更改可让键盘用户更轻松地修改播放器的音量
- [更改] 已将非活动上下文菜单的超时从 3 秒增加到 5 秒
- [更改][辅助功能][亮度] 改善了字幕设置中下拉菜单上的亮度对比率

## <a name="213-official-update"></a>2.1.3（正式更新） ##

### <a name="bug-fixes-213"></a>Bug 修复 2.1.3 ###

- [Bug 修复][插件|标题覆盖] 标题覆盖插件在使用 AMP v2.X+ 时引发 JS 异常
- [Bug 修复] 即使禁用日志记录，也仍会将 Source Set 事件发送到 JavaScript 控制台
- [Bug 修复][皮肤] 当鼠标光标悬停在任一端的持续期间栏时，播放器的时间提示呈现在播放器上下文的外部
- [Bug 修复][辅助功能][屏幕阅读器] 当观看者的焦点置于播放器上时，讲述人念出“区域地标”或“视频播放器区域地标”
- [Bug 修复][AMP] 无法通过 CSS 禁用播放器外框
- [Bug 修复][辅助功能] 当用户处于全屏模式时，无法通过 Tab 键将焦点置于整个播放器上
- [Bug 修复][皮肤][实时] 皮肤无法响应日文的本地化 LIVE 文本
- [Bug 修复][皮肤] 当流 > 60 分钟时，持续时间和当前时间被截断 - [Bug 修复][iPhone|实时] 播放器在控制条中显示当前时间/持续时间的文本
- [Bug 修复][AMP] 调用播放器启发式 API 生成 JavaScript 异常
- [Bug 修复][本机 Html5|iOS] Videotag 属性“playsinline”不传播到播放器
- [Bug 修复][iOS|iframe] 如果在 iframe 中加载播放器，播放器无法在 iPhone 上进入全屏模式
- [Bug 修复][AMP][启发] AMP 始终使用混合配置文件来运行，而不管播放器选项如何
- [Bug 修复][AMP|Win8.1] 托管在使用 Web 视图的 Win8.1 应用中时引发

### <a name="changes-213"></a>更改 2.1.3 ###

- [更改][AMP] 在 FragmentDownloadComplete 事件中添加了 CDN 终结点信息
- [更改][AMP][实时] 已改进并优化实时传送视频流延迟

## <a name="212-official-hotfix"></a>2.1.2（正式修补程序） ##

### <a name="bug-fixes-212"></a>Bug 修复 2.1.2 ####

- [Bug 修复][辅助功能][Windows 讲述人] 当用户具有进度条的上下文，且当前时间为 0:00 时，讲述人念出“进度午夜”
- [Bug 修复][皮肤] 徽标大小在 JavaScript 代码中硬编码
- [辅助功能][热键] 单击播放器时不启用热键。

### <a name="changes-212"></a>更改 2.1.2 ####

- [更改][日志记录] 当播放器无法加载清单时记录清单 URL

### <a name="features-212"></a>功能 2.1.2 ###

- [更改][性能][优化] 改进了播放器的加载和启动时间

## <a name="211-official-update"></a>2.1.1（正式更新） ##

### <a name="bug-fixes-211"></a>Bug 修复 2.1.1 ####

- [Bug 修复][iOS] 在 Safari for iOS 中将“自动播放”设置为 false 会生成一直转动的旋转图标
- [Bug 修复] 搜寻到内容持续时间范围外的时间会生成一直旋转的旋转图标
- [Bug 修复] 热键需要多次按键盘上的 Tab 键，才能让播放器的上下文正常工作
- [Bug 修复] 在特定资产中重设播放器大小后，视频冻结几秒钟
- [Bug 修复] 当用户快速进行多次搜寻时，旋转图标一直旋转（搜寻完成后）
- [Bug 修复] 非活动期间不隐藏控制条
- [Bug 修复] 打开托管 AMP 的 Web 应用可能导致网页加载两次
- [Bug 修复] 通过 Flash 技术播放特定资产的内容时，旋转图标一直旋转
- [Bug 修复] 第三方插件不显示“更多选项”菜单
- [Bug 修复][皮肤|Tube][实时] 当播放器位于节目的实时边缘时显示两个实时图标
- [Bug 修复][皮肤] 无法禁用徽标
- [Bug 修复][DD+ 内容] 包含 Dolby Digital 音频轨道的资产显示一直旋转的旋转图标
- [Bug 修复] 在实时传送流期间切换音频语言轨道时，最新的 AMP 冻结
- [Bug 修复] 修复了旋转图标背景消失的问题
- [Bug 修复] 修复了 AES Flash 标记静态示例中旋转图标一直旋转的 bug

### <a name="changes-211"></a>更改 2.1.1 ####

- [更改] 针对 Widevine Https 要求添加了错误代码：从 Chrome v58 起，必须通过 `https://` 协议加载/播放 Widevine 内容，否则播放会失败。
- [更改] 添加了用于加载旋转图标的 aria 标签，使辅助技术能够在加载内容时念出“正在加载视频”  

## <a name="210-official-release"></a>2.1.0（正式发行版） ##

### <a name="features-210"></a>功能 2.1.0 ###

- [功能][AzureHtml5JS] VOD 广告支持 pre- mid- post-rolls
- [功能][Beta][AzureHtml5JS] 实时广告支持 pre- mid- post-rolls
- [功能] 添加了新的皮肤选项 - AMP-flush
- [功能] 添加了改进的 aria 标签，以便与屏幕阅读器/辅助技术更好地集成
- [功能][皮肤] 皮肤现在以高对比度模式清晰显示所有图标和按钮

### <a name="bug-fixes-210"></a>Bug 修复 2.1.0 ###

- [Bug 修复] 辅助功能和 UI 修复的数目
- [Bug 修复] AMP 无法在 IE9 中正常加载

### <a name="changes-210"></a>更改 2.1.0 ###

- [更改] 已重构播放器中的 DOM 元素以配合广告工作
- [更改] 皮肤开发已从 CSS 切换到 SCSS
- [更改][示例] 添加了 VOD 广告的示例
- [更改][示例] 添加了播放速度的示例
- [更改][示例] 添加了刷新皮肤的示例

## <a name="200-beta-release"></a>2.0.0（Beta 版） ##

- [更改] 已更新到 VJS5
- [功能] 添加了新的流畅 API，让播放器流畅响应
- [功能] 播放速度
- [更改] 皮肤已从 CSS 切换到 SCSS

## <a name="183-official-hotfix-update"></a>1.8.3（正式修补程序更新） ##

### <a name="bug-fixes-183"></a>Bug 修复 1.8.3 ###

- [Bug 修复][AzureHtml5JS] 具有负 DTS 的特定资产不在 Chrome 中播放

## <a name="182-official-hotfix-update"></a>1.8.2（正式修补程序更新） ##

### <a name="bug-fixes-182"></a>Bug 修复 1.8.2 ###

- [Bug 修复][AzureHtml5JS] 较高的音频比特率不通过 AzureHtml5JS 播放

## <a name="181-official-update"></a>1.8.1（正式更新） ##

### <a name="bug-fixes-181"></a>Bug 修复 1.8.1 ###

- [Bug 修复][iOS] 本机播放器中不显示隐藏式字幕/对白字幕
- [Bug 修复][AMP] 追加了身份验证令牌的基于 CDN 的流 URL 不播放
- [Bug 修复][FairPlay] FairPlay 错误代码缺失技术 ID（错误代码的 Bits [31-28]）。有关详细信息，请参阅“错误代码”
- [Bug 修复][Safari][PlayReady] Safari 中的 PlayReady 内容生成一直旋转的旋转图标

### <a name="changes-181"></a>更改 1.8.1 ###

- [更改][Html5] 更改本机 Html5 技术详细日志以包含来自 VideoTag 的事件

## <a name="180-official-update"></a>1.8.0（正式更新） ##

### <a name="features-180"></a>功能 1.8.0 ###

- [功能][DRM] 添加了 FairPlay 支持（有关详细信息，请参阅[受保护内容](azure-media-player-protected-content.md)）

### <a name="bug-fixes-180"></a>Bug 修复 1.8.0 ###

- [Bug 修复][AMP] 当网络受到限制时，用户搜寻不触发等待事件
- [Bug 修复][FlashSS] 在 Flash 技术中选择质量会引发异常
- [Bug 修复][AMP] 以动态方式选择质量的操作会显示在上下文菜单中
- [Bug 修复][皮肤] 很难选择上下文菜单的最后一个菜单项

### <a name="changes-180"></a>更改 1.8.0 ###

- [更改] 已根据当前的 Chrome EME 要求更新播放器
- [更改] 默认 techOrder 已更改得适应新技术 - html5FairPlayHLS（有关详细信息，请参阅[受保护内容](azure-media-player-protected-content.md)）
- [更改][AzureHtml5JS] 已在 Safari 中启用 MPEG-Dash 播放
- [更改][示例] 已更改多重 DRM 示例，使之符合 FairPlay 的要求

## <a name="174-official-hotfix-update"></a>1.7.4（正式修补程序更新） ##

### <a name="bug-fixes-174"></a>Bug 修复 1.7.4 ###

- [Bug 修复][Chrome] 当用户具有播放器的上下文时，搜寻控点周围出现蓝色外框
- [Bug 修复][IE9] 在 IE9 中载入播放器时引发 JavaScript 异常

## <a name="173-official-hotfix-update"></a>1.7.3（正式修补程序更新） ##

### <a name="bug-fixes-173"></a>Bug 修复 1.7.3 ###

- [Bug 修复][AzureHtml5JS] 播放器在受限网络中超时

### <a name="changes-173"></a>更改 1.7.3 ###

- [更改] 在 Edge 上启用 Webcrypto 以解密 AES 内容
- [更改] 优化 AMP 启发算法，使之考虑已缓存的区块
- [更改][AzureHtml5JS] 通过减少带宽估算延迟优化启发算法

## <a name="172-official-hotfix-update"></a>1.7.2（正式修补程序更新） ##

### <a name="features-172"></a>功能 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [功能][AzureHtml5JS|Firefox] 针对 Firefox 47+ 启用以 EME 进行 Widevine 播放的功能
- [功能] 添加供播放器处置的事件
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Bug 修复 1.7.2 ###

- [Bug 修复] 不正确解码已编码的 Akamai CDN URL 查询参数
- [Bug 修复] 对 manifestPlayableWindowLength() 引发异常
- [Bug 修复] 结束视频后重新观看时，观看者不一定能在视频中单击“播放”
- [Bug 修复] 响应式大小调整不符合快速更改窗口大小的要求
- [Bug 修复][Edge|IE] 响应式大小调整不在 width=x, height=auto 的页面加载时生效
- [Bug 修复][Android|Chrome] Chrome 在内容未加密时，请求获取播放 DRM 内容的权限
- [Bug 修复][辅助功能][Edge] 键盘控件不正确选择上下文菜单项
- [Bug 修复][辅助功能] 在高对比度模式下缺少显示的边框
- [Bug 修复][FlashSS] 在播放器处置之后未删除鼠标上移事件侦听器导致异常
- [Bug 修复][FlashSS] 分析具有已编码空格的清单 URL 时出现问题
- [Bug 修复][iOS] 评估 tech.featuresVolumeControl 时发生类型错误

### <a name="changes-172"></a>更改 1.7.2 ###

- [更改][DRM] 已将设置源后进行 DRM 检查改为仅在内容已加密时进行检查
- [更改][AES] 已从密钥传送请求中删除未定义的类型/纯文本正文
- [更改][辅助功能] 当上下文在播放器上而不是属性上时，Windows 讲述人现在会念出“Media Player”

## <a name="171-official-hotfix-update"></a>1.7.1（正式修补程序更新） ##

### <a name="features-171"></a>功能 1.7.1 ###

- [功能] 添加了混合启发式配置文件（默认设置此配置文件）的选项

### <a name="bug-fixes-171"></a>Bug 修复 1.7.1 ###

- [Bug 修复] 响应式设计不按照 HTML5 标准（width=100%，height=auto）运行
- [Bug 修复] v1.7.0 中的宽度和高度百分比值的行为不符合预期

## <a name="170-official-update"></a>1.7.0（正式更新） ##

### <a name="features-170"></a>功能 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [功能][AzureHtml5JS][FlashSS] 添加了 currentMediaTime() 以获取当前时间的编码器媒体时间（以秒为单位）
- [功能][FlashSS] 已使用 videoBufferData() 和 audioBufferData() 实现下载遥测 API<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [功能][FlashSS] 添加了“downloadbitratechanged”事件
- [功能] 相比于旧版播放器，加载时间已改善
- [功能] 将错误记录到 JavaScript 控制台

### <a name="bug-fixes-170"></a>Bug 修复 1.7.0 ###

- [Bug 修复] 具有查询字符串参数的已编码海报 URL 不显示在播放器中
- [Bug 修复] 在未加载任何技术的情况下调用 API amp.Player.poster() 时，会引发异常
- [Bug 修复] 当函数尝试在处置后访问播放器时，会引发异常
- [Bug 修复][辅助功能] 将焦点置于进度条搜寻头部时缺少外框
- [Bug 修复][辅助功能] 上下文菜单在高对比度模式下出现阴影
- [Bug 修复][iOS] 本机播放器 WebVTT 字幕播放无法正常进行
- [Bug 修复][AzureHtml5JS] 在 HTTPS 站点上播放 HTTP 流时，本应显示错误 0x0100002，但却由于内容混合在一起而生成一直旋转的旋转图标
- [Bug 修复][AzureHtml5JS] 缺少结束片段，导致循环运行状况检查错误显示可觉察到的无限缓冲状态
- [Bug 修复][AzureHtml5JS] 当 useManifestForLabel=false 并使用三字母语言代码时，菜单中的音频轨道名称不正确
- [Bug 修复][AzureHtml5JS|Chrome] 在 Chrome 中使用 JavaScript 期间，浮点不精确造成内容末尾出现可觉察到的无限缓冲状态
- [Bug 修复][FlashSS] 创建 Flash 播放器时，暂时显示非严重的间歇性错误
- [Bug 修复][FlashSS] 当视频和音频流使用不同的时间刻度时播放失败，原因是发生舍入不精确失败，错误消息为“片段 URL (...)无法生成 FLVTags”
- [Bug 修复][FlashSS] 分析具有已编码空格的清单 URL 时出现问题
- [Bug 修复][FlashSS] 缺少确定 Flash 播放器版本是否 >= 11.4 的检查，这样会导致播放错误，而不是回退到 techOrder 中的下一个技术
- [Bug 修复][FlashSS][AES] 在接受包含下划线的 AES 标记时出现问题
- [Bug 修复][SilverlightSS|OSX] 在清单而不是协议（HTTP 或 HTTPS）前面加上“//”前缀会被识别为本地文件，从而生成一直旋转的旋转图标

### <a name="changes-170"></a>更改 1.7.0 ###

- [更改][FlashSS] 已将 SWF 脚本（“MSAdaptiveStreamingPlugin-osmf2.0.swf”和“StrobeMediaPlayback.2.0.swf”）合并到名为“StrobeMediaPlayback.2.0.swf”的单个 SWF
- [更改][FlashSS] 更新了错误代码传播，以获取更精确的错误代码（例如， 404 现在生成 0x30200194，而不是常规错误 0x30200000）

## <a name="163-official-hotfix-update"></a>1.6.3（正式修补程序更新） ##

### <a name="bug-fixes-163"></a>Bug 修复 1.6.3 ###

- [Bug 修复] 在处置播放器之后执行热键事件处理程序时发生 JavaScript 运行时异常
- [Bug 修复][Android][AzureHtml5JS] 在使用移动电话网络的移动设备上无法播放
- [Bug 修复] 已将 Forge 更新为以 Web 辅助角色的形式运行，以释放 UI

## <a name="162-official-hotfix-update"></a>1.6.2（正式修补程序更新） ##

### <a name="features-162"></a>功能 1.6.2 ###

- [功能] 添加了更多的本地化语言（有关详细信息，请参阅文档）

### <a name="bug-fixes-162"></a>Bug 修复 1.6.2 ###

- [Bug 修复][IE9-10] 由于 IE9/IE10 存在调用 window.blur() 时会执行最小化操作的 bug，单击播放器周围的区域会将浏览器窗口最小化
- [Bug 修复][FlashSS] 不接受包含下划线的 AES 标记

## <a name="161-official-hotfix-update"></a>1.6.1（正式修补程序更新） ##

### <a name="bug-fixes-161"></a>Bug 修复 1.6.1 ###

- [Bug 修复][FlashSS|Edge、IE][SilverlightSS|IE] 无法将焦点置于其他 UI 元素上以在 IE/Edge 中执行输入或其他操作
- [Bug 修复] 当 forge 未定义时，AES 播放失败
- [Bug 修复][Android][AzureHtml5JS|Chrome] 在处于运行状况检查循环中时，旋转图标一直旋转而不播放内容
- [Bug 修复][IE9] IE 9 不支持 console.log() 而导致异常

## <a name="160-official-update"></a>1.6.0（正式更新） ##

### <a name="features-160"></a>功能 1.6.0 ###

- [功能] azuremediaplayer.min.js 的大小缩减 33%
- [功能][AzureHtml5JS|Edge][未测试] 在 Edge 中支持 DD+ 音频流（在初始选择之后，不切换编解码器）。 应用此时必须选择正确的音频流。
- [功能] 热键控件（有关详细信息，请参阅文档）
- [功能] 进度时间提示悬停，以便进行在时间方面很精确的搜寻
- [功能] 如果插件中有 setupDone 方法，允许对插件进行异步检测

### <a name="bug-fixes-160"></a>Bug 修复 1.6.0 ###

- [Bug 修复] 调用 getMemoryLog(true) 时内存日志不刷新
- [Bug 修复] 鼠标移动时比特率选择框重置，导致通过鼠标控件选择较低的比特率时出现问题
- [Bug 修复] 执行 DRM 检查时，应用中的 Mac Office 崩溃
- [Bug 修复] 很容易意外覆盖 CSS 类
- [Bug 修复][Chrome] user-agent 字符串浏览器的更新标识为 Edge
- [Bug 修复][AzureHtml5JS] 字幕按钮未显示在 Edge (Win10) 或 Chrome (Mac) 中的工具栏中
- [Bug 修复][Android][AzureHtml5JS|Chrome] 对短视频调用 endOfStream() 时发生 InvalidStateError 异常
- [Bug 修复][Firefox] 删除了在检查浏览器功能时 Firefox 所导致的 DRM 警告
- [Bug 修复][Html5] 不显示渐进式 mp4 内容的对白字幕/隐藏式字幕
- [Bug 修复][FlashSS] 具有匹配时间戳的消息以倒序记录
- [Bug 修复][辅助功能][Chrome|Firefox] 通过 Tab 键选择控件会自动选择第一个菜单项
- [Bug 修复][辅助功能] 通过 Tab 键控制音量按钮

### <a name="changes-160"></a>更改 1.6.0 ###

- [更改] 选择质量级别时使用 AES 解密时间
- [更改] 更新 URL 重写器，以便对多音频流先使用 HLS v4，再使用 HLS v3
- [更改] 将 nativeControlsForTouch 的默认值设置为 false（必须为 false 才能正常运行）

## <a name="150-official-update"></a>1.5.0（正式更新） ##

### <a name="features-150"></a>功能 1.5.0 ###

- [功能] 增强了常规 Web 安全性（防止注入、XSS 等攻击）
- [功能] 针对 sourceset 事件和 options.sdn 的 SDN 插件集成挂接
- [功能] 对播放期间发生的 5XX 和 4XX 错误的可靠性处理

### <a name="bug-fixes-150"></a>Bug 修复 1.5.0 ###

- [Bug 修复] 将 CSS 最小化更新为对按钮使用 HTML 实体字体代码，而不是 Unicode
- [Bug 修复][AzureHtml5JS] 多重 DRM 内容始终从 protectionInfo 选择第一个元素的标记，导致第二个 DRM 失败
- [Bug 修复][AzureHtml5JS] 搜寻存在缺失片段的区域时，搜寻永远不会完成。
- [Bug 修复][AzureHtml5JS|Edge] 在 PlayReady 播放的 Edge 更新中启用带前缀的 EME
- [Bug 修复][AzureHtml5JS|Firefox] 更新 EME 检查，允许 Firefox v42+（包含 MSE）回退到 Silverlight 来获取受保护内容
- [Bug 修复][FlashSS] 将 error.message 从数字更新为详细字符串

### <a name="changes-150"></a>更改 1.5.0 ###

- [更改] 海报目前只能作为绝对 URL 来使用。

## <a name="140-official-update"></a>1.4.0（正式更新） ##

### <a name="features-140"></a>功能 1.4.0 ###

- [功能][AzureHtml5JS|Chrome] 简单 Widevine DRM 支持
- [功能][AzureHtml5JS] 对播放期间发生的 404/412 错误的可靠性处理

### <a name="bug-fixes-140"></a>Bug 修复 1.4.0 ###

- [Bug 修复][FlashSS] 参数验证增强

## <a name="130-official-update"></a>1.3.0（正式更新） ##

### <a name="features-130"></a>功能 1.3.0 ###

- [功能][AzureHtml5JS][FlashSS] 相同编解码器多音频内容的音频切换

### <a name="bug-fixes-130"></a>Bug 修复 1.3.0 ###

- [Bug 修复][AzureHtml5JS|Chrome] 间歇性显示一直旋转的旋转图标
- [Bug 修复][AzureHtml5JS|IE][Windows Phone] 异常导致 Windows Phone 出现播放问题
- [Bug 修复][FlashSS] 针对其他实例将自动播放设置为 false 失败
- [Bug 修复] UI 菜单大小调整问题

## <a name="120-official-update"></a>1.2.0（正式更新） ##

### <a name="features-120"></a>功能 1.2.0 ###

- [功能][AzureHtml5JS|Firefox] 启用 MSE 时的支持
- [功能] 不再需要应用提供回退技术二进制文件（swf、xap）的路径。 路径是相对于 Azure Media Player 脚本的。

### <a name="bug-fixes-120"></a>Bug 修复 1.2.0 ###

- [Bug 修复][AzureHtml5JS|Chrome] 播放器在后台偏移到实时边缘后面
- [Bug 修复][AzureHtml5JS|Edge] 全屏模式不正常
- [Bug 修复][AzureHtml5JS] 在选项中设置后，日志记录未正确启用
- [Bug 修复][Flash] 在等待事件期间显示“正在缓冲”和缓冲图标
- [Bug 修复] 允许在初始带宽请求失败时继续播放
- [Bug 修复] 使用未定义的选项进行初始化时，播放器无法加载
- [Bug 修复] 在已经处置播放器后，尝试处置该播放器时发生 vdata 异常
- [Bug 修复] 质量栏图标未正确映射

## <a name="111-official-hotfix-update"></a>1.1.1（正式修补程序更新） ##

### <a name="bug-fixes-111"></a>Bug 修复 1.1.1 ###

- [Bug 修复] 旧 IE 全屏问题
- [Bug 修复] 不再覆盖插件

## <a name="110-official-update"></a>1.1.0（正式更新） ##

### <a name="features-110"></a>功能 1.1.0 ###

- [功能] 更新 UI 本地化字符串

### <a name="bug-fixes-110"></a>Bug 修复 1.1.0 ###

- [Bug 修复] 大“播放”按钮的对比度不足
- [Bug 修复] 视觉选项卡焦点指示器
- [Bug 修复] 现在会使用正确的分辨率信息选择“比特率”菜单
- [Bug 修复]“更多选项”菜单现在会动态调整大小
- [Bug 修复] 各种 UI 问题

## <a name="100-official-release"></a>1.0.0（正式发行版） ##

### <a name="features-100"></a>功能 1.0.0 ###

- [功能] Tab 键控件、焦点控件、屏幕阅读器、高对比度 UI 的基本辅助功能测试
- [功能] 更新了 UI
- [功能] 开发日志记录
- [功能] 用于动态设置隐藏字幕/对白字幕轨道的 API
- [功能] 基本的本地化功能
- [功能] 跨技术的错误代码合并
- [功能] 未安装插件（例如 Flash 或 Silverlight）时显示新错误代码
- [功能][AzureHtml5JS] 实现了基本诊断事件

### <a name="bug-fixes-100"></a>Bug 修复 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Bug 修复][AzureHtml5JS] 当时间戳中存在轻微的不精准情况时，实时播放会在 MPD 更新时冻结
- [Bug 修复][AzureHtml5JS] 缓解了多个实时播放问题
- [Bug 修复][AzureHtml5JS] 当窗口大小启发算法启用时刷新缓冲区，并转到分辨率更高的屏幕
- [Bug 修复][AzureHtml5JS] Chrome 现在会正确显示已结束的事件。 已链接到以前的已知问题：“Chrome 在使用 AzureHtml5JS 时不正确地发送已结束的事件。底层浏览器中出现问题。”
- [Bug 修复][AzureHtml5JS] 已针对此技术禁用 Safari，以解决“使用 AzureHtml5JS *技术的 OSX Yosemite 出现播放问题。MSE 存在实现问题。* 临时的风险缓解措施：强制以 flashSS、silverlightSS 作为这些用户代理的技术指令”
- [Bug 修复][FlashSS] 发生错误后触发 loadstart

## <a name="020-beta"></a>0.2.0 (Beta) ##

### <a name="features-020"></a>功能 0.2.0 ###

- [功能] 已针对点播流与实时流的 PlayReady 和 AES 完成测试 - 请参阅兼容性矩阵
- [功能] 处理不连续性
- [功能] 支持大于 2^53 的时间戳
- [功能] URL 查询参数保存到清单请求
- [功能][未测试] 支持 `QuickStart` 和 `HighQuality` 启发算法配置文件
- [功能][未测试] 在 AzureHtml5JS 和 FlashSS 上公开比特率、宽度和高度的视频流信息
- [功能][未测试] 在 AzureHtml5JS 和 FlashSS 上选择比特率（请参阅 API 文档）

### <a name="bug-fixes-020"></a>Bug 修复 0.2.0 ###

- [Bug 修复] 现在可在 WP8.1 上看到大的播放按钮
- [Bug 修复] 修复了多个实时播放问题
- [Bug 修复]“取消静音”按钮现在可在 UI 上正常工作
- [Bug 修复] 已更新自动播放模式的 UI 加载体验
- [Bug 修复] AMD 加载程序问题以及 define 方法冲突
- [Bug 修复] WP 8.1 Cordova 应用加载问题
- [Bug 修复] 受保护内容查询平台/技术支持的 ProtectionType，用于选择适当的播放技术。  修复了以前的已知问题：“在 Chrome（桌面版）/Safari 8（在 OSX Yosemite 上）上的 PlayReady 内容当前不会回退到 Silverlight 播放器”
- [Bug 修复] WinServer 2012 R2 上出现未捕获到的异常，因为该计算机上默认不会安装 Media Foundation。  尝试使用了未实现的 HTML 视频标记 API，因而引发了错误。 当前的缓解措施是捕获该错误并返回 false，而不是引发错误。
- [Bug 修复] 始终在搜寻或发生 http 失败后获取 init 片段，以防止在播放期间出现小问题
- [Bug 修复] 发生错误时关闭跟踪模拟进度和时间更新的功能。
- [Bug 修复] 删除右键菜单
- [Bug 修复][AzureHtml5JS] 针对 PlayReady 内容设置无效的标记时不显示错误消息
- [Bug 修复][AzureHtml5JS] 在实时播放期间进入全屏模式时不考虑窗口大小启发算法
- [Bug 修复][FlashSS] 删除了 Strobe Media Player 显示的消息，只显示 Azure Media Player 消息
- [Bug 修复][SilverlightSS] 搜寻超过持续时间或小于 0 时，不会获得“seeked”事件

## <a name="010-beta-release"></a>0.1.0（Beta 版） ##

初始预发行版

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)

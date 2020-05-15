---
title: Azure Media Player 插件库
description: 本文包含可用于 Azure Media Player 的插件的列表。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: ee29d1c45896583ef5aa5837e8383159db855d52
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124612"
---
# <a name="azure-media-player-plugin-gallery"></a>Azure Media Player 插件库 #

## <a name="plugins"></a>插件 ##

| 插件名称                         | 演示 URL                    | 源代码                | 说明    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| 其他功能                 | | | |
| **新增内容！** AMP360Video                | [演示](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | 此插件用于在 AMP 中可视化 360 视频，不管是在桌面设备上，还是在与 VR 兼容的设备中。 [此处](https://doc\.babylonjs\.com/extensions/amp360video)提供了完整文档： |
|  子画面提示                         | [演示](http://www.skymedia.tv/asset/sprite)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | 此 Azure Media Player (AMP) 插件用于按时间线渲染根据 Azure Media Services (AMS) Media Encoder Standard (MES) 生成的视频缩略图子画面。 |
| 诊断覆盖                 | [演示](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | 此插件显示：所有关键参数、视频统计信息、视频播放生命周期中的所有事件，以及受保护情况下的 DRM 保护信息（例如密钥 ID、许可证获取 URL）。                                                                                                                                                                      |
| 帧速率和时间代码计算器 | [演示](http://mconvertitest001.blob.core.windows.net/public/example.html)                        | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | 此插件基于第一个 MPEG-DASH 视频片段的 `tfhd`/`trun` MP4 框计算视频的帧速率，分析 MPEG-DASH 客户端清单中的时间刻度值，并提供了一种在播放器中根据给定的绝对时间生成时间代码的方法（另外还可根据时间代码提供播放器绝对时间）。 |
| <strike>播放速度</strike>                      | [演示](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | 此插件允许观看者控制视频速度。 注意，此功能在 AMP v2.0.0+ 版本中自动提供，但默认处于禁用状态。  若要了解如何启用它，请查看[此处](https://github.com/Azure-Samples/azure-media-player-samples)提供的示例 |
| 悬停时间提示                      | [演示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | 当鼠标悬停时在进度条上显示时间提示，以便在时间方面进行精确的查找。 注意：  此插件已集成到 AMP 中，但如果你有兴趣查看其编程方式，可以随时查看。                                                                                                                       |
| 标题覆盖                       | [演示](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | 覆盖屏幕上的可配置视频标题 |
| 时间线标记                    | [演示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | 此插件根据一组时间覆盖进度条上相应时间处的小标记。 |
| 分析                           | | | |
| Application Insights                | [博客文章](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | 此插件跟踪播放器指标并将其移植到 Power BI，为观看者的播放器体验提供直观的图形表示形式。 |
| Google Analytics                    | 空值                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | 适用于 Azure Media Player 的 Google Analytics 插件 |
| 诊断                         | | | |
| 诊断输出                  | [演示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | 此插件输出播放器的一组诊断。若要观看操作演示，请转到演示链接并打开 JavaScript 控制台。 |
| 轻松访问                      | | | |
| 放大                             | [演示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | 此插件在播放器屏幕上显示一个可拖动的比例尺，允许观看者放大你的内容 |
| 实时字幕                       | [Azure 博客文章](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/)、[SubPly 文章](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | 不适用 | 有关详细信息，请参阅相关文章。  为 Azure Media Player 的实时字幕生成插件设计了端到端工作流，请单击最左侧的链接以转到 SubPly 的站点，了解有关该解决方案的详细信息 |
| 热键                            | <strike>[演示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | 有了此热键插件，观看者可以通过通用的插件组合来控制播放器的各个方面，例如，F 表示全屏，M 表示静音，箭头键用于进度条控制。 注意：  此插件已集成到 AMP 中，但你可以随意将其作为资源使用 |
| 社交                              | | | |
| 分享                               | [演示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | 此插件向播放器的控制栏添加了一个分享按钮，方便观看者通过 Facebook、Twitter 或 Linkedin 与好友分享观看的视频。 |

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)（Azure Media Player 快速入门）
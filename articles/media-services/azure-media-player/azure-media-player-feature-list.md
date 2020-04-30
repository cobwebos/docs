---
title: Azure Media Player 功能列表
description: Azure Media Player 的功能参考。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727224"
---
# <a name="feature-list"></a>功能列表 #
下面是已测试功能和不支持的功能的列表：

|                                         | 测试 | 部分测试 | 未经测试 | 不支持 | 注释                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| 播放                                |        |                  |          |             |                                                                                                                      |
| 基本点播播放                | X      |                  |          |             | 仅支持来自 Azure 媒体服务的流                                                                      |
| 基本实时播放                     | X      |                  |          |             | 仅支持来自 Azure 媒体服务的流                                                                      |
| AES                                     | X      |                  |          |             | 支持 Azure 媒体服务密钥传送服务                                                                   |
| 多 DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | 支持 Azure 媒体服务密钥传送服务                                                                   |
| Widevine                                |        | X                |          |             | 支持清单中概述的 Widevine PSSH 框                                                                    |
| FairPlay                                |        | X                |          |             | 支持 Azure 媒体服务密钥传送服务                                                                   |
| 方面                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME （AzureHtml5JS）                  | X      |                  |          |             |                                                                                                                      |
| 闪存回退（FlashSS）                | X      |                  |          |             | 并非所有功能都可用于此技术。                                                                         |
| Silverlight 回退 SilverlightSS      | X      |                  |          |             | 并非所有功能都可用于此技术。                                                                         |
| 本机 HLS 传递（Html5）         |        | X                |          |             | 由于平台限制，此技术上不提供所有功能。                                            |
| 功能                                |        |                  |          |             |                                                                                                                      |
| API 支持                             | X      |                  |          |             | 查看已知问题列表                                                                                                |
| 基本 UI                                | X      |                  |          |                                                                                                                                    |
| 通过 JavaScript 初始化       | X      |                  |          |             |                                                                                                                      |
| 通过视频标记进行初始化        |        | X                |          |             |                                                                                                                      |
| 分段寻址-基于时间         | X      |                  |          |             |                                                                                                                      |
| 分段寻址-基于索引        |        |                  |          | X           |                                                                                                                      |
| 分段寻址-基于字节         |        |                  |          | X           |                                                                                                                      |
| Azure 媒体服务 URL 重写工具       |        | X                |          |             |                                                                                                                      |
| 辅助功能-标题和副标题  |        | X                |          |             |  支持按需运行 WebVTT，CEA 708 部分测试                                                       |
| 辅助功能-热键                 | X      |                  |          |             |                                                                                                                      |
| 辅助功能-高对比度           |        | X                |          |             |                                                                                                                      |
| 辅助功能-选项卡焦点               |        | X                |          |             |                                                                                                                      |
| 错误消息                         |        | X                |          |             | 错误消息在方面中不一致                                                                         |
| 事件触发                        | X      |                  |          |             |                                                                                                                      |
| 诊断                             |        | X                |          |             | 诊断信息仅适用于 AzureHtml5JS 技术，在 SilverlightSS 技术上部分可用。 |
| 可自定义的技术订单                 |        | X                |          |             |                                                                                                                      |
| 试探法-基本                      | X      |                  |          |             |                                                                                                                      |
| 试探-自定义              |        |                  | X        |             | 自定义仅适用于 AzureHtml5JS 技术。                                                          |
| 中断                         | X      |                  |          |             |                                                                                                                      |
| 选择比特率                          | X      |                  |          |             | 此 API 仅适用于 AzureHtml5JS 和 FlashSS 方面。                                                    |
| 多音频流                      |        | X                |          |             | AzureHtml5JS 和 FlashSS 方面支持编程音频开关，可通过 AzureHtml5JS、FlashSS 和 native Html5 上的 UI 选择（在 Safari 中）使用。  大多数平台需要相同的编解码器专用数据来切换音频流（同一编解码器、频道、采样率等）。 |
| UI 本地化                         |        | X                |          |             |                                                                                                                      |
| 多实例播放                 |        |                  |          | X           | 这种情况可能适用于一些方面，但目前不受支持且未经测试。 你还可以使用 iframe 实现此操作 |
| 广告支持                             |        | x                |          |             | AMP 支持从适用于 AzureHtml5JS 技术的大型 ad 服务器中插入预发布的线性广告 |
| Analytics                               |        | X                |          |             | AMP 提供侦听分析和诊断事件的功能，以便发送到所选的分析后端。  由于平台限制，所有事件和属性在方面中不可用。                                                                            |
| 自定义外观                            |        |                  | X        |             | 在 AMP 中将控件设置为 false，并使用自己的 HTML 和 CSS。           |
| 搜索栏清理                      |        |                  |          | X           |                                                                                                                      |
| 技巧-游戏                              |        |                  |          | X           |                                                                                                                      |
| 仅音频                              |        |                  |          | X           | 可在某些方面中工作，以实现自适应流式处理，但目前不受支持，并且在 AzureHtml5JS 中不起作用。 如果平台支持 HTML5 技术，则可以使用 HTML5 技术。                                                                                                        |
| 仅视频                              |        |                  |          | X           | 可在某些方面中工作，以实现自适应流式处理，但目前不受支持，并且在 AzureHtml5JS 中不起作用。      |
| 多段演示               |        |                  |          | X                                                                                                                                  |
| 多相机角度                  |        |                  |          | X           |                                                                                                                      |
| 播放速度                          |        | X                |          |             | 大多数情况下都支持播放速度，因为 Chrome 中的部分 bug 除外                 |

## <a name="next-steps"></a>后续步骤 ##
- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
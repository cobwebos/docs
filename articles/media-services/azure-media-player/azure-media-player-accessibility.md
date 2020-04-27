---
title: Azure Media Player 辅助功能
description: 详细了解 Azure Media Player 的辅助功能设置。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727604"
---
# <a name="accessibility"></a>辅助功能 #

Azure Media Player 可以与屏幕阅读器功能（例如 Windows 讲述人和 Apple OSX/iOS VoiceOver）协同工作。 UI 按钮有替代标记，当用户导航到这些替代标记时，屏幕阅读器能够读取这些替代标记。 可以在操作系统级别设置其他配置。

## <a name="captions-and-subtitles"></a>隐藏式字幕和字幕 ##

目前，Azure Media Player 仅支持对 WebVTT 格式的点播活动和采用 CEA 708 的直播活动使用隐藏式字幕。 当前不支持 TTML 格式。 使用隐藏式字幕和字幕，播放器能够满足更多受众（包括听力障碍人士或想用不同语言阅读的人）的需求。 隐藏式字幕还可以提高视频参与度，提高理解力，并使得在声音敏感的环境中（如工作场所）观看视频成为可能。  

## <a name="high-contrast-mode"></a>高对比度模式 ##

Azure Media Player 中的默认 UI 与大多数设备/浏览器的高对比度视图模式兼容。 可以在操作系统级别设置配置。

## <a name="mobility-options"></a>移动性选项 ##

### <a name="tabbing-focus"></a>Tab 键控焦点 ###

Azure Media Player 中可使用按常规 HTML 标准提供的 Tab 键控焦点。 若要启用 Tab 聚焦，必须向 HTML `<video>` 添加 `tabindex=0`（或其他值，如果你了解在 HTML 中如何影响 Tab 次序），如下所示：`<video ... tabindex=0>...</video>`。 在某些平台上，控件的焦点出现的前提是控件可见并且平台支持这些功能。

在启用 Tab 键控焦点后，最终用户可以在不依赖鼠标的情况下高效地导航和控制视频播放器。 通过点击 Tab 按钮并选择 Enter 或空格键，可以导航到每个上下文菜单或可控制的元素。 在上下文菜单上点击 Enter 或空格键会展开该菜单，以便最终用户可以继续通过 Tab 按键来选择菜单项。 在定位到要选择的项的上下文后，再次按 Enter 或空格键来完成选择。

### <a name="hotkeys"></a>热键 ###

Azure Media Player 支持通过键盘热键进行控制。 在 Web 浏览器中，控制基础视频元素的唯一方法是将焦点放在播放器上。 将焦点放在播放器上后，热键即可控制播放器的功能。  下表介绍了各种热键及其关联的行为：

| 热键              | 行为                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | 播放器将进入/退出全屏模式                                  |
| M/m                  | 播放器音量将静音/取消静音                                          |
| 向上和向下箭头    | 播放器音量将增大/减小                                    |
| 向左和向右箭头 | 视频进度条会前进/后退                                  |
| 0、1、2、3、4、5、6、7、8、9  | 视频进度会更改为 0%\- 90%，具体取决于按下的键 |
| 单击操作         | 视频将播放/暂停                                                   |

## <a name="next-steps"></a>后续步骤

<!---Some context for the following links goes here--->
- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
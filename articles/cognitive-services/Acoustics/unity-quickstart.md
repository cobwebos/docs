---
title: 使用 Unity 的项目音响效果快速入门
titlesuffix: Azure Cognitive Services
description: 使用示例内容，在 Unity 中试验项目音响效果设计控件，并部署到 Windows 桌面。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243022"
---
# <a name="project-acoustics-unity-quickstart"></a>项目音响效果 Unity 快速入门
使用 Unity 的项目音响效果示例内容来试验模拟支持的设计控件。

所需软件：
* 用于 Windows 的 [Unity 2018.2 及更高版本](https://unity3d.com)
* [项目音响效果示例内容包](https://www.microsoft.com/download/details.aspx?id=57346)

示例包中包括什么？
* 具有几何、声源和游戏控件的 Unity 场景
* 项目音响效果插件
* 场景的烘培音效资产

## <a name="import-the-sample-package"></a>导入示例包
将示例包导入到新的 Unity 项目。
1. 在 Unity 中，转到“资产” > “导入包” > “自定义包”    。

    ![Unity 导入包选项](media/import-package.png)  

1. 选择“ProjectAcoustics.unitypackage”。 

1. 选择“导入”  按钮，将 Unity 包集成到项目中。  
  
    ![Unity 导入包对话框](media/import-dialog.png)  

若要将包导入到现有项目中，请参阅 [Unity 集成](unity-integration.md)以获取其他步骤和说明。

>[!NOTE]
>导入完成后，控制台日志中会显示几个错误消息。 继续执行下一步并重启 Unity。

## <a name="restart-unity"></a>重启 Unity
音效工具包的制作部分需要 .NET 4.*x* 脚本编写运行时版本。 包导入更新 Unity 播放器设置。 重启 Unity，使此设置生效。 若要验证设置是否已生效，请打开“播放器”设置  ：

![“Unity 项目设置”菜单](media/player-settings.png)  

![选中了 .NET 4.x 的 Unity 播放器设置面板](media/net45.png)  

>[!NOTE]
>此屏幕截图取自 Unity 2018.*x*。 此图在更新版的 Unity 中可能有所不同。

## <a name="open-the-project-acoustics-bake-window"></a>打开项目音响效果烘焙窗口
在 Unity 的“窗口”菜单上选择“音效”。  

![Unity 编辑器，其中突出显示了“窗口”菜单中的“音效”选项](media/window-acoustics.png)

将打开一个浮动的“音效”窗口。  在此窗口中设置音响效果模拟属性。

![打开了“音响效果”窗口的 Unity 编辑器](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>试验设计控件
打开 ProjectAcousticsSample 文件夹中的示例场景，然后选择 Unity 编辑器中的“播放”按钮  。 使用 W、A、S、D 键和鼠标进行移动。 若要比较有音响效果和没有音响效果的场景声音，请选择 R 键，直到覆盖文本变成红色，并显示“音响效果:已禁用”。 若要查看更多控件的键盘快捷方式，请选择 F1。 还可以通过右键单击选择一项操作，然后通过左键单击执行该操作。

AcousticsAdjust 脚本附加到示例场景中的声音源  。 它允许按源划分的设计参数。

![Unity AcousticsAdjust 脚本](media/acoustics-adjust.png)

以下部分介绍一些可以通过可用控件创建的效果。 有关每个控件的详细信息，请参阅[项目音响效果 Unity 设计教程](unity-workflow.md)。

### <a name="modify-distance-based-attenuation"></a>修改基于距离的衰减
项目音响效果 Unity 空间音响插件中的音频数字信号处理遵守内置于 Unity 编辑器的基于距离的按源衰减  。 针对基于距离的衰减的控件位于“三维声音设置”下的声源“检查器”面板的“音频源”组件中    ：

![Unity 距离衰减选项面板](media/distance-attenuation.png)

项目音响效果在以播放器位置为中心的“模拟区域”框中计算。 示例包中的音效资产烘焙的模拟区域大小为播放器周围 45 米。 因此，声音衰减应设计为在约 45 米处降至 0。

### <a name="modify-occlusion-and-transmission"></a>修改闭塞和传输
* 如果“闭塞”乘数大于 1（默认值为 1），闭塞会扩大  。 若要使闭塞效果更精细，请将其设置为小于 1。

* 若要启用穿墙传输，请将“传输(dB)”滑块从最低设置移开  。

### <a name="modify-wetness-for-a-source"></a>修改源湿度
* 若要更改湿度随距离变化的速度，请使用“可感知距离弯曲”  。 “项目音响效果”通过模拟计算湿度级别，湿度级别提供可感知的距离提示，并会随距离平稳变化。 增加距离弯曲会通过增加与距离相关的湿度级别来扩大这种效果。 如果弯曲值小于 1，基于距离的混响变化会更加精细。

   若要对此效果进行更精细的调整，请更改“湿度(dB)”设置。 

* 若要增加整个空间的衰减时间，请调整“衰减时间刻度”  。 如果特定源侦听器位置对的模拟结果是 1.5 秒的衰减时间，并且“衰减时间刻度”设置为 2，则应用于源的衰减时间为 3 秒  。

## <a name="next-steps"></a>后续步骤
* 阅读[基于 Unity 的项目音响效果设计控件](unity-workflow.md)的详细信息。
* 进一步探索[设计过程](design-process.md)中的概念。
* [创建 Azure 帐户](create-azure-account.md)以了解预先烘焙和烘焙过程。

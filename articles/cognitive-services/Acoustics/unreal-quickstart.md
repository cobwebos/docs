---
title: 使用 Unreal 的项目音响效果快速入门
titlesuffix: Azure Cognitive Services
description: 使用示例内容，在 Unreal 和 Wwise 中试验项目音响效果设计控件，并部署到 Windows 桌面。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242925"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>项目音响效果 Unreal/Wwise 快速入门
在本快速入门中，你将使用 Unreal Engine 和 Wwise 的示例内容来试验项目音响效果设计控件。

使用示例内容时的软件要求：
* [Unreal Engine](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>下载示例包
下载[项目音响效果 Unreal 和 Wwise 示例包](https://www.microsoft.com/download/details.aspx?id=58090)。 该示例包包含：
- Unreal Engine 项目
- Unreal 项目的 Wwise 项目
- 项目音响效果 Wwise 插件

## <a name="set-up-the-project-acoustics-sample-project"></a>设置项目音响效果示例项目
首先，将项目音响效果插件安装到 Wwise 中。 接下来，将 Wwise 二进制文件部署到 Unreal 项目。 然后，调整 Wwise Unreal 插件以支持项目音响效果。

### <a name="install-the-project-acoustics-wwise-plug-in"></a>安装项目音响效果 Wwise 插件
打开 Wwise Launcher。 在“插件”选项卡上的“安装新插件”下，选择“从目录添加”。    选择已下载的包中包含的 *AcousticsWwisePlugin\ProjectAcoustics* 目录。

![用于在 Wwise Launcher 中安装 Wwise 插件的选项](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>将 Wwise 二进制文件添加到项目音响效果 Unreal 示例项目
1. 在 Wwise Launcher 中选择“Unreal Engine”选项卡。  
1. 选择“最近的 Unreal Engine 项目”旁边的汉堡包（图标）菜单，然后选择“浏览项目”。   打开包 *AcousticsSample\AcousticsGame\AcousticsGame.uproject* 中的示例 Unreal 项目 *.uproject* 文件。

   ![Wwise Launcher 中的“Unreal”选项卡](media/wwise-unreal-tab.png)

3. 在项目音响效果示例项目旁边，选择“在项目中集成 Wwise”  。

   ![Wwise Launcher 将显示 Acoustics Game Unreal 项目，其中突出显示了“集成”选项。](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>扩展 Wwise Unreal 插件功能
项目音响效果 Unreal 插件需要从 Wwise Unreal 插件 API 中公开其他行为。 运行项目音响效果 Unreal 插件随附的批处理文件以自动执行这些修改。
* 在 *AcousticsGame\Plugins\ProjectAcoustics\Resources* 中运行 *PatchWwise.bat*。

    ![Windows 资源管理器窗口中突出显示的用于修补 Wwise 项目的脚本](media/patch-wwise-script.png)

* 如果尚未安装 DirectX SDK：根据所用的 Wwise 版本，可能需要在 *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* 中注释掉包含 `DXSDK_DIR` 的行：

    ![显示已注释掉“DXSDK”的代码编辑器](media/directx-sdk-comment.png)

* 如果使用 Visual Studio 2019 进行编译：若要解决 Wwise 的链接错误，请将 *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* 中的默认值 *VSVersion* 手动更改为 **vc150**：

    ![代码编辑器，其中的 VSVersion 已更改为“vc150”](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>打开 Unreal 项目 
打开 Unreal 项目时，它会提示你重新生成模块。 请选择“是”。 

如果因生成失败而无法打开项目，请检查是否已将项目音响效果 Wwise 插件安装到项目音响效果示例项目中使用的相同版本的 Wwise。

如果使用的 [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 版本低于2019.1，则无法使用项目音响效果示例项目生成声音库。 需要将 Wwise 版本 2019.1 集成到示例项目中。

## <a name="experiment-with-project-acoustics-design-controls"></a>试验项目音响效果设计控件
选择 Unreal 编辑器中的播放按钮，聆听场景声效。 使用 W、A、S、D 键和鼠标进行移动。 若要查看更多控件的键盘快捷方式，请选择 F1。

以下信息描述了可尝试的一些设计活动。

### <a name="modify-occlusion-and-transmission"></a>修改闭塞和传输
每个 Unreal 声音执行组件都有基于源的项目音响效果设计控件。

![Unreal 编辑器音效设计控件](media/demo-scene-sound-source-design-controls.png)

如果“闭塞”乘数大于 1（默认值为 1），闭塞会扩大  。 将其设置为小于 1 会使闭塞效果更精细。

若要启用穿墙传输，请将“传输 (dB)”滑块从最低级别移开  。

### <a name="modify-wetness-for-a-source"></a>修改源湿度
若要更改湿度随距离变化的速度，请使用“可感知距离弯曲”  。 项目音响效果通过模拟计算整个空间内的湿度水平。 这些水平相对于距离平稳变化，提供可感知距离提示。若要放大此效果，请增大距离相关的湿度水平以增大距离弯曲量。 如果弯曲值小于 1，基于距离的混响变化会更加精细。 还可以通过“湿度(dB)”设置对此效果进行更精细的调整。 

若要增加整个空间的衰减时间，请调整“衰减时间刻度”  。 请考虑模拟结果是 1.5 秒衰减时间的情况。 将“衰减时间刻度”设置为 2 会对源应用衰减时间 3 秒。 

### <a name="modify-distance-based-attenuation"></a>修改基于距离的衰减
项目音响效果 Wwise 混音器插件遵循 Wwise 内置的基于距离的按源衰减。 更改此曲线将改变干路径级别。 项目音响效果插件将调整湿度级别以保持模拟和设计控件指定的湿/干混合。

![Wwise 衰减曲线面板，显示模拟边界之前的衰减即将达到 0](media/demo-sounds-attenuation.png)

项目音响效果在以每个模拟播放器位置为中心的“模拟区域”框中计算。 示例包中的音效资产已通过半径为 45 米的模拟区域进行烘培。 衰减在 45 米之前已降到 0。 虽然这种衰减并不是严格要求，但需要注意的是，只有侦听器 45 米内的几何形状才能闭塞声音。

## <a name="next-steps"></a>后续步骤
* [将项目音响效果插件集成](unreal-integration.md)到 Unreal 项目。
* 为自己的烘培[创建 Azure 帐户](create-azure-account.md)。

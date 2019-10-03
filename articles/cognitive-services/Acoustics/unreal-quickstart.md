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
ms.openlocfilehash: 927ce403130460c302f546038ff3a0c3a16e0368
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933020"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>项目音响效果 Unreal/Wwise 快速入门
在本快速入门中，将使用为 Unreal Engine 和 Wwise 提供的示例内容来试验项目音响效果设计控件。

使用示例内容时的软件要求：
* [Unreal Engine](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>下载示例包
下载[项目音响效果 Unreal + Wwise 示例包](https://www.microsoft.com/download/details.aspx?id=58090)。 示例包中包含 Unreal Engine 项目、该 Unreal 项目的 Wwise 项目以及项目音响效果 Wwise 插件。

## <a name="set-up-the-project-acoustics-sample-project"></a>设置项目音响效果示例项目
若要设置项目音响效果 Unreal/Wwise示例项目，需要先将项目音响效果插件安装到 Wwise 中。 然后将 Wwise 二进制文件部署到 Unreal 项目，并调整 Wwise 的 Unreal 插件以支持项目音响效果。

### <a name="install-the-project-acoustics-wwise-plugin"></a>安装项目音响效果 Wwise 插件
打开 Wwise Launcher，然后在“插件”选项卡的“安装新插件”下，选择“从目录添加”    。 选择下载的包中包含的 `AcousticsWwisePlugin\ProjectAcoustics` 目录。

![显示安装 Wwise 插件选项的 Wwise Launcher 的屏幕截图](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>将 Wwise 二进制文件添加到项目音响效果 Unreal 示例项目
在 Wwise Launcher 中，单击“Unreal Engine”选项卡，单击“最近的 Unreal Engine 项目”旁边的汉堡菜单，然后选择“浏览项目”    。 打开包 `AcousticsSample\AcousticsGame\AcousticsGame.uproject` 中的示例 Unreal 项目 `.uproject` 文件。

![Wwise Launcher Unreal 选项卡的屏幕截图](media/wwise-unreal-tab.png)

然后，在项目音响效果示例项目旁边，单击“在项目中集成 Wwise”  。

![Wwise Launcher 的屏幕截图，其中显示音效游戏 Unreal 项目](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>扩展 Wwise 的 Unreal 插件功能
项目音响效果 Unreal 插件需要从 Wwise Unreal 插件 API 中公开其他行为。 运行项目音响效果 Unreal 插件提供的批处理文件以自动执行这些修改：
* 在 `AcousticsGame\Plugins\ProjectAcoustics\Resources` 内，运行 `PatchWwise.bat`。

    ![Windows 资源管理器窗口的屏幕截图，其中显示修补 Wwise 项目的脚本](media/patch-wwise-script.png)

* 如果没有安装 DirectX SDK，则根据所用的 Wwise 版本，可能需要在 `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` 中注释掉包含 `DXSDK_DIR` 的行：

    ![显示注释掉 DXSDK 的代码编辑器的屏幕截图](media/directx-sdk-comment.png)

* 如果使用 Visual Studio 2019 进行编译，要解决 Wwise 的链接错误，请手动将 `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` 中的默认 `VSVersion` 值编辑为 `vc150`：

    ![显示 VSVersion 已更改为 vc150 的代码编辑器的屏幕截图](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>打开 Unreal 项目。 
它会要求你重新生成模块；单击“是”。

>如果因生成故障打开项目失败，请检查是否已将项目音响效果 Wwise 插件安装到项目音响效果示例项目中使用的相同版本的 Wwise。

>如果使用早于 2019.1 的 [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 版本，将无法使用项目音响效果示例项目生成声音库。  需要将 Wwise 版本 2019.1 集成到示例项目中才能正常使用。

## <a name="experiment-with-project-acoustics-design-controls"></a>试验项目音响效果设计控件
通过单击 Unreal 编辑器中的播放按钮，侦听场景的声音效果。 在桌面上，使用 W、A、S、D 和鼠标进行移动。 若要查看更多控件的键盘快捷方式，请按 **F1**。 下面是一些要尝试的设计活动：

### <a name="modify-occlusion-and-transmission"></a>修改闭塞和传输
每个 Unreal 声音参与者都有基于源的项目音响效果设计控件：

![Unreal 编辑器音效设计控件的屏幕截图](media/demo-scene-sound-source-design-controls.png)

如果“闭塞”乘数大于 1（默认值为 1），闭塞将被扩大  。 将其设置为小于 1 会使闭塞效果更精细。

若要启用穿墙传输，请将“传输 (dB)”滑块从最低级别移开  。 

### <a name="modify-wetness-for-a-source"></a>修改源湿度
若要更改湿度随距离变化的速度，请使用“可感知距离弯曲”  。 “项目音响效果”通过模拟计算整个空间的湿度级别，湿度级别会随距离平稳变化，并提供可感知的距离提示。增加距离弯曲会通过增加与距离相关的湿度级别来扩大这种效果。 如果弯曲值小于 1，基于距离的混响变化会更加精细。 通过调整“湿度 (dB)”，还可以在更精细的细节中调整此效果  。

通过调整“衰减时间刻度”来增加整个空间的衰减时间  。 请考虑模拟结果是 1.5 秒衰减时间的情况。 将“衰减时间刻度”设置为 2 将导致应用于源的衰减时间为 3 秒  。

### <a name="modify-distance-based-attenuation"></a>修改基于距离的衰减
项目音响效果 Wwise 混音器插件遵循 Wwise 内置的基于距离的按源衰减。 更改此曲线将改变干路径级别。 项目音响效果插件将调整湿度级别以保持模拟和设计控件指定的湿干混合。

![模拟边界前衰减到零的 Wwise 衰减曲线面板的屏幕截图](media/demo-sounds-attenuation.png)

项目音响效果在以每个模拟播放器位置为中心的“模拟区域”框中进行计算。 示例包中的音效资产烘焙的模拟区域半径为 45 米，衰减设计为在 45 米之前降至 0。 虽然这种衰减并不是严格要求，但需要注意的是，只有侦听器 45 米内的几何形状才能闭塞声音。

## <a name="next-steps"></a>后续步骤
* [将项目音响效果](unreal-integration.md)插件集成到 Unreal 项目
* 为自己的制作[创建 Azure 帐户](create-azure-account.md)



---
title: 项目噪声 Unreal 和 Wwise 集成
titlesuffix: Azure Cognitive Services
description: 本操作指南介绍了与你的项目的项目噪声 Unreal 和 Wwise 插件集成。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 0baaf31386e1155dee6ca2bbfda6827ca3fc36fe
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313441"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>项目噪声 Unreal 和 Wwise 集成
本指南提供了现有的 Unreal 和 Wwise 游戏项目到项目噪声插件包的详细的集成步骤。 

软件要求：
* [Unreal 引擎](https://www.unrealengine.com/)4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/)了对 2018.1。 +
* [Unreal 的 Wwise 插件](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * 如果使用的 Wwise SDK 的直接集成而不使用 Wwise Unreal 插件，请查阅项目噪声 Unreal 插件并调整 Wwise API 调用。

如果你想要用于音频引擎而不是 Wwise 项目噪声，请联系我们上[项目噪声论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)。 项目噪声 Unreal 插件可用于查询噪声数据，然后进行到引擎的 API 调用。

## <a name="download-project-acoustics"></a>下载项目噪声
如果你尚未下载[Unreal 项目噪声和 Wwise 插件包](https://www.microsoft.com/download/details.aspx?id=58090))。 

在包中，我们已添加 Unreal Engine 插件和 Wwise 混音器插件。 Unreal 插件提供编辑器和运行时集成。 在玩游戏，期间项目噪声 Unreal 插件的每个帧计算参数，例如为每个游戏对象的阻挡物。 这些参数将转换为 Wwise API 调用。

## <a name="review-integration-steps"></a>检查集成步骤

有以下主要步骤来安装包并将其部署在游戏中。
1. 安装项目噪声 Wwise 混音器插件
2. （重新） 将部署到您的游戏的 Wwise。 此步骤将传播到游戏项目的混音器插件。
3. 将项目噪声 Unreal 插件添加到您的游戏
4. 扩展 Wwise 的 Unreal 插件功能
5. 构建游戏并检查已启用 Python
6. 设置要使用项目噪声 Wwise 项目
7. Unreal 中的音频设置

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1.安装项目噪声混音器插件
* 然后在中打开 Wwise 启动器**插件**选项卡上，在**安装新插件**，选择**从目录添加**。 

    ![安装插件 Wwise 启动器中的屏幕截图](media/wwise-install-new-plugin.png)

* 选择`AcousticsWwisePlugin\ProjectAcoustics`下载的包中包含的目录。 它包含 Wwise mixer 插件捆绑包。

* Wwise 将安装该插件。 项目噪声现在应显示在 Wwise 中安装了的插件列表中。
![屏幕截图的 Wwise 项目噪声安装之后安装了插件列表](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2.（重新） 将 Wwise 部署到您的游戏
即使已整合了 Wwise 重新部署 Wwise 到您的游戏。 这将提取项目噪声 Wwise 插件。

* **引擎插件：** 如果必须 Wwise 作为 Unreal c + + 项目中的游戏插件安装，请跳过此步骤。 如果安装改成作为引擎插件实例，由于 Unreal 项目是使用我们 mixer 插件蓝图仅限 Wwise 部署已更复杂。 创建虚拟的空 Unreal c + + 项目、 Unreal 编辑器随即打开，如果将其关闭并按照部署到此虚拟项目 Wwise 的剩余步骤。 然后将复制出的已部署的 Wwise 插件。
 
* 从 Wwise 启动器，单击**Unreal Engine**选项卡，然后单击汉堡菜单旁边**最近 Unreal 引擎项目**，然后选择**浏览项目**。 打开您的游戏 Unreal 项目`.uproject`文件。

    ![屏幕截图的 Wwise 启动器的 Unreal 选项卡](media/wwise-unreal-tab.png)

* 然后单击**项目中集成 Wwise**或**项目中修改 Wwise**。 此步骤 （重新） 集成到你的项目，现在包括项目噪声 mixer 插件 Wwise 二进制文件。

* **引擎插件：** 如果使用 Wwise 作为引擎插件并创建虚拟项目按上面所述复制文件夹 Wwise 部署：`[DummyUProject]\Plugins\Wwise`并将其粘贴到`[UESource]\Engine\Plugins\Wwise`。 `[DummyUProject]` 是空的 Unreal c + + 项目路径和`[UESource]`是您必须安装的 Unreal 引擎源。 一旦你完成了复制，可以删除 dummy 项目。

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3.将项目噪声 Unreal 插件添加到您的游戏
 
* 复制`Unreal\ProjectAcoustics`文件夹中的插件包并创建一个新文件夹`[UProjectDir]\Plugins\ProjectAcoustics`，其中`UProjectDir`您的游戏项目文件夹包含`.uproject`文件。
  * **引擎插件**:如果使用 Wwise 作为引擎插件，您应该使用项目噪声作为一个的 Unreal 引擎插件。 而不是上述的目标目录中，使用： `[UESource]\Engine\Plugins\ProjectAcoustics`。

* 确认你看到`Wwise`文件夹旁边`ProjectAcoustics`文件夹。 它包含在上面的步骤 2 中您重新部署该混音器插件的二进制文件以及 Wwise 插件。

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4.扩展 Wwise 的 Unreal 插件功能
* 项目噪声 Unreal 插件需要其他行为从每个 Wwise Unreal 插件 API 公开[这些准则](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)。 我们提供了一个批处理文件来自动执行修补过程。 
* 内部`Plugins\ProjectAcoustics\Resources`，请运行`PatchWwise.bat`。 以下示例图使用我们 AcousticsGame 示例项目。

    ![资源管理器屏幕截图的 Windows 窗口突出显示提供修补 Wwise 脚本](media/patch-wwise-script.png)

* 如果没有安装 DirectX SDK，将需要进行注释掉包含 DXSDK_DIR 中的行 `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![代码编辑器，显示 DXSDK 注释掉的屏幕截图](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5.构建游戏并检查已启用 Python

* 编译您的游戏，并确保正确生成。 否则，请继续操作之前请仔细前面的步骤。 
* 在 Unreal 编辑器中打开你的项目。 
* **引擎插件：** 如果使用 ProjectAcoustics 作为引擎插件，还要确保它已启用，下列出"内置"插件。
* 应会看到新的模式，指示已集成项目噪声。

    ![屏幕截图的 Unreal 显示噪声模式完整](media/acoustics-mode-full.png)

* 确认你有 Python 插件的 Unreal 启用。 这是必需的编辑器集成才能正常工作。

    ![启用 Python 扩展 Unreal 编辑器中的屏幕截图](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6.Wwise 项目安装程序

在示例下载中包含示例 Wwise 项目。 我们建议查看它与这些说明。 下面的屏幕截图取自此项目。

### <a name="bus-setup"></a>总线安装程序
* 项目噪声 Unreal 插件将查找与此总线上的关联的 mixer 插件***确切***名称： `Project Acoustics Bus`。 具有此名称创建新的音频总线。 Mixer 插件可以在不同的配置，但现在，我们假定将用于仅回响处理。 此总线将附带使用噪声的所有源的混合的混响信号。 它可以混合使用上游到混合使用结构任何总线，示例如下所示，从我们的示例下载中包含的 Wwise 示例项目。

    ![显示项目噪声总线的屏幕截图的 Wwise 总线](media/acoustics-bus.png)

* 必须设置为一个的总线上的通道配置： `1.0, 2.0, 4.0, 5.1 or 7.1`。 其他配置将导致此总线上没有输出。

    ![为项目噪声总线通道配置选项的屏幕截图](media/acoustics-bus-channel-config.png)

* 现在，转到项目噪声总线的详细信息，并确保您可以看到 Mixer 插件选项卡

    ![显示如何启用 Mixer 插件选项卡为项目噪声总线 Wwise 的屏幕截图](media/mixer-tab-enable.png)

* 然后转到 Mixer 插件选项卡，将项目噪声 mixer 插件添加到总线

    ![显示如何添加项目噪声 Mixer 插件 Wwise 如总线](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>执行组件 mixer 层次结构设置
* 出于性能原因，项目噪声应用于音频 DSP 所有源同时。 这需要插件作为混音器插件。 Wwise 必须要在输出总线上进行的混音器插件，但输出总线通常携带 dry 输出信号。 项目噪声需要当湿的信号传输时通过 aux 总线路由 dry 信号`Project Acoustics Bus`。 以下过程支持逐步迁移到此信号流。

* 假设你有现有的项目包含效仿、 武器和其他人在最上面的执行组件 mixer 层次结构。 每个都有相应的输出总线对于其 dry 组合。 假设您要迁移效仿使用噪声允许。 首先创建相应的 aux 总线来执行其 dry submix 效仿输出总线的子级。 例如，我们使用"Dry"前缀在下图中来组织这些数据，尽管的确切名称并不重要。 任何计量器或必须效仿总线的效果将仍像以前一样正常。

    ![建议 Wwise Dry 组合安装程序的屏幕截图](media/wwise-dry-mix-setup.png)

* 然后，如下所示修改效仿 actor mixer 总线输出结构，用于将设置为输出总线和 Dry_Footsteps 项目噪声总线设置作为用户定义 aux 总线。

    ![建议 Wwise Actor Mixer 总线安装程序的屏幕截图](media/actor-mixer-bus-settings.png)

* 现在所有效仿获取噪声处理方式，并输出其混响项目噪声总线上。 Dry 的信号是通过 Dry_Footsteps 路由和 spatialized 像往常一样。

* 项目噪声仅适用于世界上有一个三维位置的声音。 遵循[Wwise 文档](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)、 定位属性必须按照所示设置。 根据需要可以是"位置"或"位置 + 方向"的"3D Spatialization"设置。

    ![定位建议 Wwise 执行组件的设置的屏幕截图](media/wwise-positioning.png)

* 将输出总线设置为上游到混合使用某些其他总线**项目噪声总线**不起作用。 Wwise 实施此要求在混音器插件。

* 如果你想要不使用噪声效仿 actor mixer 层次结构中的子项，您可以始终使用其上的"替代父"选择退出。

* 如果您正在使用在游戏中的任何参与者 mixer 混响游戏或用户定义的发送，那些上关闭此执行组件 mixer 避免两次应用混响。

### <a name="spatialization"></a>Spatialization
默认情况下，项目噪声 Wwise 混音器插件应用卷积混响，离开 Wwise 执行平移 spatialization。 在这种默认仅混响配置中使用项目噪声时, 您可以随意 dry 混合，对使用任何通道配置和 spatialization 方法允许您混合和匹配项目噪声混响使用几乎任何 spatializer。 您的选择包括[Ambisonics 基于双耳 spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/)并[Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)。
 
项目噪声包括支持基于对象的高分辨率 HRTF 呈现和平移可选 spatializer。 选中"执行 Spatialization"复选框在 mixer 插件设置，并选择 HRTF 或移动或禁用设置上述到所有 dry 总线以避免两次，spatializing 项目噪声 mixer 插件和 Wwise 的用户定义 aux 发送。 不能实时更改 spatialization 模式，因为它需要声音银行重新生成。 必须重新启动 Unreal，然后重新 soundbanks 生成达到 play 选取混音器插件配置更改，例如执行 Spatialization 复选框之前。

![Wwise Mixer 插件 Spatialization 屏幕快照设置](media/mixer-spatial-settings.png)

遗憾的是，其他对象基于 spatializer 插件不支持这一次它们并作为混音器插件、 实现和 Wwise 目前也不允许分配给单个执行组件的混音器的多个混音器插件。  

## <a name="7-audio-setup-in-unreal"></a>7.Unreal 中的音频设置
* 首先您将需要制作您的游戏级别以生成将放入的噪声资产`Content\Acoustics`。 请查阅[Unreal 制作教程](unreal-baking.md)和此处继续。 示例包中包含一些预先生成的级别。
* 在您的场景中创建噪声空间执行组件。 仅这些参与者之一的级别中创建因为它表示整个级别噪声。 

    ![屏幕截图的 Unreal 编辑器，显示的噪声空间执行组件的创建](media/create-acoustics-space.png)

* 现在将生成的声学数据资产分配给噪声空间执行组件的噪声数据槽。 您的场景现在具有噪声 ！

    ![屏幕截图的 Unreal 编辑器 s howing 噪声资产分配](media/acoustics-asset-assign.png)

* 现在添加空的执行组件，执行以下操作：

    ![空执行组件中显示噪声组件使用情况的屏幕截图的 Unreal 编辑器](media/acoustics-component-usage.png)

1. 将噪声音频组件添加到执行组件。 此组件的项目噪声功能扩展 Wwise 音频组件。
2. 默认情况下，这将触发上使用级别启动的关联的 Wwise 事件检查开始框上的播放。
3. 使用显示噪声参数复选框屏幕上打印调试信息源。
    ![使用调试值启用声音源上的 Unreal 屏幕截图编辑器噪声面板](media/debug-values.png)
4. 分配每个常用 Wwise 工作流的 Wwise 事件
5. 请确保使用空间音频处于关闭状态。 在此期间，如果使用项目噪声进行特定的音频组件，不能同时用于 Wwise 的空间音频引擎噪声。

已经完成全部设置。 场景中移动和浏览声学效果 ！

## <a name="next-steps"></a>后续步骤
* [设计](unreal-workflow.md)教程，了解在 Unreal/Wwise 项目噪声
* [了解如何执行烘焙](unreal-baking.md)游戏场景的 

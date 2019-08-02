---
title: 项目噪声 Unreal 和 Wwise 集成
titlesuffix: Azure Cognitive Services
description: 本操作指南介绍项目中的 Unreal 和 Wwise 插件与项目的集成。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 5511dd6b9a7d77c0988a94fef747a30d25bb4fc3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706628"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>项目噪声 Unreal 和 Wwise 集成
本操作说明提供项目噪声插件包到现有 Unreal 和 Wwise 游戏项目的详细集成步骤。 

所需软件：
* [Unreal 引擎](https://www.unrealengine.com/)4.20 或4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1。\*
* [适用于 Unreal 的 Wwise 插件](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * 如果你使用的是 Wwise SDK 的直接集成, 而不是使用 Wwise Unreal 插件, 请参阅项目噪声 Unreal 插件并调整 Wwise API 调用。

如果要将项目噪声与 Wwise 以外的音频引擎一起使用, 请在[项目噪声论坛](https://github.com/microsoft/ProjectAcoustics/issues)上发出增强请求。 您可以使用项目噪声 Unreal 插件来查询噪声数据, 然后对您的引擎进行 API 调用。

## <a name="download-project-acoustics"></a>下载项目噪声
如果尚未这样做, 请下载[项目噪声 Unreal & Wwise 插件包](https://www.microsoft.com/download/details.aspx?id=58090))。 

我们在包中包含了 Unreal 引擎插件和 Wwise 混音器插件。 Unreal 插件提供编辑器和运行时集成。 在游戏期间, 项目噪声 Unreal 插件将为每个帧的每个游戏对象计算参数, 如封闭。 这些参数转换为 Wwise API 调用。

## <a name="review-integration-steps"></a>查看集成步骤

安装包并将其部署到游戏中主要有以下步骤。
1. 安装项目噪声 Wwise 混音器插件
2. (重新) 将 Wwise 部署到游戏。 此步骤将混音器插件传播到游戏项目。
3. 将项目噪声 Unreal 插件添加到游戏
4. 扩展 Wwise 的 Unreal 插件功能
5. 生成游戏和检查 Python 已启用
6. 将 Wwise 项目设置为使用项目噪声
7. Unreal 中的音频设置

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1.安装项目噪声混音器插件
* 打开 Wwise Launcher，然后在“插件”选项卡的“安装新插件”下，选择“从目录添加”。 

    ![在 Wwise 启动器中安装插件的屏幕截图](media/wwise-install-new-plugin.png)

* 选择下载的包中包含的 `AcousticsWwisePlugin\ProjectAcoustics` 目录。 它包含 Wwise 混音器插件捆绑包。

* Wwise 将安装该插件。 项目噪声现在应显示在 Wwise 的 "已安装的插件" 列表中。
![项目噪声安装后 Wwise 安装的插件列表的屏幕截图](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2.(重新) 将 Wwise 部署到游戏中
即使已集成 Wwise, 也会将 Wwise 重新部署到游戏。 这会选取项目噪声 Wwise 插件。

* **引擎插件:** 如果已将 Wwise 作为游戏插件安装在 Unreal C++项目中, 请跳过此步骤。 例如, 如果已将它作为引擎插件安装, 例如, 由于 Unreal 项目只是蓝图, 使用混音器插件的 Wwise 部署更复杂。 创建一个虚拟的空 Unreal C++项目, 在打开 Unreal 编辑器时关闭该项目, 然后按照剩余过程将 Wwise 部署到此虚拟项目。 然后复制已部署的 Wwise 插件。
 
* 在 Wwise Launcher 中，单击“Unreal Engine”选项卡，单击“最近的 Unreal Engine 项目”旁边的汉堡菜单，然后选择“浏览项目”。 打开游戏的 Unreal 项目`.uproject`文件。

    ![Wwise 启动程序的 "Unreal" 选项卡的屏幕截图](media/wwise-unreal-tab.png)

* 然后单击 "**在项目中集成 Wwise** " 或 "**修改项目中的 Wwise"** 。 此步骤 (re) 将 Wwise 二进制文件集成到你的项目中, 现在包括项目噪声混音器插件。

* **引擎插件:** 如果使用 Wwise 作为引擎插件并创建了如上所示的虚拟项目, 请复制 Wwise 部署`[DummyUProject]\Plugins\Wwise`的文件夹, 并将其粘贴到`[UESource]\Engine\Plugins\Wwise`其中。 `[DummyUProject]`是空的 Unreal C++项目路径, 并且`[UESource]`是安装了 Unreal 引擎源的位置。 完成复制后, 可以删除虚拟项目。

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3.将项目噪声 Unreal 插件添加到游戏
 
* 复制插件`Unreal\ProjectAcoustics`包中的文件夹并创建一个新文件夹`[UProjectDir]\Plugins\ProjectAcoustics`, 其中`UProjectDir`是包含该`.uproject`文件的游戏项目文件夹。
  * **引擎插件**:如果使用 Wwise 作为引擎插件, 还应使用项目噪声作为 Unreal 引擎插件。 使用: `[UESource]\Engine\Plugins\ProjectAcoustics`, 而不是上面的目标目录。

* 确认文件夹与`ProjectAcoustics`文件夹`Wwise`一起出现。 它包含 Wwise 插件以及您在上面步骤2中部署的混音器插件的二进制文件。

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4.扩展 Wwise 的 Unreal 插件功能
* 项目噪声 Unreal 插件要求根据[这些准则](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html), 从 Wwise UNREAL 插件 API 公开其他行为。 我们提供了一个批处理文件来自动完成修补过程。 
* 在 `Plugins\ProjectAcoustics\Resources` 内，运行 `PatchWwise.bat`。 下面的示例图像使用我们的 AcousticsGame 示例项目。

    ![Windows 资源管理器窗口的屏幕截图突出显示提供的脚本以修补 Wwise](media/patch-wwise-script.png)

* 如果没有安装 DirectX SDK，则需要在 `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` 中注释掉包含 DXSDK_DIR 的行

    ![显示注释掉 DXSDK 的代码编辑器的屏幕截图](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5.生成游戏和检查 Python 已启用

* 编译您的游戏并确保其正确生成。 否则, 请仔细检查前面的步骤, 然后再继续。 
* 在 Unreal 编辑器中打开项目。 
* **引擎插件:** 如果使用 ProjectAcoustics 作为引擎插件, 还应确保它已启用, 并且在 "内置" 插件下列出。
* 应该会看到一个新模式, 指示项目噪声已集成。

    ![显示噪声模式已满的 Unreal 屏幕截图](media/acoustics-mode-full.png)

* 确认已启用适用于 Unreal 的 Python 插件。 这是编辑器集成正常运行所必需的。

    ![在 Unreal 编辑器中启用 Python 扩展的屏幕截图](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6.Wwise 项目设置

示例下载中包含了一个示例 Wwise 项目。 建议在这些说明中查看。 下面的屏幕截图取自此项目。

### <a name="bus-setup"></a>总线设置
* 项目噪声 Unreal 插件将在具有此***确切***名称的总线上查找关联的混音器插件: `Project Acoustics Bus`。 使用此名称创建新的音频总线。 混音器插件可以使用各种配置, 但现在我们假设它仅用于执行回音处理。 此总线将为使用噪声的所有源传输混合回音信号。 它可以将上游混合为任何总线混合结构, 如下所示, 从示例下载中包含的 Wwise 示例项目中获取。

    ![显示项目噪声总线的 Wwise 总线的屏幕截图](media/acoustics-bus.png)

* 总线上的通道配置需要设置为以下其中一项: `1.0, 2.0, 4.0, 5.1 or 7.1`。 其他配置将导致此总线上无输出。

    ![适用于项目噪声总线的通道配置选项的屏幕截图](media/acoustics-bus-channel-config.png)

* 现在, 请进入项目噪声总线详细信息, 并确保可以看到 "混音器插件" 选项卡

    ![Wwise 的屏幕截图, 显示如何启用项目噪声总线的 "混音器插件" 选项卡](media/mixer-tab-enable.png)

* 然后, 中转到 "混音器插件" 选项卡, 并将项目噪声混音器插件添加到总线

    ![Screenshow 的 Wwise 总线显示如何添加项目噪声混音器插件](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>执行组件-混合器层次结构设置
* 出于性能原因, 项目噪声同时向所有源应用音频 DSP。 这要求插件作为混音器插件运行。 Wwise 要求将混音器插件置于输出总线上, 尽管输出总线通常携带干燥输出信号。 项目噪声要求在上`Project Acoustics Bus`携带湿信号时, 通过 aux 总线来路由晾干信号。 以下过程支持逐步迁移到此信号流。

* 假设你有一个现有项目, 其中包含一个包含效仿、武器和其他顶级的参与者-混合器层次结构。 每个都有相应的输出总线用于其干布。 假设要将效仿迁移为使用噪声。 首先, 创建相应的 aux 总线来携带其干燥的 submix, 这是效仿输出总线的子项。 例如, 在下图中, 我们使用了 "干燥" 前缀来组织这些名称, 尽管确切名称并不重要。 效仿总线上的任何计量或效果仍将像以前一样工作。

    ![建议的 Wwise 晾干混合安装程序的屏幕截图](media/wwise-dry-mix-setup.png)

* 然后, 按如下所示修改效仿执行组件-混合器的总线输出结构, 并将项目噪声总线设置为输出总线, 并将 Dry_Footsteps 设置为用户定义的 aux 总线。

    ![建议的 Wwise 执行组件混音总线设置的屏幕截图](media/actor-mixer-bus-settings.png)

* 现在, 所有效仿都可以获得噪声处理并在项目噪声总线上输出其回音。 通常, 使用 Dry_Footsteps 和 spatialized 来路由晾干信号。

* 项目噪声仅适用于在世界各地具有3D 位置的声音。 按照[Wwise 文档](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)中的说明, 必须按如下所示设置定位属性。 "3D Spatialization" 设置可以是 "位置" 或 "位置 + 方向" (根据需要)。

    ![建议的 Wwise 参与者定位设置的屏幕截图](media/wwise-positioning.png)

* 将输出总线设置为将上游混合到**项目噪声总线**的其他总线将不起作用。 Wwise 在混音器插件上施加这一要求。

* 如果希望在效仿的使用者-混音器层次结构中的某个子节点不使用噪声, 则可以始终在其上使用 "替代父项" 来选择它。

* 如果在游戏中的任何执行组件上使用游戏或用户定义的回响来发送回音, 请在此执行组件混音器上将其关闭, 以避免应用回音两次。

### <a name="spatialization"></a>Spatialization
默认情况下, 项目噪声 Wwise 混音器插件会应用卷积回音, 使 Wwise 能够执行平移 spatialization。 如果在此默认的 "仅回音" 配置中使用项目噪声, 则可以自由地在您的干布上使用任何通道配置和 spatialization 方法, 使您可以混合和匹配几乎任何 spatializer 与项目噪声的回音。 选项包括[基于 Ambisonics 的 binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/)和[Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)。
 
项目噪声包括一个可选的 spatializer, 它支持基于对象的高分辨率 HRTF 渲染和平移。 选中 "混音器插件" 设置中的 "执行 Spatialization" 复选框, 并在 HRTF 或平移之间进行选择, 并禁用用户定义的 aux 发送到所有干燥总线, 以避免 spatializing 两次, 同时提供项目噪声混音器插件和 Wwise。 不能实时更改 spatialization 模式, 因为它需要重新生成声音银行。 你必须重新启动 Unreal, 然后在点击 "播放" 之前重新生成 soundbanks, 以选取混音器插件配置更改, 如 "执行 Spatialization" 复选框。

![Wwise 混音器插件 Spatialization 设置的屏幕截图](media/mixer-spatial-settings.png)

遗憾的是, 目前不支持其他基于对象的 spatializer 插件, 因为它们作为混音器插件实现, 并且 Wwise 目前不允许将多个混音器插件分配给单个执行组件。  

## <a name="7-audio-setup-in-unreal"></a>7.Unreal 中的音频设置
* 首先, 您需要制作您的游戏级别, 生成一个将放`Content\Acoustics`入的 "噪声资产"。 请参阅[Unreal 制作教程](unreal-baking.md)并在此处继续。 示例包中包含某些预融入级别。
* 在场景中创建一个噪声空间参与者。 仅在一个级别中创建这些参与者之一, 因为它表示整个级别的噪声。 

    ![显示创建噪声空间参与者的 Unreal 编辑器屏幕截图](media/create-acoustics-space.png)

* 现在, 将融入声音数据资产分配到 "噪声空间" 执行组件上的噪声数据槽。 你的场景现在已有噪声!

    ![Unreal editor howing 的 "噪声资产分配" 的屏幕截图](media/acoustics-asset-assign.png)

* 现在, 添加一个空参与者并执行以下操作:

    ![Unreal 编辑器的屏幕截图, 显示空参与者中的噪声组件用法](media/acoustics-component-usage.png)

1. 向执行组件添加一个噪声音频组件。 此组件扩展 Wwise 音频组件的功能, 以用于项目噪声。
2. 默认情况下, "开始播放" 框处于选中状态, 这将触发级别启动上关联的 Wwise 事件。
3. 使用 "显示噪声参数" 复选框可以打印有关源的屏幕上的调试信息。
    ![启用了调试值的声音源上 Unreal 编辑器噪声面板的屏幕截图](media/debug-values.png)
4. 为每个常见的 Wwise 工作流分配 Wwise 事件
5. 确保已关闭 "使用空间音频"。 此时, 如果对特定音频组件使用项目噪声, 则不能同时使用 Wwise 的空间音频引擎来进行噪声。

已经完成全部设置。 四处移动并浏览声音效果!

## <a name="next-steps"></a>后续步骤
* Unreal/Wwise 中的项目噪声[设计](unreal-workflow.md)教程
* [了解如何](unreal-baking.md)为游戏场景执行烘焙 

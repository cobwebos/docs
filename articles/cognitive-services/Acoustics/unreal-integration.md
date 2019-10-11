---
title: 项目噪声 Unreal 和 Wwise 集成
titlesuffix: Azure Cognitive Services
description: 本文介绍项目中的 "噪声 Unreal" 和 "Wwise" 插件的集成。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243061"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>项目噪声 Unreal 和 Wwise 集成
本文介绍如何将项目噪声插件包集成到现有的 Unreal 和 Wwise 游戏项目。

所需软件：
* [Unreal 引擎](https://www.unrealengine.com/)4.20 +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018。1
* [用于 Unreal 的 Wwise 插件](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  如果你使用的是 Wwise SDK 的直接集成，而不是 Wwise Unreal 插件，请参阅项目噪声 Unreal 插件并调整 Wwise API 调用。

若要将项目噪声与 Wwise 以外的音频引擎一起使用，请在项目的[噪声论坛](https://github.com/microsoft/ProjectAcoustics/issues)中发出增强请求。 您可以使用项目噪声 Unreal 插件来查询噪声数据，并对您的引擎进行 API 调用。

## <a name="download-project-acoustics"></a>下载项目噪声
下载[项目噪声 Unreal 和 Wwise 插件包（](https://www.microsoft.com/download/details.aspx?id=58090)如果尚未这样做）。

包中包含 Unreal 引擎插件和 Wwise 混音器插件。 Unreal 插件提供编辑器和运行时集成。 在游戏期间，项目噪声 Unreal 插件计算每个帧的每个游戏对象的参数，如封闭。 这些参数转换为 Wwise API 调用。

## <a name="integration-steps"></a>集成步骤

按照以下步骤安装包并将其部署到游戏中。

### <a name="install-the-project-acoustics-mixer-plug-in"></a>安装项目噪声混音器插件
1. 打开 Wwise 启动器。 在 "**插件**" 选项卡上的 "**安装新插件**" 下，选择 "**从目录添加**"。

    ![在 Wwise 启动器中安装插件](media/wwise-install-new-plugin.png)

1. 选择下载包中的*AcousticsWwisePlugin\ProjectAcoustics*目录。 它包含 Wwise 混音器插件捆绑包。

   Wwise 将安装该插件。 项目噪声应该出现在 Wwise 中的 "已安装的插件" 列表中。  
![项目噪声安装后的 Wwise 已安装插件列表](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Dedeploy Wwise 游戏
即使已集成 Wwise，也会将 Wwise 重新部署到游戏中。 此步骤集成项目噪声 Wwise 插件。

   > [!NOTE]
   > **引擎插件：** 如果已将 Wwise 作为游戏插件安装在 Unreal C++项目中，请跳过此步骤。 例如，如果已将它作为引擎插件安装，例如，由于 Unreal 项目只是蓝图，使用混音器插件的 Wwise 部署更复杂。 创建虚拟空的 Unreal C++项目。 关闭 "Unreal 编辑器" （如果它打开），并按照剩余步骤将 Wwise 部署到虚拟项目。 然后，复制已部署的 Wwise 插件。
 
1. 在 Wwise 启动器中，选择 " **Unreal 引擎**" 选项卡。选择 "**最近的 Unreal 引擎项目**" 旁边的 "汉堡" （图标）菜单，然后选择 "**浏览项目**"。 打开游戏的*Unreal 文件。*

    ![Wwise 启动器 Unreal 选项卡](media/wwise-unreal-tab.png)

1. 选择 **"在项目中集成 Wwise"** 或 **"修改项目中的 Wwise"** 。 此步骤将 Wwise 二进制文件集成到项目中，包括项目噪声混音器插件。

   > [!NOTE]
   > **引擎插件：** 如果使用 Wwise 作为引擎插件，并按前面所述创建了虚拟项目，请复制 Wwise 部署的文件夹： *[DummyUProject] \Plugins\Wwise*。 将其粘贴到 *[UESource] \Engine\Plugins\Wwise*。 *[DummyUProject]* 是空的 Unreal C++项目路径， *[UESource]* 是安装 Unreal 引擎源的位置。 复制文件夹后，可以删除虚拟项目。

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>将项目噪声 Unreal 插件添加到游戏
 
1. 复制插件包中的*Unreal\ProjectAcoustics*文件夹。 创建新文件夹 *[UProjectDir] \Plugins\ProjectAcoustics*，其中 *[UProjectDir]* 是游戏的项目文件夹，其中包含*uproject*文件。

   > [!NOTE]
   > **引擎插件**：如果使用 Wwise 作为引擎插件，则还应将项目噪声用作 Unreal 引擎插件。 请使用 *[UESource] \Engine\Plugins\ProjectAcoustics*，而不是前面提到的目标目录。

1. 确认*Wwise*文件夹和*ProjectAcoustics*文件夹一起出现。 它包含先前部署的混音器插件的 Wwise 插件和二进制文件。

### <a name="extend-wwise-unreal-plug-in-functionality"></a>扩展 Wwise Unreal 插件功能
项目噪声 Unreal 插件根据[这些准则](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)，需要从 Wwise UNREAL 插件 API 公开的其他行为。 我们提供了一个批处理文件来自动完成修补过程。

* 在*Plugins\ProjectAcoustics\Resources*中，运行*PatchWwise*。 下面的示例图像使用我们的 AcousticsGame 示例项目。

    ![突出显示了用于修补 Wwise 的脚本的 Windows 资源管理器窗口](media/patch-wwise-script.png)

* 如果尚未安装 DirectX SDK：根据你所使用的 Wwise 的版本，你可能需要在*AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*中注释掉包含 `DXSDK_DIR` 的行：

    ![显示 "DXSDK" 注释掉的代码编辑器](media/directx-sdk-comment.png)

* 如果使用 Visual Studio 2019 进行编译：若要解决与 Wwise 的链接错误，请手动将*AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*中的默认 @no__t 值更改为**microsoft.vc150**：

    ![显示 "VSVersion" 的代码编辑器已更改为 "microsoft.vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>构建游戏并检查是否已启用 Python

1. 编译您的游戏，并确保其正确生成。 如果未生成，请仔细检查前面的步骤，然后再继续。

1. 在 Unreal 编辑器中打开项目。

    > [!NOTE]
    > **引擎插件：** 如果使用 ProjectAcoustics 作为引擎插件，还应确保在 "内置" 插件下启用它。

    应该会看到一个新模式，指示项目噪声已集成。

    ![Unreal 中的噪声模式已满](media/acoustics-mode-full.png)

1. 确认已启用适用于 Unreal 的 Python 插件，以便编辑器集成正常工作。

    ![已启用 Unreal 编辑器中的 Python 扩展](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>将 Wwise 项目设置为使用项目噪声

示例下载中包含了一个示例 Wwise 项目。 建议你查看并查看这些说明。 本文后面的屏幕截图来自此项目。

#### <a name="bus-setup"></a>总线设置
项目噪声 Unreal 插件将在名称 @no__t 为0的总线上查找关联的混音器插件。 创建一个具有相同名称的新音频总线。 混音器插件可在各种配置中工作。 但现在，我们假定它仅用于回音处理。 此总线将为使用噪声的所有源传输混合回音信号。 它可以将上游混合到任何总线混合结构。 此处显示的示例来自示例下载中包含的 Wwise 示例项目。

![显示项目噪声总线的 Wwise 总线](media/acoustics-bus.png)

1. 将总线上的通道配置设置为*1.0*、 *2.0*、 *4.0*、 *5.1*或*7.1*。 任何其他设置将导致总线上无输出。

    ![项目噪声总线的通道配置选项](media/acoustics-bus-channel-config.png)

1. 进入项目噪声总线详细信息，并确保可以看到 "**混音器插件**" 选项卡。

    ![启用项目噪声总线的 "混音器插件" 选项卡的 Wwise](media/mixer-tab-enable.png)

1. 中转到 "**混音器插件**" 选项卡，并将项目噪声混音器插件添加到总线。

    ![如何将项目噪声混音器插件添加到 Wwise 总线的示意图](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>执行组件-混合器层次结构设置
为了获得最佳性能，项目噪声同时对所有源应用音频数字信号处理。 因此，该插件必须作为混音器插件运行。 Wwise 要求将混音器插件置于输出总线上，尽管输出总线通常携带干燥输出信号。 项目噪声要求在 "`Project Acoustics Bus`" 上携带湿信号，而将湿信号通过 aux 总线进行路由。 以下过程支持逐步迁移到此信号流。

假设你有一个现有项目，其中包含一个包含*效仿*、*武器*和其他级别的参与者-混合器层次结构。 每个都有一个对应的输出总线用于其干布。 假设要将效仿迁移为使用噪声。 首先，创建相应的 aux 总线来携带效仿输出总线的子 submix。 例如，使用下图中的 "干燥" 前缀来组织总线，尽管确切名称并不重要。 效仿总线上的任何计量或效果仍将像以前一样工作。

![建议的 Wwise 晾干混合设置](media/wwise-dry-mix-setup.png)

接下来，按如下所示修改效仿执行组件-混音器的总线输出结构，并将*项目噪声总线*设置为**输出总线**，并将*Dry_Footsteps*设置为用户定义的 aux 总线。

![建议的 Wwise 执行组件混音总线设置](media/actor-mixer-bus-settings.png)

现在，所有效仿都可以获得噪声处理并在项目噪声总线上输出其回音。 通常，使用 Dry_Footsteps 和 spatialized 来路由晾干信号。

项目噪声仅适用于在世界各地具有3D 位置的声音。 按照[Wwise 文档](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)中的说明，必须按如下所示设置定位属性。 根据需要， **3D Spatialization**设置可以是*位置*或*位置 + 方向*。

![建议的 Wwise 参与者定位设置](media/wwise-positioning.png)

不能将**输出总线**设置为将上游混合到*项目噪声总线*的其他总线。 Wwise 在混音器插件上施加这一要求。

如果希望在效仿的使用者-混合器层次结构中的子节点不使用噪声，可以在其上使用 "替代父项" 来选择它。

如果在游戏中的任何执行组件上使用游戏定义的或用户定义的回响来发送回音，请在该执行组件混音器上将其关闭，以避免应用两次回音。

#### <a name="spatialization"></a>Spatialization
默认情况下，"项目噪声 Wwise 混音器" 插件应用卷 如果在此默认的 "仅回音" 配置中使用项目噪声，则可以对干布使用任意通道配置和 spatialization 方法。 因此，你可以使用项目噪声回音来混合和匹配几乎所有 spatializer。 选项包括[基于 Ambisonics 的 binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/)和[Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)。
 
项目噪声包含一个可选的 spatializer，它支持基于对象的高分辨率 HRTF 渲染和平移。 选中 "混音器插件" 设置中的 "**执行 Spatialization** " 复选框，并选择 " *HRTF* " 或 "*平移*"。 另外，通过项目噪声混音器插件和 Wwise，禁用用户定义的 aux 发送到所有干燥的总线，以避免 spatializing 两次。 不能实时更改 spatialization 模式，因为它需要重新生成声音银行。 重新启动 Unreal，然后在选择 "播放" 以集成混音器插件配置更改（如 "**执行 Spatialization** " 复选框设置）之前，重新生成 soundbanks。

![Wwise 混音器插件 Spatialization 设置](media/mixer-spatial-settings.png)

遗憾的是，目前不支持其他基于对象的 spatializer 插件。 它们是作为混音器插件实现的，Wwise 不允许将多个混音器插件分配给单个执行组件混音器。  

### <a name="audio-setup-in-unreal"></a>Unreal 中的音频设置
1. 首先，需要制作游戏级别，以生成将在*Content\Acoustics*中放置的 "噪声资产"。 请参阅[Unreal 制作教程](unreal-baking.md)。 示例包中包含某些预融入级别。

1. 在场景中创建一个噪声空间参与者。 仅在一个级别中创建这些参与者之一，因为它表示整个级别的噪声。

    ![在 Unreal 编辑器中创建一个噪声空间参与者](media/create-acoustics-space.png)

1. 将融入声音数据资产分配到 "噪声空间" 执行组件上的噪声数据槽。 你的场景现在已有噪声！

    ![Unreal 编辑器中的 "噪声资产分配"](media/acoustics-asset-assign.png)

1. 添加一个空参与者。 按如下所示对其进行配置。

    ![Unreal 编辑器在空的参与者中显示噪声组件的使用情况](media/acoustics-component-usage.png)

       
    <sup>1</sup>将向执行组件添加一个噪声音频组件。 此组件将项目噪声功能添加到 Wwise 音频组件。
        
    <sup>2</sup>默认情况下，"**开始播放**" 框处于选中状态。 此设置在级别启动时触发关联的 Wwise 事件。</li>
         
    <sup>3</sup>使用 "**显示噪声参数**" 复选框可以打印有关源的屏幕上的调试信息。

    ![启用了调试值的声音源上的 Unreal 编辑器噪音面板](media/debug-values.png)

    <sup>4</sup>为每个常见的 Wwise 工作流分配一个 Wwise 事件。
       
    <sup>5</sup>确保已关闭 "**使用空间音频**"。 如果对特定音频组件使用项目噪声，则不能同时使用 Wwise 的空间音频引擎来进行噪声。</li>
       
已经完成全部设置。 四处移动并浏览声音效果！

## <a name="next-steps"></a>后续步骤
* 尝试[项目噪声 Unreal/Wwise 设计教程](unreal-workflow.md)。
* 了解如何在游戏场景中[执行烘焙](unreal-baking.md)。

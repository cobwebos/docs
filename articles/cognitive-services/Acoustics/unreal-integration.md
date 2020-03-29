---
title: 项目声学虚幻和 Wwise 集成
titlesuffix: Azure Cognitive Services
description: 本文介绍了项目声学虚幻和 Wwise 插件集成到项目中。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243061"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>项目声学虚幻和 Wwise 集成
本文介绍如何将项目声学插件包集成到现有的虚幻游戏和 Wwise 游戏项目中。

所需软件：
* [虚幻引擎](https://www.unrealengine.com/)4.20°
* [音频动力学 Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [虚幻插件](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  如果您使用的是 Wwise SDK 的直接集成，而不是 Wwise 虚幻插件，请参阅项目声学虚幻插件并调整 Wwise API 调用。

要将项目声学与 Wwise 以外的音频引擎一起使用，请在[项目声学论坛](https://github.com/microsoft/ProjectAcoustics/issues)中提出增强请求。 您可以使用"项目声学虚幻插件"查询声学数据，并对引擎进行 API 调用。

## <a name="download-project-acoustics"></a>下载项目声学
如果您尚未下载["项目声学虚幻"和"Wwise"插件包](https://www.microsoft.com/download/details.aspx?id=58090)。

包装中包括虚幻引擎插件和 Wwise 混合器插件。 虚幻插件提供编辑器和运行时集成。 在游戏过程中，项目声学虚幻插件计算参数，如每个帧的每个游戏对象的遮挡。 这些参数被转换为 Wwise API 调用。

## <a name="integration-steps"></a>集成步骤

按照以下步骤安装包并将其部署到游戏中。

### <a name="install-the-project-acoustics-mixer-plug-in"></a>安装项目声学混合器插件
1. 打开Wwise发射器。 在**插件**选项卡上，在 **"安装新插件**"下，选择 **"从目录添加**"。

    ![在 Wwise 发射器中安装插件](media/wwise-install-new-plugin.png)

1. 选择下载包中的*声学WwisePlugin_Project声学目录*。 它包含 Wwise 混合器插件捆绑包。

   Wwise 将安装该插件。 项目声学应出现在 Wwise 中已安装的插件列表中。  
![项目声学安装后 Wwise 安装的插件列表](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>将 Wwise 部署到您的游戏中
重新部署 Wwise 到您的游戏，即使您已经集成了 Wwise。 此步骤集成了项目声学 Wwise 插件。

   > [!NOTE]
   > **发动机插件：** 如果您在虚幻C++项目中将 Wwise 作为游戏插件安装，请跳过此步骤。 如果它作为引擎插件安装，例如，由于您的虚幻项目仅蓝图，使用混合器插件进行 Wwise 部署则更为复杂。 创建虚拟空虚C++项目。 如果虚幻编辑器打开，请关闭该编辑器，然后按照其余过程将 Wwise 部署到虚拟项目中。 然后，复制已部署的 Wwise 插件。
 
1. 从 Wwise 启动器中，选择**虚幻引擎**选项卡。选择 **"最近虚幻引擎项目**"旁边的"汉堡包"（图标）菜单，然后选择 **"浏览项目**"。 打开游戏的虚幻项目 *.project*文件。

    ![Wwise 启动器虚幻选项卡](media/wwise-unreal-tab.png)

1. 选择"**在项目中集成 Wwise"** 或在**项目中修改 Wwise。** 此步骤将 Wwise 二进制文件集成到您的项目中，包括项目声学混合器插件。

   > [!NOTE]
   > **发动机插件：** 如果您使用 Wwise 作为引擎插件，并且创建了虚拟项目，请复制 Wwise 部署的文件夹 *：[DummyUProject]_Plugins_Wwise*。 粘贴在 *[UESource]\引擎\插件\Wwise*。 *[DummyUProject]* 是空的虚C++项目路径 *，[UESource]* 是安装虚幻引擎源的位置。 复制文件夹后，可以删除虚拟项目。

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>将项目声学虚幻插件添加到您的游戏
 
1. 复制插件包中的*虚幻\项目声学*文件夹。 创建新文件夹 *[UProjectDir]_Plugins_ProjectAcoustics*，其中 *[UProjectDir]* 是包含 *.uproject*文件的游戏项目文件夹。

   > [!NOTE]
   > **发动机插件**：如果您使用 Wwise 作为发动机插件，则还应使用"项目声学"作为虚幻引擎插件。 使用 *[UESource]\引擎\插件\项目声学*，而不是前面引用的目标目录。

1. 确认在 *"项目声学"* 文件夹旁边看到*Wwise*文件夹。 它包含 Wwise 插件以及您前面部署的混合器插件的二进制文件。

### <a name="extend-wwise-unreal-plug-in-functionality"></a>扩展 Wwise Unreal 插件功能
项目声学虚幻插件需要根据[这些准则](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)从 Wwise 虚幻插件 API 中公开的其他行为。 我们已包含一个批处理文件，用于自动执行修补过程。

* 插件内部*\项目声学\资源*，运行*PatchWwise.bat*。 以下示例图像使用我们的声学游戏示例项目。

    ![具有修补 Wwise 突出显示的脚本的 Windows 资源管理器窗口](media/patch-wwise-script.png)

* 如果您没有安装 DirectX SDK：根据您使用的 Wwise 版本，您可能需要注释掉`DXSDK_DIR`*声学游戏_Plugins_Wwise_Source_AkAudio_AkAudio.build.cs*中包含的行：

    ![显示"DXSDK"的代码编辑器注释了](media/directx-sdk-comment.png)

* 如果使用 Visual Studio 2019 编译：要解决 Wwise 的链接错误，请手动更改`VSVersion`*声学游戏_Plugins_Wwise_Source_AkAudio_AkAudio.Build.cs*到**vc150 中的**默认值：

    ![显示"VSVersion"的代码编辑器更改为"vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>构建游戏并检查 Python 是否已启用

1. 编译您的游戏，并确保它构建正确。 如果无法生成，请在继续之前仔细检查前面的步骤。

1. 在虚幻编辑器中打开项目。

    > [!NOTE]
    > **发动机插件：** 如果您使用 ProjectAcoustics 作为引擎插件，请确保它在"内置"插件下启用。

    您应该会看到一个新模式，该模式表示项目声学已集成。

    ![声学模式在虚幻中充满](media/acoustics-mode-full.png)

1. 确认启用了虚幻的 Python 插件，以便编辑器集成正常工作。

    ![启用虚幻编辑器中的 Python 扩展](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>设置 Wwise 项目以使用项目声学

示例下载中包括 Wwise 项目。 我们建议您查看它以及这些说明。 本文后面的屏幕截图来自此项目。

#### <a name="bus-setup"></a>总线设置
项目声学虚幻插件将查找总线上具有确切名称`Project Acoustics Bus`的相关混合器插件。 创建具有相同名称的新音频总线。 混合器插件可在各种配置下工作。 但目前，我们假设它将仅用于混响处理。 此总线将携带使用声学的所有源的混合混响信号。 它可以将上游混合到任何总线混合结构中。 示例下载中包括的 Wwise 示例项目在此处显示了一个示例。

![显示项目声学巴士的 Wwise 巴士](media/acoustics-bus.png)

1. 将总线上的通道配置设置为*1.0*、2.0、4.0、5.1 或*2.0* *7.1*。 *4.0* *5.1* 任何其他设置都将导致总线上没有输出。

    ![项目声学总线的通道配置选项](media/acoustics-bus-channel-config.png)

1. 进入项目声学总线详细信息，并确保您可以看到**混合器插件**选项卡。

    ![启用了"项目声学总线的混音器插件"选项卡](media/mixer-tab-enable.png)

1. 转到**混合器插件**选项卡，并将项目声学混合器插件添加到总线。

    ![如何将项目声学混合器插件添加到 Wwise 总线的图表](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>参与者-混合器层次结构设置
为了获得最佳性能，项目声学将音频数字信号处理同时应用于所有源。 因此，插件必须作为混合器插件运行。 Wwise 要求混合器插件位于输出总线上，尽管输出总线通常带有干输出信号。 项目声学要求干信号通过辅助总线路由，而湿信号则在 上进行`Project Acoustics Bus`。 以下过程支持逐渐迁移到此信号流。

假设你有一个现有的项目，一个演员-混音器层次结构，其中包含*脚步*，*武器*，和其他在顶层。 每个都有相应的输出总线，用于其干混合。 假设您要迁移足迹以使用声学。 首先，创建相应的辅助总线来承载作为足迹输出总线子级的干子混合。 例如，我们使用下图中的"Dry"前缀来组织总线，尽管确切名称并不重要。 你对脚步巴士的任何仪表或效果仍将像以前那样起作用。

![推荐的 Wwise 干混合设置](media/wwise-dry-mix-setup.png)

接下来，修改足迹参与者-混合器的总线输出结构，如下所示，*将项目声学总线*设置为**输出总线***，Dry_Footsteps*设置为用户定义的辅助总线。

![推荐的 Wwise 演员混合总线设置](media/actor-mixer-bus-settings.png)

现在，所有的脚步声都得到声学处理，并在项目声学总线上输出其混响。 干信号通过Dry_Footsteps路由，并像往常一样进行空间化。

"声学项目"仅适用于世界上具有 3D 位置的声音。 在[Wwise 文档](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)之后，必须按所示设置定位属性。 **3D 空间化**设置可以根据需要*定位*或*位置 + 方向*。

![推荐的 Wwise 参与者定位设置](media/wwise-positioning.png)

你不能将**输出总线**设置为其他总线，这些总线会混入项目*声学总线*。 Wwise 对混合器插件施加此要求。

如果希望足迹角色混合器层次结构中的儿童不使用声学，则可以使用"覆盖父级"来选择退出。

如果您在游戏中的任何演员混频器上使用游戏定义或用户定义的发送来混响，请将其关闭，以避免应用混响两次。

#### <a name="spatialization"></a>空间
默认情况下，项目声学 Wwise 混合器插件应用卷积混响，让 Wwise 执行平移空间化。 在此默认仅混响配置中使用 Project 声学时，可以在干混合中使用任何通道配置和空间化方法。 因此，您可以将几乎任何空间调整器与项目声学混响混合和匹配。 您的选项包括[基于 Ambisonics 的双体空间器](https://www.audiokinetic.com/products/ambisonics-in-wwise/)和[Windows 声波](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)。
 
项目声学包括一个可选的空间器，支持基于对象的高分辨率 HRTF 渲染和平移。 在混合器插件设置中选择"**执行空间化**"复选框，并在*HRTF*或*平移*之间进行选择。 此外，禁用用户定义的辅助发送到所有干总线，以避免通过项目声学混合器插件和 Wwise 进行两次空间化。 空间化模式无法实时更改，因为它需要声音库再生。 重新启动虚幻，然后在选择播放以集成混频器插件配置更改（如 **"执行空间化"** 复选框设置）之前重新生成声库。

![Wwise 混合器插件空间化设置](media/mixer-spatial-settings.png)

遗憾的是，当前不支持其他基于对象的空间器插件。 它们作为混合器插件实现，Wwise 不允许将多个混合器插件分配给单个执行组件混合器。  

### <a name="audio-setup-in-unreal"></a>虚幻中的音频设置
1. 首先，你需要烘焙你的游戏水平，以产生一个声学资产，这将放置在*Content_声学*。 请参阅[虚幻烘焙教程](unreal-baking.md)。 示例包中包括一些预烘焙的级别。

1. 在场景中创建声学空间参与者。 仅在一个级别中创建这些参与者之一，因为它表示整个级别的声学。

    ![在虚幻编辑器中创建声学空间角色](media/create-acoustics-space.png)

1. 将烘焙声学数据资产分配给声学空间组件上的声学数据插槽。 你的场景现在有音响！

    ![虚幻编辑器中的声学资产分配](media/acoustics-asset-assign.png)

1. 添加空执行组件。 按如下方式配置它。

    ![虚幻编辑器显示空参与者中的声学组件使用情况](media/acoustics-component-usage.png)

       
    <sup>1</sup>向执行组件添加声学音频组件。 此组件将项目声学功能添加到 Wwise 音频组件。
        
    <sup>2</sup>默认情况下选择"**在开始时播放"** 框。 此设置在级别启动时触发关联的 Wwise 事件。</li>
         
    <sup>3</sup>使用 **"显示声学参数"** 复选框打印有关源的屏幕调试信息。

    ![启用调试值的声源上的虚幻编辑器声学面板](media/debug-values.png)

    <sup>4</sup>根据通常的 Wwise 工作流分配 Wwise 事件。
       
    <sup>5</sup>确保关闭 **"使用空间音频**"。 如果对特定音频组件使用"项目声学"，则不能同时使用 Wwise 的空间音频引擎进行声学。</li>
       
已经完成全部设置。 移动周围的场景，探索声学效果！

## <a name="next-steps"></a>后续步骤
* 尝试[项目声学不真实/Wwise设计教程](unreal-workflow.md)。
* 了解如何为游戏场景[进行烘焙](unreal-baking.md)。

---
title: 项目音响效果 Unity 烘焙教程
titlesuffix: Azure Cognitive Services
description: 本教程介绍了在 Unity 中使用项目音响效果进行音效烘焙。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243125"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>项目音响效果 Unity 烘焙教程
本教程介绍如何在 Unity 中使用项目音响效果进行音效烘培。

所需软件：
* 用于 Windows 或 MacOS 的 [Unity 2018.2 及更高版本](https://unity3d.com)
* [Unity 项目中集成的项目音响效果插件](unity-integration.md)或[项目音响效果 Unity 示例内容](unity-quickstart.md)
* *可选：* [Azure Batch 帐户](create-azure-account.md)，它用于通过云计算加快烘培速度

## <a name="open-the-project-acoustics-bake-window"></a>打开项目音响效果烘焙窗口
在 Unity 中，从“窗口”菜单选择“音效”。  

![Unity 编辑器，其中突出显示了“窗口”菜单中的“音效”选项](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>创建导航网格
项目音响效果使用一个导航网格来放置用于模拟的侦听器探测点。 可以使用 Unity 的[导航网格工作流](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)。 或者，可以使用其他 3D 建模包来自行设计网格。

## <a name="mark-acoustic-scene-objects"></a>标记音效场景对象
项目音响效果依赖于两种类型的场景对象进行模拟：
- 在模拟中反射和屏蔽声音的对象。
- 对模拟中的侦听器探测点进行约束的播放器导航网格。

这两种对象类型均使用“对象”选项卡进行标记  。

由于标记对象时仅向对象添加 AcousticsGeometry 或 AcousticsNavigation 组件，因此还可使用[标准Unity 组件工作流](https://docs.unity3d.com/Manual/UsingComponents.html)来标记或取消标记对象   。 只能标记网格渲染器和地形。 系统会忽略其他所有对象类型。 复选框会标记或取消标记受影响的所有对象。

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>标记声音屏蔽和反射几何体
打开“音效”窗口的“对象”选项卡   。 如果对象应屏蔽、反射或吸收声音，则将此对象标记为“音效几何体”  。 音效几何体可包含地面、墙壁、屋顶、窗户和窗玻璃、地毯以及大型家具等物体。 你可对这些对象使用任意复杂度级别。 由于场景已在模拟前像素化处理，因此与简单化的对象相比，详细网格（例如带有很多树叶的树木）的成本不会增加太多。

不要包含不得对音效造成影响的物体，例如不可见碰撞体网格。

烘培结果中修复了对象在探测计算期间的转换（通过“探测”选项卡）  。 移动场景中任何标记的对象都需要重做探测计算并烘培场景。

### <a name="mark-the-navigation-mesh"></a>标记导航网格
音效系统将选取通过 Unity 工作流创建的导航网格。 要使用自己的网格，请通过“对象”选项卡进行标记  。

### <a name="for-reference-the-objects-tab-parts"></a>供参考：“对象”选项卡部件
选项卡页的部件包括（说明后面有插图）：

1. 选项卡选择按钮（已选择“对象”选项卡）  。 使用这些按钮可从左到右推移音效烘培的各个步骤。
1. 有关使用此页面需要执行的操作的简短说明。
1. 层次结构窗口的适用筛选器。 使用这些选项可按指定类型的对象筛选层次结构窗口，以便轻松标记这些窗口。 如果还没有为音效标记任何对象，选择最后两个选项不会显示任何内容。 但是，在标记对象后，可以借助这些选项查找这些对象。
1. 如果未选择任何对象，本部分则显示场景中所有对象的状态。
    * 总计：活动的非隐藏对象的总数。
    * 已忽略：非网格渲染器或地形对象的数目。
    * 网格：网格渲染器对象的数目。
    * 地形：地形对象的数目。
    * 几何体：场景中标记为“音效几何体”的网格或地形对象的数目  。
    * 导航：场景中标记为“音效导航”的网格或地形对象的数目。  该数目不包括 Unity 的 NavMesh。
1. 场景中“可标记”对象的总数，这些对象仅限网格渲染器和地形。 使用复选框可音效将这些对象标记（向其添加相应的组件）为几何体或导航的复选框。
1. 若未选择任何对象，在需要时，此说明可提醒选择对象进行标记。 还可以选中一个或两个复选框以标记场景中的所有对象。
1. 若已选择某些对象，本部分则仅显示场景中所选对象的状态。
1. “可标记”选定对象的总数。 选中或取消选中复选框将标记或取消标记选定对象。

如果场景中未选择任何内容，则“对象”选项卡将如下图所示。 

![“音效对象”选项卡，未选择任何项](media/objects-tab-no-selection-detail.png)

如果选择了场景或层次结构窗口中的某些对象，选项卡将如下图所示。

![包含选项的“音效对象”选项卡](media/objects-tab-selection-detail.png)

如果部分对象已标记，部分未标记，相应的复选框则显示“混合”值，如下图所示。

![“音效对象”选项卡，其中突出显示了混合选择图标](media/mixed-object-selection-detail.png)

选中该复选框可标记所有项。 取消选中它会取消标记所有对象。

可同时为几何体和导航标记对象。

## <a name="select-acoustic-materials"></a>选择音效材料
标记对象后，选择“材料”按钮。  然后分配音效材料。 项目音响效果材料系统绑定到 Unity 视觉材料系统。 要使两个对象具有不同的音效材料，它们必须具有不同的视觉材料。

音效材料选择将确定从每个表面反射回来的声音能量大小。 默认音效材料具有与钢材类似的吸收能力。 项目音响效果根据视觉材料的名称推荐材料。 还可向材料分配“自定义”音效材料，以激活可调整的吸收系数滑块  。

房间中给定材料的混响时间与其吸收系数成反比。 大多数材料的吸收系数值介于 0.01 到 0.20 之间。 如果材料的吸收系数超出此范围，则其吸收性很强。

![此图显示混响时间与吸收系数的负相关性。](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>供参考：“材料”选项卡的控件部件
![Unity 中的“音效材料”选项卡](media/materials-tab-detail.png)
1. 单击“材料”按钮会显示此选项卡。 
2. 有关使用此页面需要执行的操作的简短说明。
3. 如果选中此复选框，仅列出标记为“音效几何体”的对象所用的材料。  否则，将列出场景中使用的所有材料。
4. 使用这些选项可更改“音效”列 (#6) 中菜单上的选项的顺序。  按“名称”或“吸收能力”从低到高的顺序对音效材料进行排序。  
5. 场景中所用的材料列表，按字母顺序排序。 如果已选中“仅显示已标记”复选框 (#3)，则仅显示标记为“声学几何体”的对象所用的材料   。 选择此处的材料可选择场景中使用该材料的所有对象。
6. 已分配给场景材料的音效材料。 选择任一项可更改分配给场景材料的音效材料。 若要更改这些菜单的排序顺序，请使用“音效排序依据”选项 (#4)  。
7. 在左侧列中选择的材料的声学吸收系数 (#6)。 值为 0 表示完美反射（无吸收），而值为 1 表示完美吸收（无反射）。 吸收系数无法更改，除非所选材料为“自定义”  。
8. 对于标记为“自定义”的材料，将激活滑块  。 可以使用滑块或键入值来分配吸收系数。

## <a name="calculate-and-review-listener-probe-locations"></a>计算并检查侦听器探测位置
分配材料后，切换到“探测”选项卡  。单击“计算”以放置侦听器探测点进行模拟  。

### <a name="what-the-calculate-button-does"></a>“计算”按钮的作用
“计算”按钮使用所选的音效场景几何体来为模拟准备场景  ：

- 它采用场景网格中的几何体并计算体素体  。 体素体是一个三维体，包含整个场景，并由较小的立方“体素”组成。 体素大小由模拟频率决定，而模拟频率通过“模拟分辨率”设置进行控制  。 每个体素都被标记为“露天”或包含场景几何体。 如果体素包含几何体，则使用分配给该几何体的材料的吸收系数标记该体素。
- 它使用导航网格来放置侦听器探测点。 该算法在空间覆盖面和模拟时间及文件大小等计算考虑因素之间实现平衡。 它旨在确保走廊狭长和空间狭小时都始终能覆盖一定面积。 典型的探测点计数跨度很大，从针对小场景的 100 到针对大场景的数千个。

这些计算可能需要几分钟，具体取决于场景的大小和计算机的速度。

### <a name="review-voxel-and-probe-placement"></a>检查体素和探针布置情况
预览体素数据和探测点位置，确保已准备好烘培场景。 如果导航网格不完整或者音效几何体缺乏或多余，则通常很容易就能在预览中呈现。 使用“Gizmos”菜单启用或禁用体素和探测定位  。

![Unity 中的“Gizmos”菜单](media/gizmos-menu.png)

包含音效几何体的体素显示为绿色立方体。 请探索你的场景并验证应为几何体的所有内容是否都有体素。 场景照相机必须在对象 5 米范围内，以便体素显示。

如果比较所创建的体素在粗糙分辨率与精细分辨率下的效果，你将看到低分辨率的体素是高分辨率的两倍大。

![Unity 编辑器中的粗糙分辨率体素预览](media/voxel-cubes-preview.png)

在运行时，模拟结果插入到侦听器探测点位置之间。 请检查播放器应在场景中遍历的任何位置附近是否有探测点。

![Unity 编辑器中的探测预览](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>场景重命名时要小心
场景名称用于连接场景和存储探测点放置和体素化的文件。 如果在计算探测点后重命名场景，材料的分配和放置数据将丢失，应重新运行。

### <a name="for-reference-parts-of-the-probes-tab"></a>供参考：“探测”选项卡的控件部件
![Unity 中的“音效探测”选项卡](media/probes-tab-detail.png)

1. 单击“探测”按钮会打开此选项卡  。
2. 有关可在此选项卡上执行的操作的简短说明。
3. 使用这些选项可设置粗糙或精细模拟分辨率。 粗糙分辨率的速度更快，但存在一些弊端。 有关详细信息，请参阅[烘培分辨率](bake-resolution.md)。
4. 指定要将音效数据文件放置到的位置。 选择“...”按钮以访问文件夹选取器  。 默认位置为 *Assets/AcousticsData*。 在此位置下还会创建一个 *Editor* 子文件夹。 有关详细信息，请参阅本文稍后的[烘培过程添加的数据文件](#Data-Files)。
5. 使用此处提供的前缀命名此场景的数据文件。 默认值为 *Acoustics_[Scene Name]* 。
6. 计算探测后，将禁用上述控件。 选择“清除”按钮清除计算并启用控件，以便可以使用新设置重新计算  。
7. 选择“计算”按钮以体素化场景并计算探测点位置  。 计算过程在计算机本地完成， 且必须在烘培之前完成。

在此版本的项目音响效果中，无法手动放置探测。 需使用“探测”选项卡上的自动化过程  。

有关粗糙分辨率与精细分辨率的详细信息，请参阅[烘培分辨率](bake-resolution.md)。

## <a name="bake-your-scene-by-using-azure-batch"></a>使用 Azure Batch 烘培场景
可借助 Azure Batch 服务使用云端计算群集来烘培场景。 项目音响效果 Unity 插件会直接连接到 Azure Batch，为每个烘培实例化、管理和关闭一个 Azure Batch 群集。 在“烘培”选项卡上，输入你的 Azure 凭据，选择群集计算机类型和大小，然后选择“烘培”   。

### <a name="for-reference-parts-of-the-bake-tab"></a>供参考：“烘焙”选项卡的部件
![Unity 中的“音效烘培”选项卡](media/bake-tab-details.png)

1. 单击“烘培”按钮会显示此选项卡  。
2. 有关可在此页上执行的操作的简短说明。
3. 创建 Azure 帐户后，在这些字段中输入 Azure 凭据。 有关详细信息，请参阅[创建 Azure Batch 帐户](create-azure-account.md)。
4. 音效工具集的 Docker 映像标记字段。
5. 打开 Azure 门户以管理订阅、监视使用情况和查看计费信息。
6. 指定用于计算的 Azure Batch 计算节点类型。 节点类型必须受 Azure 数据中心位置支持。 如果你不确定，请保留 **Standard_F8s_v2**。
7. 用于计算的节点数。 此数字会影响烘培时间。 它受 Azure Batch 核心分配的限制。 默认分配仅允许使用两个 8 核节点或一个 16 核节点，但可以进行扩展。 有关核心分配约束的详细信息，请参阅[创建 Azure Batch 帐户](create-azure-account.md)。
8. 选中此复选框可将计算机池配置为使用[低优先级节点](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)。 低优先级计算节点的成本要低得多。 但它们可能并非一直可用，或随时被抢占。
9. 场景的探测计数在“探测”选项卡上计算得出  。探测数决定需要在云中运行的模拟次数。 不能指定多于探测数的节点数。
10. 作业在云中运行预计耗费的运行时间，不包括节点启动时间。 作业开始运行后，此字段将显示返回结果前预计需要等待的时间。
11. 运行模拟所需的总计算时间。 此值是 Azure 中将使用的总的节点计算时间。 有关详细信息，请参阅本文稍后的[估算 Azure 烘培成本](#Estimating-bake-cost)。
12. 此消息告知作业完成后烘培结果保存到的位置。
13. *（仅限高级用法：）* 此按钮强制 Unity 忘记已提交的烘培。 例如，如果你使用其他计算机下载了结果，请选择“清除状态”按钮以忘记该作业  。 准备就绪后，将不下载结果文件  。 这不同于取消作业。  如果运行，该作业将继续在云中运行。
14. 选择此按钮可将烘培提交到云。 当某个作业正在运行时，此按钮将变为“取消作业”  。
15. 选择此按钮可以准备处理[电脑上的音效模拟](#Local-bake)。
16. 此区域显示制作的状态。 完成烘培后，它将显示“已下载”。

随时可在 [Azure 门户](https://portal.azure.com)获取关于有效作业、计算池和存储的完整信息。

作业运行时，“烘培”按钮标签将更改为“取消作业”   。 使用此按钮可取消正在进行的作业。 系统将提示你确认。 取消作业操作不可撤消。 如果取消，将不会显示任何结果，但仍会根据使用的任何 Azure 计算时间收费。

开始烘培后，可以关闭 Unity。 云制作可能需要数个小时，具体取决于项目、节点类型和节点数。 重新加载该项目并打开“音效”窗口时，制作作业状态将更新。 如果作业已完成，将下载输出文件。

出于安全性，Azure 凭据将存储在本地计算机上，并与 Unity 编辑器相关联。 它们仅用于建立与 Azure 的安全连接。

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>在 Azure 门户上查找正在运行的作业的状态

1. 在“烘培”选项卡上找到烘培作业 ID  。

    ![“烘培”选项卡上突出显示的 Unity 烘培作业 ID](media/unity-job-id.png)  

2. 打开 [Azure 门户](https://portal.azure.com)，转到用于烘培的 Batch 帐户，然后选择“作业”  。

    ![Azure 门户中的“作业”链接](media/azure-batch-jobs.png)  

3. 在作业列表中搜索作业 ID。

   ![Azure 门户中突出显示的烘培作业状态](media/azure-bake-job-status.png)  

4. 选择作业 ID 可查看相关任务的状态和总体作业状态。

   ![烘培任务状态](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a> 估算 Azure 烘培成本

若要估算烘培的成本，请从“估算的计算成本”值（持续时间）着手  。 将该值乘以所选“VM 节点类型”的每小时成本（当地货币）  。 请注意，结果不包括节点启动和运行所需的节点时间。

例如，假设你选择了“Standard_F8s_v2”（成本为 $0.40/小时）作为节点类型  。 如果“估算的计算成本”为 3 小时 57 分钟，则运行作业的估算成本为 $0.40 * ~4 小时 = ~$1.60  。 实际成本很可能略高，因为开始使用节点时会产生的额外时间。

在 [Azure Batch 定价](https://azure.microsoft.com/pricing/details/virtual-machines/linux)中查找每小时节点成本。 （选择“计算优化”或“高性能计算”作为类别。）  

## <a name="Local-bake"></a>在电脑上烘焙场景
你还可以在自己的电脑上烘培场景。 在创建 Azure Batch 帐户之前对小场景进行音效试验时，此方法可能很有用。 但请注意，本地音效模拟可能耗时很长，具体取决于场景的大小。

### <a name="minimum-hardware-requirements"></a>最低硬件要求
* 至少 8 核且带 32 GB RAM 的 x86-64 处理器
* [已启用 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 以运行 Docker

例如，在装配了 Intel Xeon E5-1660 @ 3 GHz 和 32 GB RAM 的 8 核计算机上测试：
* 对于探测 100 次的小场景，粗糙分辨率烘培时约耗时 2 小时，精细分辨率烘培时约耗时 32 小时。
* 对于探测 1000 次的小场景，粗糙分辨率烘培时约耗时 20 小时，精细分辨率烘培时约耗时 21 天。

### <a name="set-up-docker"></a>设置 Docker
在电脑上安装并配置用于处理模拟的 Docker：
1. 安装 [Docker Desktop](https://www.docker.com/products/docker-desktop)。
2. 启动 Docker 设置，转到“高级”，将资源配置为至少具有 8 GB RAM  。 可向 Docker 分配的 CPU 越多，烘培完成的速度就越快。  
![示例 Docker 设置](media/docker-settings.png)
1. 转到“共享驱动器”，启用用于处理的驱动器共享  。  
![Docker 共享驱动器选项](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>运行本地烘培
1. 选择“烘培”选项卡上的“准备本地烘培”按钮   。然后选择要将输入文件和执行脚本保存到的文件夹位置。 然后，可以通过将该文件夹复制到计算机，在任何计算机上运行烘培，只要该计算机满足最低硬件要求并安装了 Docker 即可。
2. 若要启动模拟，请在 Windows 上运行 *runlocalbake.bat* 脚本，或在 MacOS 上运行 *runlocalbake.sh* 脚本。 此脚本将提取 Project Acoustics Docker 映像（其中包含用于模拟处理的工具集）并启动模拟。
3. 模拟完成后，将生成的 *.ace* 文件复制回到 Unity 项目。 为确保 Unity 将此识别为二进制文件，请将“.bytes”追加到文件扩展名（例如，“Scene1.ace.bytes”）。 有关模拟的详细日志存储在 *AcousticsLog.txt* 中。 如果遇到任何问题，请检查此文件来帮助诊断问题。

## <a name="Data-Files"></a>由烘焙过程添加的数据文件

以下四个数据文件是在烘培过程中创建的。 一个包含模拟结果并按你的标题进行传输。 其他文件存储与 Unity 编辑器相关的数据。

模拟结果：
* *Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes*：此文件是运行时查找表。 其中包含模拟结果和体素化的音效场景元素。 可以在“探测”选项卡上更改此文件的位置和名称  。

   请注意不要删除模拟结果文件。  该文件不可恢复，除非通过重新烘培场景。

编辑器数据文件：
* *Assets/Editor/[SceneName]\_AcousticsParameters.asset*：此文件存储你在音效 UI 的字段中输入的数据。 不能更改此文件的名称和位置。
* *Assets/AcousticsData/Editor/Acoustics_[SceneName].vox*：该文件存储像素化的音效几何体以及选择“探测”选项卡上的“计算”按钮后计算得出的材料属性   。可以在“探测”选项卡上更改此文件的位置和名称  。
* *Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml*：此文件存储选择“计算”按钮后计算得出的模拟参数  。 可以在“探测”选项卡上更改此文件的位置和名称  。

## <a name="set-up-the-acoustics-lookup-table"></a>设置音效查找表
将项目音响效果预制件从项目面板拖放到场景中  ：

![Unity 中的“音效”预制件](media/acoustics-prefab.png)

选择“ProjectAcoustics”游戏对象，然后转到其检查器面板  。 指定烘培结果的位置（*Assets/AcousticsData* 中的 .ace 文件）。将其拖放到音效管理器脚本，或选择文本框旁边的圆形按钮。

![Unity 中的“音效管理器”预制件](media/acoustics-manager.png)

## <a name="next-steps"></a>后续步骤
* 了解 [Unity 设计控件](unity-workflow.md)。
* 了解[项目音响效果的设计概念](design-process.md)。

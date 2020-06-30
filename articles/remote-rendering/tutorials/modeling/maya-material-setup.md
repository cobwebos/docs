---
title: 在 Maya 中设置基于物理的渲染材料
description: 说明如何在 Maya 中设置基于物理的渲染材料，并将它导出为 FBX 格式
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977354"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>教程：在 Maya 中设置基于物理的渲染材料

## <a name="overview"></a>概述
在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 将具有高级照明模型的材料分配给场景中的对象。
> * 处理对象和材料的实例化。
> * 将场景导出为 FBX 格式，并说明要选择的重要选项。

在 `Maya` 中创建[基于物理的渲染 (PBR) 材料](../../overview/features/pbr-materials.md)是一种相对简单的任务，在许多方面与在其他内容创建应用（如 `3DS Max`）中设置 PBR 类似。 以下教程是针对 ARR 项目的基本 PBR 着色器设置和 FBX 导出的指南。 

本教程中的示例场景包含一些 `Polygon Box` 对象，向这些对象分配了一些不同材料（木材、金属、涂漆金属、塑料和橡胶）。 广泛地说，每种材料包含以下所有或大多数纹理 

* `Albedo` 是材料颜色图，也称为 `Diffuse` 或 `BaseColor`
* `Metalness`，确定材料是否为金属以及哪些部分是金属。 
* `Roughness`，确定表面的粗糙度或平滑度，这会影响表面上的反射和高光的清晰度或模糊度。
* `Normals`，向表面添加细节，例如金属表面上的斑蚀和凹痕或木材中的颗粒，而无需添加更多多边形。
* `Ambient Occlusion`，用于向模型添加软底纹和接触阴影。 它是一个灰度图，指示模型的哪些区域接收完整照明（白色）或完整阴影（黑色）。 

## <a name="prerequisites"></a>先决条件
* `Autodesk Maya 2017` 或更高版本

## <a name="setting-up-materials-in-the-scene"></a>在场景中设置材料
在 Maya 中，设置 PBR 材料的过程如下所示：

首先，如你在示例场景中所见，我们已创建了一些 Box 对象，其中每个对象都表示不同类型的材料。 请注意，如下图所示，向每个对象都提供了其自己的适当名称 

> 值得注意的是，在开始为 Azure 远程渲染 (ARR) 创建资产之前，它使用米进行度量并且向上方向为 Y 轴。 因此，建议将 Maya 中的场景单位设置为米。 此外在导出时，建议在 FBX 导出设置中将单位设置为米。 

> [!TIP]
良好做法是，正确命名模型资产（通常使用相关部件或材料类型），因为通过名称可以更轻松地在包含大量对象的场景中导航。

![对象名](media/object-names.jpg)

创建/获取纹理后，根据需求，可能要在纹理应用（如 `Quixel Suite`、`Photoshop` 或 `Substance Suite`）中为模型创建独有的纹理，或者，可以使用来自其他源的通用平铺纹理，并按如下所示将它们应用于模型：

* 在场景视区中，选择模型/几何并右键单击它。 在出现的菜单中单击 `Assign New Material`
* 在 `Assign New Material` 选项中，请转到 `Maya`>`Stingray PBS`。 此操作会将一种 PBR 材料分配给模型。 

在 `Maya 2020` 中，有一些不同的 PBR 着色器可用（`Maya Standard Surface`、`Arnold Standard Surface` 和 `Stingray PBR`）。 `Maya Standard Surface Shader` 尚无法通过 `FBX plugin 2020` 导出，而 `Arnold Standard Surface Shader` 可以通过 FBX 文件导出。 在大多数其他方面，它与 `Maya Standard Surface Shader` 相同，并且类似于 `3D Studio Max` 中的 `Physical Material`。

`The Stingray PBR Shader` 可与许多其他应用程序兼容，并且最符合 `ARR` 的要求，自 `Maya 2017` 以来便受支持。 此外，在视区中可视化的这种类型的材料类似于后面将在 ARR 中可视化的内容，这十分方便。

![“Stingray”材料](media/stingray-material.jpg)

将材料分配给资产并进行适当命名后，现在可以继续分配各种纹理。 以下图像详细说明每种纹理类型适合于 PBR 材料的情况。 `Stingray PBR` 材料使你可以选择可激活的属性，因此在 `plug in` 纹理贴图之前，需要激活相关属性： 

![材料设置](media/material-setup.jpg)

> [!TIP]
良好做法是正确命名材料，考虑其用途和/或类型。 要在唯一部件上使用的材料可以针对该部件进行命名，而可以在更多区域中使用的材料可以针对其属性或类型进行命名。

按如下所示分配纹理：

![纹理设置](media/texture-setup.jpg)

创建和设置了 PBR 材料后，值得考虑的是如何在场景中[实例化对象](../../how-tos/conversion/configure-model-conversion.md#instancing)。 在场景中实例化类似对象（例如螺母、螺栓、螺钉垫圈），相同的任何对象本质上都可以在文件大小方面产生显著节省。 主对象的实例可以具有自己的缩放、旋转和转换，以便可以在场景中按需放置。 在 Maya 中，实例化的过程十分简单。

* 在 `Edit` 菜单中，转到 `Duplicate Special` 并打开 `Options`， 
* 在 `Duplicate Special` 选项中，将 `Geometry Type` 从 `Copy` 切换为 `Instance`， 
* 单击 `Duplicate Special`

![实例化](media/instancing.jpg)

此操作会创建对象的一个实例，它可以独立于父对象以及该父对象的其他实例进行旋转或缩放。 
>但是，在处于组件模式期间对实例进行的任何更改都会传输到对象的所有实例，因此，如果使用的是实例化对象组件（顶点、多边形面等），请首先确保需要进行任何更改以影响所有这些实例。

在示例场景中，每个单独 box 对象都已实例化。 将场景导出为 FBX 格式时，此操作将具有相关性。

![场景概述](media/scene-overview.jpg)

>有关场景中的实例化的最佳做法是在进行过程中创建它们，因为以后将“副本”替换为实例化对象非常困难。 

## <a name="fbx-export-process"></a>FBX 导出过程

现在，我们可以进一步对场景或场景资产进行 FBX 导出。 一般而言，在导出资产时，仅选择从场景中导出所需对象/资产会很有意义。 如果场景中有 100 个对象，但只想使用其中 30 个对象，则导出整个场景时没有任何意义。 因此，除非很乐意导出整个场景，否则请进行选择，并转到：

* `File` > `Export Selection`然后在导出对话框中，转到底部，并将 `Files of Type` 设置为 `FBX Export`。 此窗口将公开 FBX 导出设置。 下图中以红色突出显示了 FBX 导出的主要设置。

![FBX 导出](media/FBX-exporting.jpg)

根据要求（例如，可能要将资产发送到客户端，但不希望随资产发送大量纹理文件），可以选择在导出的 FBX 文件中嵌入纹理。 此选项意味着只打包的一个文件，但它会显著增加该 FBX 资产的大小。 可以通过打开 `Embed Media` 选项来启用嵌入纹理的选项，如下所示。

> [!TIP]
> 请注意，此示例中的文件已进行命名，以反映此情况。 这是一种跟踪资产的良好做法。 

完成导出配置设置后，请单击右下角的“导出选择”按钮。

![嵌入媒体](media/embedding-media.jpg)

## <a name="conclusion"></a>结束语

一般而言，此类型的材料看起来更真实，因为它基于光线的现实物理学。 它可创造场景在现实中存在的附加沉浸式效果。

## <a name="next-steps"></a>后续步骤

你现在已了解最重要的功能，用于对场景中的对象设置具有高级照明的材料并将它导出为 ARR 支持的 FBX 格式。 下一步是转换 FBX 文件并在 ARR 中进行可视化。

> [!div class="nextstepaction"]
> [快速入门：转换要渲染的模型](../../quickstarts\convert-model.md)
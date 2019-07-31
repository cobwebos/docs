---
title: 教程 - 有关使用 Azure 空间定位点创建新 HoloLens Unity 应用的分步说明 | Microsoft Docs
description: 本教程介绍如何使用 Azure 空间定位点创建新的 HoloLens Unity 应用。
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7e7d4a542146fb8d342cce6c34f9d97e72349f84
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561391"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>教程：有关使用 Azure 空间定位点创建新 HoloLens Unity 应用的分步说明

本教程将演示如何使用 Azure Spatial Anchor 创建新的 HoloLens Unity 应用。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保做好以下准备：

1. 具有  通用 Windows 平台开发工作负荷和  Windows 10 SDK（10.0.18362.0 或更新版本）组件以及<a href="https://git-scm.com/download/win" target="_blank">适用于 Windows 的 Git</a> 且安装了 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> 的 Windows 计算机。
2. 适用于 Visual Studio 的 [C++/WinRT Visual Studio 扩展 (VSIX)](https://aka.ms/cppwinrt/vsix) 应从 [Visual Studio Marketplace](https://marketplace.visualstudio.com/) 安装。
3. 启用了[开发人员模式](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio)的 HoloLens 设备。 本文需要包含 [Windows 2018 年 10 月 10 日更新](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 )（也称为 RS5）的 HoloLens 设备。 要在 HoloLens 上更新为最新版本，请打开“设置”应用，转到“更新和安全”，然后选择“检查更新”按钮    。

## <a name="getting-started"></a>入门

首先设置项目和 Unity 场景：
1. 启动 Unity。
2. 选择“新建”  。
4. 确保已选中“3D”  。
5. 为项目命名并输入保存位置  。
6. 单击“创建项目”。 
7. 使用以下方法将空的默认场景保存到新文件：“文件” > “另存为”   。
8. 将新场景命名为“Main”，然后按“保存”按钮   。

**设置项目设置**

现在来设置一些 Unity 项目设置，帮助面向 Windows Holographic SDK 进行开发。 

首先，为应用程序设置质量设置。 
1. 选择“编辑” > “项目设置” > “质量”   
2. 在“Windows 应用商店”徽标下的列中，单击“默认”行处的箭头，然后选择“极低”    。 当“Windows 应用商店”列和“极低”行中的框为绿色时，表明已正确应用该设置   。

我们需要让 Unity 知道，要导出的应用需创建沉浸式视图而不是 2D 视图。 通过面向 Windows 10 SDK 在 Unity 上启用虚拟现实支持来创建沉浸式视图。

1. 转到“编辑” > “项目设置” > “播放器”    。
2. 在“播放器设置”的“检查器面板”中，选择“Windows 应用商店”图标    。
3. 展开“XR 设置”组  。
4. 在“呈现”部分，选中“支持虚拟现实”复选框，添加新“虚拟现实 SDK 的”列表    。
5. 验证列表中是否显示“Windows 混合现实”  。 如果没有，请选择列表底部的“+”按钮，然后选择“Windows 混合现实”   。
 
> [!NOTE]
> 如果没有看到 Windows 应用商店图标，请仔细检查以确保在安装之前选择了 Windows 应用商店 .NET 脚本后端。 如果没有，可能需要使用正确的 Windows 安装重新安装 Unity。

**验证脚本后端配置**
1. 转到“编辑” > “项目设置” > “播放器”（由于上一步操作，“播放器”可能仍处于打开的状态）     。
2. 在“播放器设置”的“检查器面板”中，选择“Windows 应用商店”图标    。
3. 在“其他设置”配置节中，确保将“脚本后端”设置为“IL2CPP”    。

**设置功能**
1. 转到“编辑” > “项目设置” > “播放器”（由于上一步操作，“播放器”可能仍处于打开的状态）     。
2. 在“播放器设置”的“检查器面板”中，选择“Windows 应用商店”图标    。
3. 在“发布设置”配置部分中，选中“InternetClientServer”和“SpatialPerception”    。

**设置主虚拟照相机**
1. 在“层次结构面板”中，选择“主摄像头”   。
2. 在“检查器”中，将其转换位置设置为“0,0,0”   。
3. 找到“清除标志”属性，将下拉列表从“Skybox”更改为“纯色”    。
4. 单击“背景”字段以打开颜色选取器  。
5. 将“R、G、B 和 A”设置为“0”   。
6. 选择“添加组件”，搜索并添加“空间映射碰撞器”   。

**创建脚本**
1. 在“项目”窗格中，在“资产”文件夹下创建新文件夹“脚本”    。 
2. 右键单击该文件夹，依次选择“创建>”、“C# 脚本”   。 标题设为“AzureSpatialAnchorsScript”  。 
3. 转到“GameObject” -> “创建空白项”   。 
4. 选择它，然后在“检查器”中将其从“GameObject”重命名为“MixedRealityCloud”    。 选择“添加组件”，然后搜索并添加“AzureSpatialAnchorsScript”   。

**创建球体预制项**
1. 转到“GameObject”   -> “3D 对象”   -> “球体”  。
2. 在“检查器”  中，将其刻度设置为“0.25、0.25、0.25”  。
3. 在“层次结构”窗格中找到“球体”   。 单击该球体，并将其拖到“项目”窗格的“资产”文件夹中   。
4. 右键单击已在“层次结构”窗格中创建的原始球体，并将其**删除**  。

现在，应该在“项目”窗格中具有球体预制项  。

## <a name="trying-it-out"></a>体验一下
若要测试一切设置是否有效，请在“Unity”中生成应用，并从“Visual Studio”进行部署   。 按照 [**MR 基础知识 100：Unity 入门**课程](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio)中第 6 章进行操作。 应会显示 Unity 启动屏幕，然后是清晰的显示屏。

## <a name="place-an-object-in-the-real-world"></a>将对象放入真实世界
让我们使用该应用创建并放置一个对象。 打开[部署应用程序](#trying-it-out)时创建的 Visual Studio 解决方案。 

首先，将以下 import 语句添加到 `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

然后，将以下成员变量添加到 `AzureSpatialAnchorsScript` 类： 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

在继续操作前，我们需要设置已在 spherePrefab 成员变量上创建的球体预制项。 返回到“Unity”  。
1. 在“Unity”的“层次结构”窗格  中，选择 **MixedRealityCloud** 对象  。
2. 单击已保存在“项目”窗格中的“球体”预制项   。 将已单击的“球体”  拖到“检查器”窗格的“Azure 空间定位点脚本(脚本)”下的“球体预制项”区域中    。

现在，“球体”应已在脚本中设置为预制项  。 从“Unity”生成，然后再次打开生成的“Visual Studio”解决方案   ，就像在[试用](#trying-it-out)中操作一样。 

在“Visual Studio”  中，再次打开 `AzureSpatialAnchorsScript.cs`。 将以下代码添加到 `Start()` 方法中。 此代码将挂接 `GestureRecognizer`，用于检测隔空敲击并调用 `HandleTap`。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

现在要在 `Update()` 下面添加以下 `HandleTap()` 方法。 它会执行光线投射并获取放置球体的命中点。 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

现在需要创建球体。 球体最初将为白色，但稍后将调整此值。 添加以下 `CreateAndSaveSphere()` 方法：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

从“Visual Studio”运行应用以再次验证它  。 这一次，点击屏幕创建并将白色球体放在所选的表面上。

## <a name="set-up-the-dispatcher-pattern"></a>设置调度程序模式

使用 Unity 时，所有 Unity API（例如用于执行 UI 更新的 API）都需要设置在主线程上。 然而，我们要编写的代码会在其他线程上获得回调。 我们想在回调中更新 UI，因此需要一种从侧线程到达主线程的方法。 为通过侧线程在主线程上执行代码，我们使用调度程序模式。 

现在添加一个成员变量 dispatchQueue，是一个“操作队列”。 将操作推入队列，然后取消排队，并在主线程上运行“操作”。 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

接下来，添加一种向队列添加操作的方法。 在 `Update()` 后面添加 `QueueOnUpdate()`：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

现在使用更新 () 循环来检查是否有排队的操作。 如果有，则取消其排队，然后运行它。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>获取 Azure 空间定位点

现在下载 Azure 空间定位点 SDK。 转到 [Azure 空间定位点 GitHub 发布页面](https://github.com/Azure/azure-spatial-anchors-samples/releases)。 在“资产”下，下载“AzureSpatialAnchors.unitypackage”文件  。 

在 Unity 中，转到“资产”，单击“导入包” > “自定义包...”    。导航到包并选择“打开”  。

在弹出的新“导入 Unity 包”窗口中，选择左下角的“无”   。 然后在“AzureSpatialAnchorsPlugin” > “插件”下，选择“通用”、“编辑器”和“HoloLens”      。 在右下角上单击“导入”  。

现在需要还原 Nuget 包以获取 Azure 空间定位点 SDK。 从“Unity”生成，然后再次打开并生成“Visual Studio”解决方案，详见[试用](#trying-it-out)   。 

在“Visual Studio”解决方案中，将以下导入添加到 `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs` 中  ：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=23-26&highlight=1)]

然后，将以下成员变量添加到 `AzureSpatialAnchorsScript` 类：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>将本地 Azure 空间定位点 附加到本地定位点

现在设置 Azure 空间定位点的 CloudSpatialAnchorSession。 我们首先在 `AzureSpatialAnchorsScript` 类中添加以下 `InitializeSession()` 方法。 调用该方法后，它会确保在启动应用期间创建并正确初始化 Azure 空间定位点会话。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

需要编写代码来处理委托调用。 在后续操作过程会陆续添加更多代码。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

现在，将 `initializeSession()` 方法挂接到 `Start()` 方法。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

最后，将以下代码添加到 `CreateAndSaveSphere()` 方法中。 此代码将本地 Azure 空间定位点附加到要放入真实世界的球体。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

在继续进一步的操作之前，需要创建 Azure 空间定位点帐户标识符和密钥（如果尚未创建）。 遵循以下部分获取这些信息。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>将本地定位点上传到云中

获取 Azure 空间定位点帐户标识符和密钥后，将 `Account Id` 粘贴到 `SpatialAnchorsAccountId`，将 `Account Key` 粘贴到 `SpatialAnchorsAccountKey`。

最后，让我们将所有元素挂接到一起。 在 `SpawnNewAnchoredObject()` 方法中添加以下代码。 创建球体后，此代码将立即调用 `CreateAnchorAsync()` 方法。 该方法返回后，以下代码将对球体执行一次最终更新，将其颜色更改为蓝色。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

再次从“Visual Studio”运行应用  。 在头部移动，然后隔空敲击以放置球体。 收集到足够的帧后，球体将变为黄色，并且云上传操作将会开始。 上传完成后，球体将变为蓝色。 （可选）也可以使用“Visual Studio”中的“输出”窗口来监视应用发送的日志消息  。 应能看到推荐的创建进度，以及上传完成后云返回的定位点标识符。

> [!NOTE]
> 如果收到“DllNotFoundException：无法加载 DLL ‘AzureSpatialAnchors’：找不到指定的模块。”，需再次“清除”并“生成”解决方案   。

## <a name="locate-your-cloud-spatial-anchor"></a>查找云空间定位点

将定位点上传到云后，可以再次尝试查找该定位点。 将以下代码添加到 `HandleTap()` 方法中。 此代码将会：

* 调用 `ResetSession()`，这将停止 `CloudSpatialAnchorSession` 并从屏幕上删除现有的蓝色球体。
* 再次初始化 `CloudSpatialAnchorSession`。 执行此操作确保要查找的定位点来自云，而不是创建的本地定位点。
* 创建一个“观察程序”，用于查找上传到 Azure 空间定位点的定位点  。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

现在添加 `ResetSession()` 和 `CleanupObjects()` 方法。 可以把它们放在 `QueueOnUpdate()` 之下

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

现在，我们需要挂接在找到要查询的定位点之后所要调用的代码。 在 `InitializeSession()` 内，添加以下回调：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]

 
现在，让我们添加一些代码，找到云空间定位点后，此代码片段将创建并放置一个绿色球体。 它还支持再次点击屏幕，以便可以再次重复整个方案：创建另一个本地定位点，将其上传，然后再次找到它。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

就这么简单！ 最后一次从“Visual Studio”运行应用，以从头到尾体验整个方案  。 四处移动设备，并放置白色球体。 然后，不断移动头部以捕获环境数据，直到球体变为黄色。 本地定位点将会上传，球体将变为蓝色。 最后，再次点击屏幕以删除本地定位点，然后查询该定位点在云中的对应定位点。 继续移动设备，直到找到云空间定位点。 绿色球体应会显示在正确的位置；可以再次清除并重复整个方案。

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]
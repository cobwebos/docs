---
title: 快速入门：使用 Azure 空间定位点创建 HoloLens Unity 应用 | Microsoft Docs
description: 在此快速入门中，将了解如何通过空间定位点使用 Unity 生成 HoloLens 应用。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b2bfec47bc92ebf5db1561d8fca33940dc376866
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752495"
---
# <a name="quickstart-create-a-hololens-unity-app-using-azure-spatial-anchors"></a>快速入门：使用 Azure 空间定位点创建 HoloLens Unity 应用

本快速入门介绍如何使用 [Azure 空间定位点](../overview.md)创建 HoloLens Unity 应用。 Azure 空间定位点是一种跨平台开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，将获得一个使用 Unity 生成的 HoloLens 应用，该应用可以保存和重新调用空间定位点。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建空间定位点帐户
> * 准备 Unity 生成设置
> * 配置空间定位点帐户标识符和帐户密钥
> * 导出 HoloLens Visual Studio 项目
> * 在 HoloLens 设备上部署和运行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

- 具有**通用 Windows 平台开发**工作负载，且安装了 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> 和 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> 的 Windows 计算机。
- 启用了[开发人员模式](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio)的 HoloLens 设备。
- 必须在“生成设置”->“播放机设置”->“发布设置”->“功能”下设置应用的“SpatialPerception”功能。
- 必须在“生成设置”->“播放机设置”->“XR 设置”下为应用启用“Windows 混合现实 SDK”的“虚拟现实支持”。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>在 Unity 中打开示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

打开 Unity 并在 `Unity` 文件夹中打开项目。

通过选择“文件” -> “生成设置”，打开“生成设置”。

在“平台”部分中，选择“通用 Windows 平台”。 然后，将“目标设备”更改为“HoloLens”。

选择“切换平台”，将平台更改为“通用 Windows 平台”。

![Unity 生成设置](./media/get-started-unity-hololens/unity-build-settings.png)

关闭“生成设置”窗口。

## <a name="configure-account-identifier-and-key"></a>配置帐户标识符和密钥

在“项目”窗格中，导航到 `Assets/AzureSpatialAnchorsPlugin/Examples` 并打开 `AzureSpatialAnchorsBasicDemo.unity` 场景文件。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

选择“文件” -> “保存”，从而保存场景。

## <a name="export-the-hololens-visual-studio-project"></a>导出 HoloLens Visual Studio 项目

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

选择“生成”，以打开一个对话框。 然后选择一个文件夹，以导出 HoloLens Visual Studio 项目。

导出完成后，将显示一个文件夹，其中包含导出的 HoloLens 项目。

## <a name="deploy-the-hololens-application"></a>部署 HoloLens 应用程序

在文件夹中，双击 `HelloAR U3D.sln`以在 Visual Studio 中打开项目。

将“解决方案配置”更改为“发布”，将“解决方案平台”更改为 x86，并从部署目标选项中选择“设备”。

![Visual Studio 配置](./media/get-started-unity-hololens/visual-studio-configuration.png)

打开 HoloLens 设备，登录并使用 USB 电缆将其连接到电脑。

选择“调试” > “开始调试”以部署应用并开始调试。

按照应用中的说明，放置并重新调用定位点。

在 Visual Studio 中，通过选择“停止调试”或按“Shift+F5”停止应用。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教程：跨设备共享空间定位点](../tutorials/tutorial-share-anchors-across-devices.md)

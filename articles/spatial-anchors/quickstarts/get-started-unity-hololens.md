---
title: 快速入门：使用 Azure 空间定位点创建 Unity HoloLens 应用 | Microsoft Docs
description: 在此快速入门中，将了解如何通过空间定位点使用 Unity 生成 HoloLens 应用。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e8196c9561465930fc9723c9b8f8a0d02668ef5
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114081"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>快速入门：使用 Azure 空间定位点创建 Unity HoloLens 应用

本快速入门介绍如何创建使用 [Azure 空间定位点](../overview.md)的 Unity HoloLens 应用。 空间定位点是一种跨平台开发者开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，将获得一个使用 Unity 生成的 HoloLens 应用，该应用可以保存和重新调用空间定位点。

将了解如何执行以下操作：

- 创建空间定位点帐户。
- 准备 Unity 生成设置。
- 配置空间定位点帐户标识符和帐户密钥。
- 导出 HoloLens Visual Studio 项目。
- 部署应用并在 HoloLens 上运行应用。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本快速入门教程需要：

- 需要一个已安装 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1</a> 或更高版本以及 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> 或更高版本的 Windows 计算机。 Visual Studio 安装必须包括“通用 Windows 平台开发”  工作负载和 **Windows 10 SDK（10.0.18362.0 或更高版本）** 组件。 也必须安装 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>。
- 需要一台启用了[开发者模式](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio)的 HoloLens 设备。 [Windows 10 2018 年 10 月更新](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018)（也称为 RS5）必须安装在此设备上。 若要在 HoloLens 上更新为最新版本，请打开“设置”应用，转到“更新和安全”，然后选择“检查更新”    。
- 在应用上，需要启用“SpatialPerception”功能  。 此设置在“生成设置” > “播放机设置” > “发布设置” > “功能”中     。
- 在应用上，需要通过“Windows 混合现实 SDK”启用“支持的虚拟现实”   。 此设置在“生成设置” > “播放机设置” > “XR 设置”中    。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>下载并打开 Unity 示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

通过选择“文件” > “生成设置”，打开“生成设置”    。

在“平台”部分中，选择“通用 Windows 平台”   。 将“目标设备”更改为“HoloLens”   。

选择“切换平台”，将平台更改为“通用 Windows 平台”   。 Unity 可能会提示你安装 UWP 支持组件（若缺少这些组件）。

![Unity 生成设置窗口](./media/get-started-unity-hololens/unity-build-settings.png)

关闭“生成设置”窗口  。

## <a name="configure-the-account-identifier-and-key"></a>配置帐户标识符和密钥

在“项目”窗格中，转到 `Assets/AzureSpatialAnchors.Examples/Scenes` 并打开 `AzureSpatialAnchorsBasicDemo.unity` 场景文件  。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

选择“文件” > “保存”，从而保存场景   。

## <a name="export-the-hololens-visual-studio-project"></a>导出 HoloLens Visual Studio 项目

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

选择“生成”  。 在对话框中，选择一个文件夹，在其中导出 HoloLens Visual Studio 项目。

导出完成后，将显示包含导出的 HoloLens 项目的文件夹。

## <a name="deploy-the-hololens-application"></a>部署 HoloLens 应用程序

在文件夹中，双击 HelloAR U3D.sln 以在 Visual Studio 中打开项目  。

将“解决方案配置”更改为“发布”，将“解决方案平台”更改为 x86，并从部署目标选项中选择“设备”      。

如果使用 HoloLens 2，请使用“ARM64”作为“解决方案平台”，而不是“x86”    。

   ![Visual Studio 配置](./media/get-started-unity-hololens/visual-studio-configuration.png)

打开 HoloLens 设备，登录并使用 USB 电缆将设备连接到电脑。

选择“调试” > “开始调试”以部署应用并开始调试   。

按照应用中的说明，放置并重新调用定位点。

在 Visual Studio 中，选择“停止调试”或 Shift+F5 停止应用  。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教程：跨设备共享空间定位点](../tutorials/tutorial-share-anchors-across-devices.md)

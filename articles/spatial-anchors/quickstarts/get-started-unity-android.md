---
title: 快速入门：使用 Azure 空间定位点创建 Unity Android 应用 | Microsoft Docs
description: 在此快速入门中，将了解如何通过空间定位点使用 Unity 生成 Android 应用。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 642abfb99b40d67802b7194ad225ebcd2872a72b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135119"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>快速入门：使用 Azure 空间定位点创建 Unity Android 应用

本快速入门介绍如何使用 [Azure 空间定位点](../overview.md)创建 Unity Android 应用。 Azure 空间定位点是一种跨平台开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，将获得一个使用 Unity 生成的 ARCore Android 应用，该应用可以保存和重新调用空间定位点。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建空间定位点帐户
> * 准备 Unity 生成设置
> * 下载并导入适用于 Unity 的 SDK
> * 配置空间定位点帐户标识符和帐户密钥
> * 导出 Android Studio 项目
> * 在 Android 设备上部署和运行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

- Windows 或 macOS 计算机，已安装 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> 和 <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>。
  - 如果在 Windows 上运行，则还需要 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>。
  - 如果在 macOS 上运行，请通过 HomeBrew 安装 Git。 在终端的一行中输入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 然后运行 `brew install git`。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">支持开发人员</a>和 <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 功能</a>的 Android 设备。
- 你的应用必须使用 **1.7** 版的适用于 Unity 的 ARCore SDK。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>下载并打开 Unity 示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>配置帐户标识符和密钥

在“项目”窗格中，导航到 `Assets/AzureSpatialAnchorsPlugin/Examples` 并打开 `AzureSpatialAnchorsBasicDemo.unity` 场景文件  。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

通过选择“文件” -> “保存”以保存场景   。

## <a name="export-the-android-studio-project"></a>导出 Android Studio 项目

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

确保“导出项目”  复选框没有复选标记。 单击“生成并运行”  。 系统会要求保存 `.apk` 文件，可以为它挑选任何名称。

按照应用中的说明，放置并重新调用定位点。

> [!NOTE]
> 运行应用时，如果没看到作为背景的照相机（例如，你看到的是空白、蓝色或其他纹理），则可能需要重新导入 Unity 中的资产。 停止应用。 在 Unity 中的顶部菜单中，选择“资产”->“重新导入全部”  。 然后再次运行应用。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教程：跨设备共享空间定位点](../tutorials/tutorial-share-anchors-across-devices.md)

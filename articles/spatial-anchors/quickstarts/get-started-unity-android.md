---
title: 快速入门：创建 Unity Android 应用
description: 在此快速入门中，将了解如何通过空间定位点使用 Unity 生成 Android 应用。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e6fbf0e4cbfc44692292c33fc46d9ea0eccec89d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75370200"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>快速入门：使用 Azure 空间定位点创建 Unity Android 应用

本快速入门介绍如何使用 [Azure 空间定位点](../overview.md)创建 Unity Android 应用。 Azure 空间定位点是一种跨平台开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，将获得一个使用 Unity生成的 ARCore Android 应用，该应用可以保存和重新调用空间定位点。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建空间定位点帐户
> * 准备 Unity 生成设置
> * 配置空间定位点帐户标识符和帐户密钥
> * 导出 Android Studio 项目
> * 在 Android 设备上部署和运行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，请确保具备以下项：

- 具有 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1+</a> 的 Windows 或 macOS 计算机，其中包括 Android Build Support 和 Android SDK & NDK Tools 模块。
  - 如果在 Windows 上运行，则还需要 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> 和 <a href="https://git-lfs.github.com/">Git LFS</a>。
  - 如果在 macOS 上运行，请通过 HomeBrew 安装 Git。 在终端的一行中输入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 然后，运行 `brew install git` 和 `brew install git-lfs`。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">支持开发人员</a>和 <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 功能</a>的 Android 设备。
  - 你的计算机可能需要其他设备驱动程序才能与 Android 设备通信。 有关其他信息和说明，请参阅[此处](https://developer.android.com/studio/run/device.html)。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>下载并打开 Unity 示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>配置帐户标识符和密钥

在“项目”窗格中，导航到 `Assets/AzureSpatialAnchors.Examples/Scenes` 并打开 `AzureSpatialAnchorsBasicDemo.unity` 场景文件  。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

选择“文件” -> “保存”，从而保存场景   。

## <a name="export-the-android-studio-project"></a>导出 Android Studio 项目

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

在“运行设备”  中选择设备，然后单击“生成并运行”  。 系统会要求保存 `.apk` 文件，可以为它选取任何名称。

按照应用中的说明，放置并重新调用定位点。

## <a name="troubleshooting"></a>故障排除

运行应用时，如果没看到作为背景的照相机（例如，你看到的是空白、蓝色或其他纹理），则可能需要重新导入 Unity 中的资产。 停止应用。 在 Unity 中的顶部菜单中，选择“资产”->“重新导入全部”  。 然后再次运行应用。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教程：跨设备共享空间定位点](../tutorials/tutorial-share-anchors-across-devices.md)

---
title: 快速入门：创建 Xamarin Android 应用
description: 本快速入门介绍如何通过空间定位点使用 Xamarin 生成 Android 应用。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2976104c72179fede3b39816663ac8196d7539ec
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277008"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>快速入门：使用 Azure 空间定位点创建 Xamarin Android 应用

本快速入门介绍如何通过 [Azure 空间定位点](../overview.md)使用 Xamarin 创建 Android 应用。 Azure 空间定位点是一种跨平台开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，将获得一个 Android 应用，该应用可以保存和重新调用空间定位点。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建空间定位点帐户
> * 配置空间定位点帐户标识符和帐户密钥
> * 在 Android 设备上部署和运行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：
- Windows 或 macOS 计算机：
  - 如果使用 Windows：
    - 最新版本的 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2+</a>。
    - <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>
  - 如果使用 macOS：
    - 最新版本的 <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio for Mac 8.1+</a>。
    - <a href="https://git-scm.com/download/mac" target="_blank">适用于 macOS 的 Git</a>
- 最新版本的 Xamarin.Android 已在所选的平台上安装并运行。 有关安装 Xamarin.Android 的指南，请参阅 [Xamarin.Android 安装](https://docs.microsoft.com/xamarin/android/get-started/installation/index)指南。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">支持开发人员</a>和 <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 功能</a>的 Android 设备。
  - 你的计算机可能需要其他设备驱动程序才能与 Android 设备通信。 有关详细信息，请参阅[此文](https://developer.android.com/studio/run/device.html)。
- 你的应用必须以 ARCore **1.8** 为目标。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>打开示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

在 Visual Studio 中打开 `Xamarin/SampleXamarin.sln`。

## <a name="configure-account-identifier-and-key"></a>配置帐户标识符和密钥

下一步是将应用配置为使用帐户标识符和帐户密钥。 在[设置空间定位点资源](#create-a-spatial-anchors-resource)时，将它们复制到文本编辑器中。

打开 `Xamarin/SampleXamarin.Common/AccountDetails.cs`。

找到 `SpatialAnchorsAccountKey` 字段并将 `Set me` 替换为帐户密钥。

找到 `SpatialAnchorsAccountId` 字段并将 `Set me` 替换为帐户标识符。

## <a name="deploy-the-app-to-your-android-device"></a>将应用部署到 Android 设备

启动 Android 设备，登录并使用 USB 电缆将其连接到计算机。

将启动项目设置为 **SampleXamarin.Android**，将**解决方案配置**更改为“发布”  ，然后在设备选择器下拉列表中选择要部署到的设备。

# <a name="windowstabdeploy-windows"></a>[Windows](#tab/deploy-windows)

![Visual Studio 配置](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

选择“调试” > “开始调试”以部署并启动应用   。

# <a name="macostabdeploy-macos"></a>[macOS](#tab/deploy-macos)

![Visual Studio 配置](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

选择“运行”   > “在不调试的情况下启动”  以部署并启动应用。

---

在应用中，选择“基本”  以运行演示，然后按照说明放置和回收定位点。

> ![屏幕截图 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![屏幕截图 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![屏幕截图 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教程：跨设备共享空间定位点](../tutorials/tutorial-share-anchors-across-devices.md)

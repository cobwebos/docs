---
title: 快速入门：使用 Azure 空间定位点创建 Android 应用 | Microsoft Docs
description: 在此快速入门中，将了解如何使用空间定位点生成 Android 应用。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: f7387b210c1499ff0a19fe17667090be0c2c0933
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561400"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>快速入门：使用 Azure 空间定位点创建 Android 应用

本快速入门介绍如何以 Java 或 C++/NDK 语言使用 [Azure 空间定位点](../overview.md)创建 Android 应用。 Azure 空间定位点是一种跨平台开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，将获得一个 ARCore Android 应用，该应用可以保存和重新调用空间定位点。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建空间定位点帐户
> * 配置空间定位点帐户标识符和帐户密钥
> * 在 Android 设备上部署和运行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

- 具有 <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a> 的 Windows 或 macOS 计算机。
  - 如果在 Windows 上运行，则还需要 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>。
  - 如果在 macOS 上运行，请通过 HomeBrew 安装 Git。 在终端的一行中输入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 然后运行 `brew install git`。
  - 若要生成 NDK 示例，还需要在 Android Studio 中安装 NDK 和 CMake 3.6 或更高版本的 SDK Tools。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">支持开发人员</a>和 <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 功能</a>的 Android 设备。
- 你的应用必须以 ARCore **1.8** 为目标。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>打开示例项目

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

从[此处](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.8.0/libraries/include/arcore_c_api.h)下载 `arcore_c_api.h` 并将其置于 `Android\NDK\libraries\include` 中。

在新克隆的存储库中，通过运行以下命令初始化子模块：

```console
git submodule update --init --recursive
```

---

打开 Android Studio。

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

选择“打开现有 Android Studio 项目”，然后选择位于 `Android/Java/` 的项目  。

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

选择“打开现有 Android Studio 项目”，然后选择位于 `Android/NDK/` 的项目  。

---

## <a name="configure-account-identifier-and-key"></a>配置帐户标识符和密钥

下一步是将应用配置为使用帐户标识符和帐户密钥。 在[设置空间定位点资源](#create-a-spatial-anchors-resource)时，将它们复制到文本编辑器中。

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

打开 `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`。

找到 `SpatialAnchorsAccountKey` 字段并将 `Set me` 替换为帐户密钥。

找到 `SpatialAnchorsAccountId` 字段并将 `Set me` 替换为帐户标识符。

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

打开 `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`。

找到 `SpatialAnchorsAccountKey` 字段并将 `Set me` 替换为帐户密钥。

找到 `SpatialAnchorsAccountId` 字段并将 `Set me` 替换为帐户标识符。

---

## <a name="deploy-the-app-to-your-android-device"></a>将应用部署到 Android 设备

打开 Android 设备，登录并使用 USB 电缆将其连接到电脑。

从 Android Studio 工具栏中选择“运行”  。

![Android Studio 部署和运行](./media/get-started-android/android-studio-deploy-run.png)

在“选择部署目标”对话框中选择 Android 设备，然后选择“确定”以在 Android 设备上运行应用   。

按照应用中的说明，放置并重新调用定位点。

通过在 Android Studio 工具栏中选择“停止”，从而停止应用  。

![停止 Android Studio](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教程：跨设备共享空间定位点](../tutorials/tutorial-share-anchors-across-devices.md)

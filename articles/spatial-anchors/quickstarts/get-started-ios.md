---
title: 快速入门：创建 iOS 应用
description: 此快速入门介绍如何使用空间定位点生成 iOS 应用。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: 1da5bd7020304c67bd9f9058612f47d528611a9e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195331"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>快速入门：使用 Azure 空间定位点在 Swift 或 Objective-C 中创建 iOS 应用

本快速入门介绍如何使用 [Azure 空间定位点](../overview.md)在 Swift 或 Objective-C 中创建 iOS 应用。 Azure 空间定位点是一种跨平台开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，将获得一个 ARKit iOS 应用，该应用可保存和重新调用空间定位点。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建空间定位点帐户
> * 配置空间定位点帐户标识符和帐户密钥
> * 在 iOS 设备上部署和运行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

- 开发人员启用已安装最新版本的 <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> 和 <a href="https://cocoapods.org" target="_blank">CocoaPods</a> 的 macOS 计算机。
- 通过 HomeBrew 安装的 Git：
  1. 在终端中以单行形式输入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 
  1. 运行 `brew install git` 和 `brew install git-lfs`。
  1. 使用 `git lfs install`（适用于当前用户）或 `git lfs install --system`（适用于整个系统）更新 git 配置。
- 支持开发人员的 <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit 兼容</a> iOS 设备。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>打开示例项目

使用终端执行以下操作。

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

使用 CocoaPods 安装必要的 Pod：

# <a name="swift"></a>[Swift](#tab/openproject-swift)

导航到 `iOS/Swift/`。

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

导航到 `iOS/Objective-C/`。

```bash
cd ./iOS/Objective-C/
```

---

运行 `pod install --repo-update` 以安装项目的 CocoaPods。

现在打开 Xcode 中的 `.xcworkspace`。

> [!NOTE]
> 如果在升级到 macOS Catalina (10.15) 后遇到 CocoaPod 问题，请参阅[此处](#cocoapods-issues-on-macos-catalina-1015)的故障排除相关步骤。

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>配置帐户标识符和密钥

下一步是将应用程序配置为使用帐户标识符和帐户密钥。 在[设置空间定位点资源](#create-a-spatial-anchors-resource)时，将它们复制到文本编辑器中。

# <a name="swift"></a>[Swift](#tab/openproject-swift)

打开 `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`。

找到 `spatialAnchorsAccountKey` 字段并将 `Set me` 替换为帐户密钥。

找到 `spatialAnchorsAccountId` 字段并将 `Set me` 替换为帐户标识符。

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

打开 `iOS/Objective-C/SampleObjC/BaseViewController.m`。

找到 `SpatialAnchorsAccountKey` 字段并将 `Set me` 替换为帐户密钥。

找到 `SpatialAnchorsAccountId` 字段并将 `Set me` 替换为帐户标识符。

---

## <a name="deploy-the-app-to-your-ios-device"></a>将应用部署到 iOS 设备

将 iOS 设备连接到 Mac 并将“活动方案”设置为 iOS 设备  。

![选择设备](./media/get-started-ios/select-device.png)

选择“生成并运行当前方案”  。

![部署和运行](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> 如果看到 `library not found for -lPods-SampleObjC` 错误，则可能是打开了 `.xcodeproj` 文件而不是 `.xcworkspace`。 请打开 `.xcworkspace` 然后重试。

在 Xcode 中，按“停止”来停止应用  。

## <a name="troubleshooting"></a>故障排除

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>macOS Catalina (10.15) 上的 CocoaPods 问题

如果最近更新到 macOS Catalina (10.15) 并事先安装了 CocoaPods，则 CocoaPods 可能处于损坏状态，无法正确配置 pod 和 `.xcworkspace` 项目文件。 若要解决此问题，需要通过运行以下命令重新安装 CocoaPods：

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>从个人预配配置文件/开发人员帐户部署到 iOS 10.3.1 时应用崩溃 

如果通过个人预配配置文件/开发人员帐户在 iOS 10.3.1 上部署 iOS 应用，可能会出现此错误：`Library not loaded: @rpath/ADAL...`。 

若要解决问题，请执行以下操作：

- 使用不是“个人团队”配置文件（付费开发人员帐户）的预配配置文件。
- 将应用部署到运行 iOS 13.3 或更低版本的 iOS 设备，或者部署到运行 iOS 13.4 Beta 版或发布版的 iOS 设备。
- 有关此问题的详细信息，请访问 [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid)。


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教程：跨设备共享空间定位点](../tutorials/tutorial-share-anchors-across-devices.md)

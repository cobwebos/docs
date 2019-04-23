---
title: 快速入门 - 使用 Azure 空间定位点创建 iOS 应用 | Microsoft Docs
description: 此快速入门介绍如何使用空间定位点生成 iOS 应用。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b8ee1bdfece35d774cdc79595a495b0b3719a836
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996639"
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

- 开发人员启用已安装 <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10+</a> 和 <a href="https://cocoapods.org" target="_blank">CocoaPods</a> 的 macOS 计算机。
- 通过 HomeBrew 安装的 Git。 在终端的一行中输入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 然后运行 `brew install git`。
- 支持开发人员的 <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit 兼容</a> iOS 设备。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>打开示例项目

使用终端执行以下操作。

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

使用 CocoaPods 安装必要的 Pod：

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

导航到 `iOS/Swift/`。

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

导航到 `iOS/Objective-C/`。

```bash
cd ./iOS/Objective-C/
```

***

运行 `pod install --repo-update` 以安装项目的 CocoaPods。

现在打开 Xcode 中的 `.xcworkspace`。

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

***

## <a name="configure-account-identifier-and-key"></a>配置帐户标识符和密钥

下一步是将应用配置为使用帐户标识符和帐户密钥。 在[设置空间定位点资源](#create-a-spatial-anchors-resource)时，将它们复制到文本编辑器中。

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

打开 `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`。

找到 `spatialAnchorsAccountKey` 字段并将 `Set me` 替换为帐户密钥。

找到 `spatialAnchorsAccountId` 字段并将 `Set me` 替换为帐户标识符。

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

打开 `iOS/Objective-C/SampleObjC/BaseViewController.m`。

找到 `SpatialAnchorsAccountKey` 字段并将 `Set me` 替换为帐户密钥。

找到 `SpatialAnchorsAccountId` 字段并将 `Set me` 替换为帐户标识符。

***

## <a name="deploy-the-app-to-your-ios-device"></a>将应用部署到 iOS 设备

将 iOS 设备连接到 Mac 并将“活动方案”设置为 iOS 设备。

![选择设备](./media/get-started-ios/select-device.png)

选择“生成并运行当前方案”。

![部署和运行](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> 如果看到 `library not found for -lPods-SampleObjC` 错误，则可能是打开了 `.xcodeproj` 文件而不是 `.xcworkspace`。 请打开 `.xcworkspace` 然后重试。

在 Xcode 中，按“停止”来停止应用。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教程：跨设备共享空间定位点](../tutorials/tutorial-share-anchors-across-devices.md)

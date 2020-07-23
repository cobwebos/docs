---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "72882317"
---
## <a name="android"></a>[Android](#tab/Android)

Java android 示例支持跨设备共享。
在 Android Studio 的示例文件夹中打开文件 `SharedActivity.java`。 在 `SharedActivity.java` 文件中输入之前步骤（从 ASP.NET Web 应用 Azure 部署）中获得的 URL 作为 `SharingAnchorsServiceUrl` 的值。 将 URL 中的 `index.html` 替换为 `api/anchors`。 它应如下所示： `https://<app_name>.azurewebsites.net/api/anchors`。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Objective-C iOS 示例支持跨设备共享。
打开示例文件夹中的文件 `SharedDemoViewController.m`。 在 `SharedActivity.java` 文件中输入之前步骤（从 ASP.NET Web 应用 Azure 部署）中获得的 URL 作为 `SharingAnchorsServiceUrl` 的值。 将 URL 中的 `index.html` 替换为 `api/anchors`。 它应如下所示： `https://<app_name>.azurewebsites.net/api/anchors`。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Xamarin Android 和 iOS 示例都支持跨设备共享。
打开示例文件夹中的文件 `AccountDetails.cs`。 在 `SharedActivity.java` 文件中输入之前步骤（从 ASP.NET Web 应用 Azure 部署）中获得的 URL 作为 `AnchorSharingServiceUrl` 的值。 将 URL 中的 `index.html` 替换为 `api/anchors`。 它应如下所示： `https://<app_name>.azurewebsites.net/api/anchors`。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>设置 Android 设备

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>设置 iOS 设备

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>配置帐户标识符和密钥

在“项目”窗格中，导航到 `Assets/AzureSpatialAnchorsPlugin/Examples` 并打开 `AzureSpatialAnchorsLocalSharedDemo.unity` 场景文件。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

在“项目”窗格中，导航到 `Assets\AzureSpatialAnchors.Examples\Resources`。 选择 `SpatialAnchorSamplesConfig`。 然后，在“检查器”  窗格中，输入 `Sharing Anchors Service url`（来自 ASP.NET Web 应用 Azure 部署）作为 `Base Sharing Url` 的值，将 `index.html` 替换为 `api/anchors`。 它应如下所示： `https://<app_name>.azurewebsites.net/api/anchors`。

选择“文件” > “保存”，从而保存场景。

## <a name="deploy-to-your-device"></a>部署到设备

### <a name="deploy-to-android-device"></a>部署到 Android 设备

登录 Android 设备并使用 USB 线将其连接到计算机上。

通过选择“文件” > “生成设置”，打开“生成设置”。

在“生成中的场景”  下，确保所有场景旁边都有复选标记。

确保“导出项目”不显示勾号  。 单击“生成并运行”  。 系统将提示你保存 `.apk` 文件。 你可为其选择任何名称。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>部署到 iOS 设备

通过选择“文件” > “生成设置”，打开“生成设置”。

在“生成中的场景”  下，确保所有场景旁边都有复选标记。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

在 Xcode 中，通过选择“停止”来停止应用  。

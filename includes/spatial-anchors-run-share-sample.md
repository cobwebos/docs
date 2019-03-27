---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305402"
---
## <a name="set-up-your-device"></a>设置设备

### <a name="set-up-an-android-device"></a>设置 Android 设备

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>设置 iOS 设备

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>配置帐户标识符和密钥

在“项目”窗格中，导航到 `Assets/AzureSpatialAnchorsPlugin/Examples` 并打开 `AzureSpatialAnchorsLocalSharedDemo.unity` 场景文件。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

在“检查器”窗格中，输入 `Sharing Anchors Service url`（来自 ASP.NET Web 应用 Azure 部署）作为 `Base Sharing Url` 的值，并将 `index.html` 替换为 `api/anchors`。 它应如下所示： `https://<app_name>.azurewebsites.net/api/anchors`。

选择“文件” > “保存”，从而保存场景。

## <a name="to-deploy-the-app-to-an-android-device"></a>将应用部署到 Android 设备

登录 Android 设备并使用 USB 线将其连接到计算机上。

通过选择“文件” > “生成设置”，打开“生成设置”。

在“生成中的场景”下，保证 `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 场景旁显示勾号，然后清除所有其他场景的勾号。

确保“导出项目”不显示勾号。 单击“生成并运行”。 系统将提示你保存 `.apk` 文件。 你可为其选择任何名称。

根据应用中的说明操作。 可选择“创建并共享定位标记”或“找到共享定位标记”。 第一个选项是让你创建一个稍后能在相同设备或其他设备上找到的定位标记。 如果已在相同设备或其他设备上运行应用，则第二个选项可让你找到之前共享的定位标记。

## <a name="to-deploy-the-app-to-an-ios-device"></a>将应用部署到 iOS 设备

通过选择“文件” > “生成设置”，打开“生成设置”。

在“生成中的场景”下，保证 `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 场景旁显示勾号，然后清除所有其他场景的勾号。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

根据应用中的说明操作。 可选择“创建并共享定位标记”或“找到共享定位标记”。 第一个选项是让你创建一个稍后能在相同设备或其他设备上找到的定位标记。 如果已在相同设备或其他设备上运行应用，则第二个选项可让你找到之前共享的定位标记。

在 Xcode 中，通过选择“停止”来停止应用。

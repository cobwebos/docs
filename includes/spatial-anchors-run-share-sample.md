---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753445"
---
## <a name="open-the-sample-project-in-unity"></a>在 Unity 中打开示例项目

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>为 Android 设备进行设置

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>为 iOS 设备进行设置

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>配置帐户标识符和密钥

在“项目”窗格中，导航到 `Assets/AzureSpatialAnchorsPlugin/Examples` 并打开 `AzureSpatialAnchorsLocalSharedDemo.unity` 场景文件。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

此外，在“检查器”窗格中，输入 `Sharing Anchors Service url`（来自 ASP.NET Web 应用 Azure 部署）作为 `Base Sharing Url` 的值，将 `index.html` 替换为 `api/anchors`。 因此它看起来应该如下所示：`https://<app_name>.azurewebsites.net/api/anchors`。

选择“文件” -> “保存”，从而保存场景。

## <a name="to-deploy-to-an-android-device"></a>部署到 Android 设备

打开 Android 设备，登录并使用 USB 电缆将其连接到电脑。

通过选择“文件” -> “生成设置”，打开“生成设置”。

在“生成中的场景”下，在 `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 场景旁放置复选标记，然后清除所有其他场景的复选标记。

确保“导出项目”复选框没有复选标记。 单击“生成并运行”。 系统会要求保存 `.apk` 文件，可以为它挑选任何名称。

根据应用中的说明操作。 可以选择“创建并共享定位点”，或“查找共享定位点”。 通过第一个选项可创建以后可以在相同设备或不同设备上查找的定位点。 如果以前在相同设备或不同设备上运行了应用，则通过第二个选项可查找以前共享的定位点。

## <a name="to-deploy-to-an-ios-device"></a>部署到 iOS 设备

通过选择“文件” -> “生成设置”，打开“生成设置”。

在“生成中的场景”下，在 `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 场景旁放置复选标记，然后清除所有其他场景的复选标记。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

根据应用中的说明操作。 可以选择“创建并共享定位点”，或“查找共享定位点”。 通过第一个选项可创建以后可以在相同设备或不同设备上查找的定位点。 如果以前在相同设备或不同设备上运行了应用，则通过第二个选项可查找以前共享的定位点。

在 Xcode 中，按“停止”来停止应用。

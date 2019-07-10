---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: b46a2b18309851bbe2934980137a53d2de6f6efc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135357"
---
## <a name="set-up-your-device-in-unity"></a>在 Unity 中设置设备

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>设置 Android 设备

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>设置 iOS 设备

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>配置帐户标识符和密钥

在“项目”窗格中，导航到 `Assets/AzureSpatialAnchorsPlugin/Examples` 并打开 `AzureSpatialAnchorsLocalSharedDemo.unity` 场景文件  。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

在“检查器”窗格中，输入 `Sharing Anchors Service url`（来自 ASP.NET Web 应用 Azure 部署）作为 `Base Sharing Url` 的值，并将 `index.html` 替换为 `api/anchors`  。 它应如下所示： `https://<app_name>.azurewebsites.net/api/anchors`。

选择“文件” > “保存”，从而保存场景   。

## <a name="deploy-to-your-device"></a>部署到设备

### <a name="deploy-to-android-device"></a>部署到 Android 设备

登录 Android 设备并使用 USB 线将其连接到计算机上。

通过选择“文件” > “生成设置”，打开“生成设置”    。

在“生成中的场景”  下，确保所有场景旁边都有复选标记。

确保“导出项目”不显示勾号  。 单击“生成并运行”  。 系统将提示你保存 `.apk` 文件。 你可为其选择任何名称。

应用启动后，请在“选择演示”  对话框中，使用向左或向右箭头选择“LocalShare”  选项，然后点击“开始!”  。 根据应用中的说明操作。 可选择“创建并共享定位标记”或“找到共享定位标记”   。

第一个方案是让你创建一个稍后能在相同设备或其他设备上找到的定位标记。
如果已在相同设备或其他设备上运行应用，则第二个方案可让你找到之前共享的定位标记。 在你选取方案以后，应用会通过进一步的说明引导你完成后续步骤。 例如，会要求你四处移动设备以收集环境信息。 随后需在环境中放置一个定位点，等待其保存，以及执行其他操作。

### <a name="deploy-to-an-ios-device"></a>部署到 iOS 设备

通过选择“文件” > “生成设置”，打开“生成设置”    。

在“生成中的场景”  下，确保所有场景旁边都有复选标记。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

应用启动后，请在“选择演示”  对话框中，使用向左或向右箭头选择“LocalShare”  选项，然后点击“开始!”  。 根据应用中的说明操作。 可选择“创建并共享定位标记”或“找到共享定位标记”   。

第一个方案是让你创建一个稍后能在相同设备或其他设备上找到的定位标记。
如果已在相同设备或其他设备上运行应用，则第二个方案可让你找到之前共享的定位标记。 在你选取方案以后，应用会通过进一步的说明引导你完成后续步骤。 例如，会要求你四处移动设备以收集环境信息。 随后需在环境中放置一个定位点，等待其保存，以及执行其他操作。

在 Xcode 中，通过选择“停止”来停止应用  。

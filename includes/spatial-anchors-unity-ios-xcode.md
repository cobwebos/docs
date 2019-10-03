---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: d8b6b1bfcbceb1168d0f74c73e72bd42b41bb2ec
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562510"
---
选择“生成”  。 在打开的对话框中，选择要将 Xcode 项目导出到的文件夹。

导出完成后，将显示包含所导出 Xcode 项目的文件夹。

> [!NOTE]
> 如果出现询问是否要替换或追加的窗口，建议选择“追加​​”，因为它速度更快  。 如果要更改场景中的资产，则只需选择“替换”  。 （例如，如果要添加、删除或更改父级/子级关系，或者要添加、删除或更改属性。）如果仅更改源代码，“追加​​”就足够了  。

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>将 Xcode 项目转换为包含 Azure 空间定位点引用的 xcworkspace

在导出的 Xcode 项目文件夹中，在终端中运行此命令，为项目安装必要的 CocoaPod：

```bash
pod install --repo-update
```

现在可以打开 `Unity-iPhone.xcworkspace` 以在 Xcode 中打开项目：

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> 如果看到 `library not found for -lPods-Unity-iPhone` 错误，则可能是打开了 `.xcodeproj` 文件，而没有打开 `.xcworkspace` 文件。 

选择根“Unity-iPhone”节点以查看项目设置，然后选择“常规”选项卡   。

在“签名”下，确保已启用“自动管理签名”   。 如果未启用，请启用，然后在出现的对话框中选择“启用自动”，以重置生成设置  。

在“部署信息”下，确保“部署目标”设置为 `11.0`   。

### <a name="deploy-the-app-to-your-ios-device"></a>将应用部署到 iOS 设备

将 iOS 设备连接到 Mac 并将“活动方案”设置为 iOS 设备  。

![选择设备](./media/spatial-anchors-unity/select-device.png)

选择“生成并运行当前方案”  。

![部署和运行](./media/spatial-anchors-unity/deploy-run.png)
---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89536236"
---
选择“生成”  。 在打开的对话框中，选择要将 Xcode 项目导出到的文件夹。

导出完成后，将显示包含所导出 Xcode 项目的文件夹。

> [!NOTE]
> 如果出现询问是否要替换或追加的窗口，建议选择“追加​​”，因为它速度更快  。 如果要更改场景中的资产，则只需选择“替换”  。 （例如，如果要添加、删除或更改父级/子级关系，或者要添加、删除或更改属性。）如果仅更改源代码，“追加​​”就足够了  。

## <a name="open-the-xcode-project"></a>打开 XCode 项目

现在可以在 Xcode 中打开 `Unity-iPhone.xcodeproj`。 你可以启动 Xcode 并打开导出的 `Unity-iPhone.xcodeproj` 项目，或通过从你导出项目的位置运行以下命令，在 Xcode 中启动项目：

```bash
open ./Unity-iPhone.xcodeproj
```

选择根“Unity-iPhone”节点以查看项目设置，然后选择“常规”选项卡********。

在“签名”下，确保已启用“自动管理签名”********。 如果未启用，请启用，然后在出现的对话框中选择“启用自动”，以重置生成设置****。

在“部署信息”下，确保“部署目标”设置为 `11.0`********。

## <a name="deploy-the-app-to-your-ios-device"></a>将应用部署到 iOS 设备

将 iOS 设备连接到 Mac 并将“活动方案”设置为 iOS 设备。

![选择设备](./media/spatial-anchors-unity/select-device.png)

选择“生成并运行当前方案”。

![部署和运行](./media/spatial-anchors-unity/deploy-run.png)

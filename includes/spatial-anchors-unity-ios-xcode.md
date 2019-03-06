---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890974"
---
选择“生成”，以打开一个对话框。 然后选择一个文件夹以导出 Xcode 项目。

导出完成后，将显示一个文件夹，其中包含导出的 Xcode 项目。

### <a name="open-the-xcode-project"></a>打开 XCode 项目

在导出的 Xcode 项目文件夹中，运行以下命令，为项目安装必要的 CocoaPod：

```bash
pod install --repo-update
```

现在可以打开 `Unity-iPhone.xcworkspace` 以在 Xcode 中打开项目：

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> 如果看到 `library not found for -lPods-Unity-iPhone` 错误，则可能是打开了 `.xcodeproj` 文件而不是 `.xcworkspace`。 请打开 `.xcworkspace` 然后重试。

选择根“Unity-iPhone”节点以查看项目设置，然后选择“常规”选项卡。

在“签名”下，选择“自动管理签名”。 在出现的对话框中选择“启用自动”以重置生成设置。

在“部署信息”下，确保“部署目标”设置为 `11.0`。

### <a name="deploy-the-app-to-your-ios-device"></a>将应用部署到 iOS 设备

将 iOS 设备连接到 Mac 并将“活动方案”设置为 iOS 设备。

![选择设备](./media/spatial-anchors-unity/select-device.png)

选择“生成并运行当前方案”。

![部署和运行](./media/spatial-anchors-unity/deploy-run.png)
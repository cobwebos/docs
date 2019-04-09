---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 33c932c36cd6de730d3768d596a214c442d74ae1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632850"
---
通过选择“文件” > “生成设置”，打开“生成设置”。

在“平台”部分，选择“Android”。 将“生成系统”更改为“Gradle”，并选择“导出项目”。

选择“切换平台”，以将平台更改为“Android”。 Unity 可能会提示你安装 Android 支持组件（若缺少这些组件）。

![Unity 生成设置窗口](./media/spatial-anchors-unity/unity-android-build-settings.png)

关闭“生成设置”窗口。

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>下载并导入适用于 Unity 的 SDK

从[适用于 Unity 1.5 版本的 ARCore SDK](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0) 下载 `unitypackage` 文件。 返回 Unity 项目，选择“资产” > “导入包” > “自定义包”，然后选择之前下载的 `unitypackage` 文件。 在“导入 Unity 包”对话框中，确保选中所有文件，然后选择“导入”。

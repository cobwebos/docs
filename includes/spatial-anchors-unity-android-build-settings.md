---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 9798e5f76881be38fb27e1f428565caba6e50bf2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135094"
---
通过选择“文件” > “生成设置”，打开“生成设置”    。

在“平台”部分，选择“Android”   。 将“生成系统”  更改为“Gradle”  ，并确保“导出项目”  复选框中没有复选标记。

选择“切换平台”，以将平台更改为“Android”   。 Unity 可能会提示你安装 Android 支持组件（若缺少这些组件）。

![Unity 生成设置窗口](./media/spatial-anchors-unity/unity-android-build-settings.png)

关闭“生成设置”窗口  。

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>下载并导入适用于 Unity 的 SDK

从[适用于 Unity 1.7 版本的 ARCore SDK](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0) 下载 `unitypackage` 文件。 返回 Unity 项目，选择“资产” > “导入包” > “自定义包”，然后选择之前下载的 `unitypackage` 文件    。 在“导入 Unity 包”对话框中，确保选中所有文件，然后选择“导入”   。

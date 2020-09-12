---
title: 安装适用于 Unity 的 Azure 空间锚
description: 配置 Unity 项目以使用 Azure 空间锚。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 08/17/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57ead9636b7218ecfc7d72bb605b469d6a7d1ac6
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536343"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>在 Unity 项目中配置 Azure 空间锚

本指南将演示如何在 Unity 项目中开始处理 Azure 空间锚点 SDK。

## <a name="requirements"></a>要求

Azure 空间定位点当前支持具有以下配置的 Unity 2019.4 (LTS) 。

* Azure 空间锚 2.4.0 + 支持 Unity 2019.4 with AR Foundation 3.1。

## <a name="configuring-a-project"></a>配置项目

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[向项目添加 Unity 包管理器包](#tab/UPMPackage)

当前使用 Unity 包管理器 (UPM) 包分发 Unity 的 Azure 空间锚。 这些包可在 [NPM 注册表](https://bintray.com/microsoft/AzureMixedReality-NPM)中找到。 若要详细了解如何在 Unity 项目中使用作用域内的包注册表，请参阅 [此处](https://docs.unity3d.com/Manual/upm-scoped.html)的官方 Unity 文档。

#### <a name="add-the-registry-to-your-unity-project"></a>将注册表添加到 Unity 项目

1. 在文件资源管理器中，导航到 Unity 项目的 `Packages` 文件夹。 `manifest.json`在文本编辑器中打开项目清单文件。
2. 在该文件的顶部，在与该部分相同的级别上 `dependencies` ，添加以下项以将 Azure 空间锚注册表添加到你的项目。 该 `scopedRegistries` 条目告诉 Unity 在哪里可以找到 Azure 空间锚定 SDK 包。

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>将 SDK 包)  (添加到 Unity 项目

| 平台 | 包名称                                    |
|----------|-------------------------------------------------|
| Android  | ".com"-"- |
| iOS      | spatial-anchors-sdk. ios。     |
| HoloLens | .com.. e-- |

1. 对于你想要在项目中支持的每个平台 (Android/iOS/HoloLens) ，请将包含包名称和包版本的条目添加到 `dependencies` 你的项目清单中的部分。 请参阅下面的示例。

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. 保存并关闭 `manifest.json` 文件。 当你返回到 Unity 时，Unity 应自动检测项目清单更改并检索指定的包。 可以 `Packages` 在项目视图中展开文件夹，以验证是否已导入正确的包。

#### <a name="android-only-configure-the-maintemplategradle-file"></a>仅限 Android：配置 Maintemplate.json. gradle 文件

1. 转到“编辑” > “项目设置” > “播放器”  。
2. 在 "**播放器设置**" 的**检查器面板**中，选择 " **Android** " 图标。
3. 在 " **生成** " 部分下，选中 " **自定义主 Gradle 模板** " 复选框，以在处生成自定义 Gradle 模板 `Assets\Plugins\Android\mainTemplate.gradle` 。
4. 在文本编辑器中打开 `mainTemplate.gradle` 文件。 
5. 在 `dependencies` 部分中，粘贴以下依赖项：

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

完成所有操作后， `dependencies` 部分应如下所示：

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[导入资产包](#tab/UnityAssetPackage)

> [!WARNING]
> SDK 版本2.5.0 后，Azure 空间锚点 SDK 的 Unity 资产包分发将被弃用。

1. 下载 `AzureSpatialAnchors.unitypackage` 适用于 [GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases)版本的目标版本的文件。 
2. 按照 [此处](https://docs.unity3d.com/Manual/AssetPackagesImport.html) 的说明将 Unity 资产包导入到项目中。    

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：创建和定位 Unity 中的定位点](./create-locate-anchors-unity.md)

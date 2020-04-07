---
title: 安装远程呈现包以进行统一
description: 说明如何安装远程呈现客户端 DLL 以进行统一
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681176"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>安装远程呈现包以进行统一

Azure 远程呈现使用 Unity 包封装到 Unity 中。

## <a name="manage-the-remote-rendering-packages-in-unity"></a>在 Unity 中管理远程呈现包

统一包是可以通过 Unity 的[包管理器](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)进行管理的容器。
此包包含整个 C# API 以及使用 Azure 远程呈现与统一所需的所有插件二进制文件。
在 Unity 的包命名方案之后，该包称为**com.microsoft.azure.远程呈现**。

该包不是[ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)的一部分，并且从 Unity 的内部包注册表中不可用。 要将其添加到项目中，必须手动编辑项目`manifest.md`的文件以添加以下内容：
```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
添加此项后，可以使用 Unity 包管理器来确保拥有最新版本。
教程中给出了更全面的说明[：从头开始设置统一项目](../../tutorials/unity/project-setup.md)。

## <a name="unity-render-pipelines"></a>统一渲染管道

远程渲染适用于**通用渲染管道**和**标准渲染管道**。 出于性能原因，建议使用通用渲染管道。

要使用**通用渲染管道**，其包必须安装在 Unity 中。 这可以在 Unity 的**包管理器**UI（包名称通用**RP、** 版本 7.2.1 或更新）中完成`Packages/manifest.json`，也可以通过文件完成，如[Unity 项目设置教程](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)中所述。

## <a name="next-steps"></a>后续步骤

* [统一游戏对象和组件](objects-components.md)
* [教程：从头开始设置 Unity 项目](../../tutorials/unity/project-setup.md)

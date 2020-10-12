---
title: 安装 Unity 的远程渲染包
description: 说明如何安装适用于 Unity 的远程呈现客户端 Dll
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564308"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>安装 Unity 的远程渲染包

Azure 远程呈现使用 Unity 包将集成封装到 Unity。

## <a name="manage-the-remote-rendering-packages-in-unity"></a>在 Unity 中管理远程呈现包

Unity 包是可通过 Unity 的 [包管理器](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)进行管理的容器。
此包包含整个 c # API 以及通过 Unity 使用 Azure 远程呈现所需的所有插件二进制文件。
对于包，遵循 Unity 的命名方案，包称为 " **.com**"。

该包不是 [ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)的一部分，并且无法从 Unity 的内部包注册表中获得。 若要将其添加到项目中，您必须手动编辑项目的 `manifest.md` 文件以添加以下内容：

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
    "com.microsoft.azure.remote-rendering": "0.1.31",
    ...existing dependencies...
  }
}
```

添加后，可以使用 Unity 包管理器确保拥有最新版本。
[教程：查看远程模型](../../tutorials/unity/view-remote-models/view-remote-models.md)中提供了更全面的说明。

## <a name="unity-render-pipelines"></a>Unity 呈现管道

远程呈现适用于 **:::no-loc text="Universal render pipeline":::** 和 **:::no-loc text="Standard render pipeline":::** 。 出于性能原因，建议使用通用呈现管道。

若要使用 **:::no-loc text="Universal render pipeline":::** ，其包必须安装在 Unity 中。 这可以在 Unity 的 **包管理器** UI 中完成， (包名称 **通用 RP**、版本7.3.1 或更新的) 或 `Packages/manifest.json` 文件，如 [Unity 项目设置教程](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)中所述。

## <a name="next-steps"></a>后续步骤

* [Unity 游戏对象和组件](objects-components.md)
* [教程：查看远程模型](../../tutorials/unity/view-remote-models/view-remote-models.md)

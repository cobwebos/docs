---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79381581"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

以下属性可在 `extensionBundle` 中使用：

| properties | 描述 |
| -------- | ----------- |
| id | Microsoft Azure 函数扩展包的命名空间。 |
| 版本 | 要安装的捆绑的版本。 Functions 运行时始终选取按版本范围或间隔定义的最宽松版本。 上述版本值允许使用从 1.0.0 到 2.0.0（不含 2.0.0）的所有捆绑版本。 有关详细信息，请参阅[用于指定版本范围的间隔表示法](/nuget/reference/package-versioning#version-ranges)。 |

捆绑中的包发生变化时，捆绑版本将会递增。 当捆绑中的包增加主版本时，就会发生主版本更改。 捆绑中的主版本更改通常与 Functions 运行时的主版本更改一致。  

默认捆绑安装的当前扩展集将在此 [extensions.json 文件](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)中枚举。

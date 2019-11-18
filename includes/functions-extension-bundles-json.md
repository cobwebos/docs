---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129071"
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

| 属性 | 说明 |
| -------- | ----------- |
| id | Microsoft Azure 函数扩展包的命名空间。 |
| version | 要安装的捆绑的版本。 Functions 运行时始终选取按版本范围或间隔定义的最宽松版本。 上述版本值允许使用从 1.0.0 到 2.0.0（不含 2.0.0）的所有捆绑版本。 有关详细信息，请参阅[用于指定版本范围的间隔表示法](/nuget/reference/package-versioning#version-ranges-and-wildcards)。 |

捆绑中的包发生变化时，捆绑版本将会递增。 当捆绑包中的包以主要版本递增时，会发生主要版本更改。 捆绑中的主要版本更改通常与主要版本的函数运行时中的更改一致。  

此[扩展插件文件](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)中枚举了默认捆绑安装的当前扩展集。

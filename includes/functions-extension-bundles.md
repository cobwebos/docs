---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442276"
---
安装绑定扩展的最简单方法是启用[扩展捆绑包](../articles/azure-functions/functions-bindings-register.md#extension-bundles)。 启用捆绑包后，会自动安装一组预定义的扩展包。

若要启用扩展捆绑包，请打开 *host.json* 文件并更新其内容以匹配以下代码：

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```
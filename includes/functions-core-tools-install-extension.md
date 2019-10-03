---
title: include 文件
description: include 文件
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ffd9e54c0f39b4256dbc83a336328797a8b53c45
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608087"
---
## <a name="register-extensions"></a>注册扩展

除了 HTTP 和计时器触发器，Functions 绑定在运行时版本 2.x 实现为扩展包。 在版本 2.x 的 Azure Functions 运行时，您必须显式注册在函数中使用的绑定类型的扩展。 此例外情况是 HTTP 绑定和计时器触发器，不需要扩展。

您可以选择单独安装绑定扩展，或者可以添加对 host.json 项目文件的扩展名捆绑包引用。 扩展捆绑包中删除拥有包兼容性问题时使用多个绑定类型的机会。 它是注册绑定扩展的推荐的方法。 扩展捆绑包也就不再需要安装.NET Core 2.x SDK。 

### <a name="extension-bundles"></a>扩展捆绑包

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

若要了解详细信息，请参阅[注册 Azure Functions 绑定扩展](../articles/azure-functions/functions-bindings-register.md#extension-bundles)。 将绑定添加到 functions.json 文件之前，您应将扩展捆绑包添加到 host.json。

### <a name="register-individual-extensions"></a>注册单独的扩展

如果你需要安装未在绑定中的扩展，可以手动注册特定绑定的单个扩展的包。 

> [!NOTE]
> 若要手动注册扩展，通过使用`func extensions install`，必须具有.NET Core 2.x SDK 安装。

更新 function.json 文件以包含函数所需的所有绑定后，请在项目文件夹中运行以下命令  。

```bash
func extensions install
```

该命令读取 function.json  文件以了解所需的程序包，安装这些包并重新生成扩展项目。 它在当前版本中添加任何新绑定，但不更新现有绑定。 使用 `--force` 选项可在安装新版本时将现有绑定更新为最新版本。
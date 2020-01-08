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
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564672"
---
## <a name="register-extensions"></a>注册扩展

除了 HTTP 和计时器触发器外，运行时版本2.x 和更高版本中的函数绑定作为扩展包实现。 在版本2.x 和更高版本的 Azure Functions 运行时中，必须显式注册函数中使用的绑定类型的扩展。 这种情况的例外是 HTTP 绑定和计时器触发器，它们不需要扩展。

您可以选择单独安装绑定扩展，也可以将扩展捆绑引用添加到 host json 项目文件中。 在使用多个绑定类型时，扩展捆绑消除了存在包兼容性问题的机会。 建议使用此方法来注册绑定扩展。 扩展捆绑还消除了安装 .NET Core 2.x SDK 的要求。 

### <a name="extension-bundles"></a>扩展捆绑

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

若要了解详细信息，请参阅[注册 Azure Functions 绑定扩展](../articles/azure-functions/functions-bindings-register.md#extension-bundles)。 在将绑定添加到函数 json 文件之前，应将扩展捆绑添加到 host。

### <a name="register-individual-extensions"></a>注册单个扩展

如果需要安装不在捆绑中的扩展，可以为特定绑定手动注册单独的扩展包。 

> [!NOTE]
> 若要使用 `func extensions install`手动注册扩展，必须安装 .NET Core 2.x SDK。

更新 function.json 文件以包含函数所需的所有绑定后，请在项目文件夹中运行以下命令。

```bash
func extensions install
```

该命令读取 function.json 文件以了解所需的程序包，安装这些包并重新生成扩展项目。 它在当前版本中添加任何新绑定，但不更新现有绑定。 使用 `--force` 选项可在安装新版本时将现有绑定更新为最新版本。

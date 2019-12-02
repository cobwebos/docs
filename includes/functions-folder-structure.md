---
title: include 文件
description: include 文件
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4460d19de1859a8a3c51d91d418b948b5d3532a6
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666725"
---
特定函数应用中所有函数的代码均位于根项目文件夹中，其中包含主机配置文件和一个或多个子文件夹。 每个子文件夹都包含单独函数的代码。 文件夹结构如下所示：

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

在函数运行时的版本2.x 中，函数应用中的所有函数必须共享同一语言堆栈。  

[Host json](../articles/azure-functions/functions-host-json.md)文件包含特定于运行时的配置，位于 function app 的根文件夹中。 *Bin*文件夹包含函数应用所需的包和其他库文件。 查看函数应用项目的语言特定要求：

* [C# 类库 (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 脚本 (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 脚本](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)

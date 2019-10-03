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
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594523"
---
特定函数应用中所有函数的代码均位于根项目文件夹中，其中包含主机配置文件和一个或多个子文件夹。 每个子文件夹包含单独函数的代码。 文件夹结构所示的以下表示形式：

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

在版本 2.x 的 Functions 运行时，函数应用中的所有函数必须共享相同的语言堆栈。  

[Host.json](../articles/azure-functions/functions-host-json.md)文件包含特定于运行时的配置，则函数应用根文件夹中。 一个*bin*文件夹包含包和函数应用都需要其他库文件。 查看函数应用项目的语言特定要求：

* [C# 类库 (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 脚本 (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 脚本](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)




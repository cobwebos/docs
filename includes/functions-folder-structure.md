---
title: include 文件
description: include 文件
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809564"
---
特定函数应用中所有函数的代码位于根文件夹 (`wwwroot`) 中，其中包含主机配置文件和一个或多个子文件夹。 每个子文件夹包含单独函数的代码，如以下示例所示：

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

host.json 文件包含一些特定于运行时的配置，位于函数应用的根文件夹中。 有关可用设置的信息，请参阅 [host.json 参考](../articles/azure-functions/functions-host-json.md)。

每个函数有一个对应的文件夹，其中包含一个或多个代码文件、function.json 配置和其他依赖项。 对于 C# 类库项目，已编译的类库 (.dll) 文件将部署到 `bin` 子文件夹。


---
title: include 文件
description: include 文件
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6fb497a5b6da00dece43c7f41ea3c411f385a2ba
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726881"
---
在本地开发函数时，可以使用 Azure Functions Core Tools 从终端或从命令提示符安装所需的扩展。 

更新 function.json 文件以包含函数所需的所有绑定后，在项目文件夹中运行 `func extensions install` 命令。 该命令读取 function.json 文件以查看并安装所需的包。

如要安装特定版本的包或要在编辑 function.json 文件之前安装包，请对包名称使用 `func extensions install` 命令，如下例所示：

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

将 `<target_version>` 替换为特定包版本。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。

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
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38944427"
---
在本地开发函数时，可以使用 Azure Functions Core Tools 从终端或从命令提示符安装所需的扩展。 

更新 function.json 文件以包含函数所需的所有绑定后，在项目文件夹中运行 `func extensions install` 命令。 该命令读取 function.json 文件以查看并安装所需的包。

如要安装特定版本的包或要在编辑 function.json 文件之前安装包，请对包名称使用 `func extensions install` 命令，如下例所示：

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

将 `<target_version>` 替换为特定包版本，例如 `3.0.0-beta5`。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。

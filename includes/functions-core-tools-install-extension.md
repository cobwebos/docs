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
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
在本地开发函数时，可以使用 Azure Functions Core Tools 从终端或从命令提示符安装所需的扩展。 以下 `func extensions install` 命令安装 Azure Cosmos DB 绑定扩展：

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

将 `<taget_version>` 替换为特定包版本。 在 [NuGet.org](https://nuget.org) 上的单个包页上列出了有效版本。

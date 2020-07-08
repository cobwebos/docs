---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905861"
---
若要从应用服务中的应用程序代码内部访问生成的控制台日志，请在[Cloud Shell](https://shell.azure.com)中运行以下命令，打开诊断日志记录：

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

的可能值为 `--level` ： `Error` 、 `Warning` 、 `Info` 和 `Verbose` 。 每个后续级别都包含之前的级别。 例如： `Error` 仅包括错误消息，并 `Verbose` 包含所有消息。

启用诊断日志记录后，运行以下命令以查看日志流：

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

如果没有立即看到控制台日志，请在 30 秒后重新查看。

> [!NOTE]
> 也可通过浏览器在 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 中检查日志文件。

若要随时停止日志流式处理，请键入 `Ctrl`+`C`。

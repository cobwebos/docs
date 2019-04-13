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
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551212"
---
你可以访问在容器内生成的控制台日志。 首先，启用容器日志记录通过在 Cloud Shell 中运行以下命令：

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

启用容器日志记录功能以后，请运行以下命令来查看日志流：

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

如果没有立即看到控制台日志，请在 30 秒后重新查看。

> [!NOTE]
> 您还可以检查与在浏览器的日志文件`https://<app-name>.scm.azurewebsites.net/api/logs/docker`。

若要停止日志流式处理在任何时候，请键入`Ctrl` + `C`。

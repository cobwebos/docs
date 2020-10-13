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
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822764"
---
可以访问在容器中生成的控制台日志。

首先，请运行以下命令，以便启用容器日志记录功能：

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

将 `<app-name>` 和 `<resource-group-name>` 替换为适合 Web 应用的名称。

启用容器日志记录功能以后，请运行以下命令来查看日志流：

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

如果没有立即看到控制台日志，请在 30 秒后重新查看。

若要随时停止日志流式处理，可键入 Ctrl+C 。

也可通过浏览器在 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 中检查日志文件。

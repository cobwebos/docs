---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/31/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: b7fb72682dbe9d9cb3e62e1a05ca8b89e9f97f4c
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246735"
---
在 `myAppServicePlan` 应用服务计划中创建一个 [Web 应用](../articles/app-service/containers/app-service-linux-intro.md)。 

在 Cloud Shell 中可以使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest) 命令。 在以下示例中，将 `<app_name>` 替换为全局唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。 运行时设置为 `DOTNETCORE|2.1`。 若要查看所有受支持的运行时，请运行 [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest)。 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "DOTNETCORE|2.1" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "DOTNETCORE|2.1" --deployment-local-git
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

已在 Linux 容器中创建了空的 Web 应用并启用了 Git 部署。

> [!NOTE]
> Git 远程的 URL 将显示在 `deploymentLocalGitUrl` 属性中，其格式为 `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`。 保存此 URL，因为后面需要它。
>

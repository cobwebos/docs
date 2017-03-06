---
title: "Azure CLI 脚本示例 - 从本地 Git 存储库部署 Web 应用 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 从本地 Git 存储库部署 Web 应用"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 343f7509e43c2305e6fa7d5e5cb9e6772e1a7e01
ms.openlocfilehash: 7c970e6d4447fed96d91143a502ec823eb60df62
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-from-a-local-git-repository"></a>从本地 Git 存储库部署 Web 应用

此示例脚本使用 Azure CLI 2.0 执行以下操作： 

* 在欧洲西部 Azure 区域的 Azure 应用服务中创建 Web 应用。
* 从本地 Git 存储库部署 Web 应用代码。
* 在浏览器中显示已部署的 Web 应用。

## <a name="prerequisites"></a>先决条件

* 运行 `az login` 登录到 Azure。
* 将 Web 应用代码提交到本地 Git 存储库。

## <a name="create-vm-sample"></a>创建 VM 示例

[!code-azurecli[主要](../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh "从本地 Git 存储库部署 Web 应用")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```azurecli
az group delete --name $webappname
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | 创建应用服务计划。 |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | 创建 Web 应用 |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) | 为本地 Git 存储库创建源控件配置。 |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | 在浏览器中打开 Web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/en-us/cli/azure/overview)。

可以在 [Azure CLI 示例]()中找到 Azure 应用服务 Web 应用的其他 CLI 脚本示例。

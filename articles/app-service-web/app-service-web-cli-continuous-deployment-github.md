---
title: "Azure CLI 脚本示例 - 从 GitHub 连续部署 Web 应用 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 从 GitHub 连续部署 Web 应用"
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
ms.sourcegitcommit: d19b07667bc2d34f860be46c299560fe9a788d53
ms.openlocfilehash: 649521d6f3740b099ab41c8ef70f7e4430aa5de9
ms.lasthandoff: 02/27/2017

---

# <a name="continuously-deploy-web-app-from-github"></a>从 GitHub 连续部署 Web 应用

此示例脚本使用 Azure CLI 2.0 执行以下操作： 

* 在欧洲西部 Azure 区域的 Azure 应用服务中创建 Web 应用。 
* 从 GitHub 部署 Web 应用代码。
* 在浏览器中显示已部署的 Web 应用。

## <a name="prerequisites"></a>先决条件

* 运行 `az login` 登录到 Azure。
* 将 Web 应用代码放入你拥有的 GitHub 存储库中。

> [!NOTE]
> 如果使用的是你未拥有的公共 GitHub 存储库，应用服务将从该 GitHub 存储库部署代码，但无法设置 SSH 密钥和连续部署所需的 webhook。
>
>

## <a name="create-vm-sample"></a>创建 VM 示例

[!code-azurecli[主要](../../cli_scripts/app-service/deploy-github/deploy-github.sh "从 GitHub 连续部署 Web 应用")]

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
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | 创建 Web 应用。 |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | 将 Web 应用与 Git 或 Mercurial 存储库相关联。 |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | 在浏览器中打开 Web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/en-us/cli/azure/overview)。

可以在 [Azure CLI 示例]()中找到 Azure 应用服务 Web 应用的其他 CLI 脚本示例。


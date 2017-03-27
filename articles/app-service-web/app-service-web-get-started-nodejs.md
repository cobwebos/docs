---
title: "在 Azure 中不到&5; 分钟创建你的第一个 Node.js Web 应用 | Microsoft 文档"
description: "了解如何通过部署一个示例 Node.js 应用，轻松地在应用服务中运行 Web 应用。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 412cc786-5bf3-4e1b-b696-6a08cf46501e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 89a9e29261e338aceb4ff6feb55cf344afeeb3d4
ms.lasthandoff: 03/21/2017


---
# <a name="create-your-first-nodejs-web-app-in-azure-in-five-minutes"></a>在 Azure 中不到&5; 分钟创建你的第一个 Node.js Web 应用
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

本快速入门帮助你在数分钟内将你的第一个 Node.js Web 应用部署到 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md)。

在开始之前，请确保已安装 Azure CLI。 有关详细信息，请参阅 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="log-in-to-azure"></a>登录 Azure
运行 `az login` 并按屏幕说明进行操作，以便登录到 Azure。
   
```azurecli
az login
```
   
## <a name="create-a-resource-group"></a>创建资源组   
创建[资源组](../azure-resource-manager/resource-group-overview.md)。 这是放置所有 Azure 资源（例如 Web 应用及其 SQL 数据库后端）的地方，这些资源需要集中进行管理。

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

若要查看适用于 `---location` 的可能值，请使用 `az appservice list-locations` Azure CLI 命令。

## <a name="create-an-app-service-plan"></a>创建应用服务计划
创建在 Linux 容器上运行的“标准”[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --is-linux --sku S1
```

## <a name="create-a-web-app"></a>创建 Web 应用
使用 `<app_name>` 中的唯一名称创建 Web 应用。

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="configure-the-linux-container"></a>配置 Linux 容器
将 Linux 容器配置为使用默认的 Node.js 6.9.3 映像。

```azurecli
az appservice web config update --node-version 6.9.3 --name <app_name> --resource-group myResourceGroup
```

## <a name="deploy-sample-application"></a>部署示例应用程序
从 GitHub 部署示例 Node.js 应用。

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>浏览到 Web 应用
若要查看应用在 Azure 中的实时运行情况，请运行此命令。

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

恭喜，你的第一个 Node.js Web 应用已在 Azure 应用服务中实时运行！

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

浏览预先创建的 [Web 应用 CLI 脚本](app-service-cli-samples.md)。


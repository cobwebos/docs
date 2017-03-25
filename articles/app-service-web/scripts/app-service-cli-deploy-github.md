---
title: "Azure CLI 脚本示例 - 从 GitHub 使用部署创建 Web 应用 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 从 GitHub 使用部署创建 Web 应用"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: ab4be9d9a03001789bed90e58d62fe1891608823
ms.lasthandoff: 03/11/2017

---


# <a name="create-a-web-app-with-deployment-from-github"></a>从 GitHub 使用部署创建 Web 应用

此示例脚本使用 Azure CLI 2.0 执行以下操作：

* 在欧洲西部 Azure 区域的 Azure 应用服务中创建 Web 应用。
* 从 GitHub 部署 Web 应用代码。
* 在浏览器中显示已部署的 Azure Web 应用。

## <a name="prerequisites"></a>先决条件

* 运行 `az login` 登录到 Azure。
* 将 Web 应用代码放入 GitHub 存储库中。

> [!NOTE]
> 如果使用的是你未拥有的公共 GitHub 存储库，应用服务将从该 GitHub 存储库部署代码，但无法设置 SSH 密钥和连续部署所需的 webhook。
>
>

## <a name="create-app-sample"></a>创建应用示例

[!code-azurecli[主要](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "从 GitHub 使用部署创建 Web 应用")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建应用服务计划。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | 创建 Azure Web 应用。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | 将 Azure Web 应用与 Git 或 Mercurial 存储库相关联。 |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | 在浏览器中打开 Azure Web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。

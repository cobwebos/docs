---
title: "Azure CLI 脚本示例 - 在 Docker 容器中创建 ASP.NET Core Web 应用 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 在 Docker 容器中创建 ASP.NET Core Web 应用"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: ba305ee8d73e7507f6e779bc9faff808b52da3df
ms.contentlocale: zh-cn
ms.lasthandoff: 09/09/2017

---

# <a name="create-an-aspnet-core-web-app-in-a-docker-container"></a>在 Docker 容器中创建 ASP.NET Core Web 应用

在此方案中，学习如何创建资源组、Linux 应用服务计划和 Web 应用，并使用 Docker 容器部署 ASP.NET Core 应用程序。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-docker/deploy-linux-docker.sh?highlight=6 "Linux Docker")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、Web 应用和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 这与 Azure Web 应用的服务器场类似。 |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | 创建 Azure Web 应用。 |
| [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set) | 为 Azure Web 应用设置 Docker 容器。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。


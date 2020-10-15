---
title: CLI：将应用连接到 Redis
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何将应用连接到 Azure Cache for Redis。
author: msangapu-msft
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: b0e9929d851133700e5b0475d5e5aef8b0b4ca18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88959742"
---
# <a name="connect-an-app-service-app-to-an-azure-cache-for-redis-using-cli"></a>使用 CLI 将应用服务应用连接到 Azure Redis 缓存

此示例脚本创建一个 Azure Redis 缓存和一个应用服务应用。 然后，它使用应用设置将 Azure Redis 缓存链接到应用。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、Azure Redis 缓存和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | 创建应用服务应用。 |
| [`az redis create`](/cli/azure/redis?view=azure-cli-latest#az-redis-create) | 创建新的用于 Redis 的 Azure 缓存实例。 |
| [`az redis list-keys`](/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys) | 列出用于 Redis 的 Azure 缓存实例的访问密钥。 |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | 创建或更新应用服务应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
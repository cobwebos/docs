---
title: "Azure CLI 脚本示例 - 将 Web 应用连接到 Redis 缓存 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 将 Web 应用连接到 Redis 缓存"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 043b1e90085a63c852e428ec25fa96e37eccb203
ms.lasthandoff: 03/21/2017

---

# <a name="connect-a-web-app-to-a-redis-cache"></a>将 Web 应用连接到 Redis 缓存

在此方案中，你将了解如何创建 Azure Redis 缓存和 Azure Web 应用。 然后，将使用应用设置将 Redis 缓存链接到 Web 应用。

必要时，请使用 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的说明安装 Azure CLI，然后运行 `az login` 创建与 Azure 的连接。

此示例在 Bash shell 中正常工作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Azure Redis 缓存")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、Web 应用、Redis 缓存和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建应用服务计划。 这与 Azure Web 应用的服务器场类似。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | 创建应用服务计划中的 Azure Web 应用。 |
| [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create) | 创建新的 Redis 缓存实例。 这将是数据存储位置。 |
| [az redis list-keys](https://docs.microsoft.com/en-us/cli/azure/redis#list-keys) | 列出 Redis 缓存实例的访问密钥。 |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | 创建或更新 Azure Web 应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。

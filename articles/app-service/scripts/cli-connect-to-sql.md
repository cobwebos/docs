---
title: CLI：将应用连接到 SQL 数据库
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何将应用连接到 SQL 数据库。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 5bdb09acf9adeaf0165512b144de0348ad4df743
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88959659"
---
# <a name="connect-an-app-service-app-to-sql-database-using-cli"></a>使用 CLI 将应用服务应用连接到 SQL 数据库

该示例脚本会创建一个应用服务应用，并在 Azure SQL 数据库中创建一个数据库。 然后，它使用应用设置将该数据库与该应用相链接。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL Database")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、SQL 数据库和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | 创建应用服务应用。 |
| [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) | 创建服务器。  |
| [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) | 创建新数据库。 |
| [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) | 生成数据库的连接字符串。 |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | 创建或更新应用服务应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
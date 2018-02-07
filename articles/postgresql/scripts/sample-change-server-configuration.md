---
title: "Azure CLI 脚本：更改服务器配置"
description: "此示例 CLI 脚本列出所有可用的服务器配置选项，并更新某个选项的值。"
services: postgres
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 334322557c83d5576c9a11def6bb0dc0e3ad1fec
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2018
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>使用 Azure CLI 列出和更新 Azure Database for PostgreSQL 服务器的配置
此示例 CLI 脚本列出所有 Azure Database for PostgreSQL 服务器的可用配置参数及其允许的值，并将 log_retention_days 设置为默认值以外的值。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本
在此示例脚本中，更改突出显示的行，以自定义管理员用户名和密码。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| **命令** | **说明** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | 创建托管数据库的 PostgreSQL 服务器。 |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | 列出 Azure Database for PostgreSQL 服务器的配置。 |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | 更新 Azure Database for PostgreSQL 服务器的配置。 |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) | 显示 Azure Database for PostgreSQL 服务器的配置。 |
| [az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/overview)。
- 尝试其他脚本：[Azure Database for PostgreSQL 的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 有关服务器参数的详细信息，请参阅[如何在 Azure 门户中配置服务器参数](../howto-configure-server-parameters-using-portal.md)。

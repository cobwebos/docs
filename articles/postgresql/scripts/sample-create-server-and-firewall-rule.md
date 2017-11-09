---
title: "Azure CLI 脚本 - 创建用于 PostgreSQL 的 Azure 数据库 | Microsoft Docs"
description: "Azure CLI 示例脚本 - 为 PostgreSQL 服务器创建 Azure 数据库，并配置服务器级防火墙规则。"
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: sample
ms.date: 05/31/2017
ms.openlocfilehash: e545b568cd57fdcf28ab33a5ebfa34a495111c7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>使用 Azure CLI 为 PostgreSQL 服务器创建 Azure 数据库并配置防火墙规则
此示例 CLI 脚本为 PostgreSQL 服务器创建 Azure 数据库，并配置服务器级防火墙规则。 成功运行此脚本后，可以通过所有 Azure 服务和配置的 IP 地址访问 PostgreSQL 服务器。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，以自定义管理员用户名和密码。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| **命令** | **说明** |
|---|---|
| [az group create](/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az postgres server create](/cli/azure/postgres/server#create) | 创建托管数据库的 PostgreSQL 服务器。 |
| [az postgres server firewall create](/cli/azure/postgres/server/firewall-rule#create) | 创建一个防火墙规则，以允许从输入的 IP 地址范围访问服务器及其下的所有数据库。 |
| [az group delete](/cli/azure/group#delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/overview)
- 尝试其他脚本：[Azure Database for PostgreSQL 的 Azure CLI 示例](../sample-scripts-azure-cli.md)

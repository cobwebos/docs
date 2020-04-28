---
title: 访问审核日志-Azure CLI-Azure Database for MySQL
description: 本文介绍如何在 Azure CLI 中配置和访问 Azure Database for MySQL 中的审核日志。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81384163"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>在 Azure CLI 中配置和访问审核日志

可以从 Azure CLI 配置[Azure Database for MySQL 审核日志](concepts-audit-logs.md)。

> [!IMPORTANT]
> 审核日志功能目前为预览版。

## <a name="prerequisites"></a>先决条件

若要逐步执行本操作方法指南，需要：

- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作方法指南要求使用 Azure CLI 版本 2.0 或更高版本。 若要确认版本，请在 Azure CLI 命令提示符下输入 `az --version`。 若要安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="configure-audit-logging"></a>配置审核日志记录

使用以下步骤启用和配置审核日志记录：

1. 通过将**audit_logs_enabled**参数设置为 "on" 来启用审核日志。 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. 通过更新 **audit_log_events** 参数，选择要记录的[事件类型](concepts-audit-logs.md#configure-audit-logging)。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. 通过更新 **audit_log_exclude_users** 参数添加不进行日志记录的 MySQL 用户。 通过提供 MySQL 用户名来指定用户。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. 通过更新**audit_log_include_users**参数添加要包含的任何特定 MySQL 用户进行日志记录。 通过提供 MySQL 用户名来指定用户。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>后续步骤
- 详细了解 Azure Database for MySQL 中的[审核日志](concepts-audit-logs.md)
- 了解如何在[Azure 门户](howto-configure-audit-logs-portal.md)中配置审核日志
---
title: 配置参数-Azure Database for PostgreSQL-灵活服务器
description: 本文介绍如何使用 Azure CLI 在 Azure Database for PostgreSQL 灵活的服务器中配置 Postgres 参数。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b14858cc99cac41e277b03171fd4cac4d6eafa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934814"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>使用 Azure CLI 自定义 Azure Database for PostgreSQL 灵活服务器的服务器参数

可以使用命令行接口 (Azure CLI) 列出、显示和更新 Azure PostgreSQL 服务器的配置参数。 在服务器级别公开引擎参数的子集，并且可对其进行修改。 

## <a name="prerequisites"></a>先决条件

若要逐步执行本操作方法指南，需要：
- 按照[创建 Azure Database for PostgreSQL](quickstart-create-server-cli.md) 创建 Azure Database for PostgreSQL 服务器和数据库
- 将 [Azure CLI](/cli/azure/install-azure-cli) 命令行接口安装在计算机上或通过浏览器在 Azure 门户中使用 [Azure Cloud Shell](../../cloud-shell/overview.md)。

## <a name="list-server-parameters-for-a-flexible-server"></a>列出灵活服务器的服务器参数

若要列出服务器中的所有可修改参数及其值，请运行 [az postgres 挠性-server parameters list](/cli/azure/postgres/flexible-server/parameter) 命令。

可以在资源组**myresourcegroup**下列出服务器**mydemoserver.postgres.database.azure.com**的服务器参数。

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>显示服务器参数详细信息

若要显示有关某个服务器的特定参数的详细信息，请运行 [az postgres 挠性-server parameter show](/cli/azure/postgres/flexible-server/parameter)  命令。

此示例显示 "资源组 myresourcegroup" 下服务器**mydemoserver.postgres.database.azure.com**的 "**日志 \_ 最小 \_ 消息**" 服务器参数的详细信息 **。**

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>修改服务器参数值

您还可以修改某个服务器参数的值，这会更新 PostgreSQL 服务器引擎的基础配置值。 若要更新参数，请使用 [az postgres 挠性-server parameter set](/cli/azure/postgres/flexible-server/parameter) 命令。 

更新 "资源组 myresourcegroup" 下服务器**mydemoserver.postgres.database.azure.com**的 "**日志 \_ 最小 \_ 消息**服务器" 参数 **。**

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

如果要重置参数的值，只需选择省略可选的 `--value` 参数，服务将应用默认值。 以上述示例为例，它类似于下面这样：

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

此命令将 **log \_ min \_ messages** 参数重置为默认值 **警告**。 有关服务器参数和允许值的详细信息，请参阅 PostgreSQL 文档，了解如何 [设置参数](https://www.postgresql.org/docs/12/config-setting.html)。

## <a name="next-steps"></a>后续步骤

- 若要配置和访问服务器日志，请参阅 [Azure Database for PostgreSQL 中的服务器日志](concepts-logging.md)

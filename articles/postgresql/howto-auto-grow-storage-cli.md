---
title: 自动增长存储-Azure CLI-Azure Database for PostgreSQL-单一服务器
description: 本文介绍如何使用 Azure Database for PostgreSQL 单一服务器中的 Azure CLI 配置存储自动增长。
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: b0dc2fbb168d9325439ee18a227f71a3b88ef9c8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767936"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>使用 Azure CLI 自动增长 Azure Database for PostgreSQL 存储-单一服务器
本文介绍如何在不影响工作负荷的情况下，将 Azure Database for PostgreSQL 服务器存储配置为扩展。

服务器[达到存储限制](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit)时，将设置为只读。 如果启用了 "存储自动增长"，则对于预配的存储空间小于 100 GB 的服务器，预配的存储大小将在可用存储空间超过 1 GB 或10% 的预配存储时立即增加 5 GB。 对于预配存储超过 100 GB 的服务器，当可用存储空间低于预配的存储大小的5% 时，预配的存储大小将增加5%。 [此处](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage)指定的最大存储限制为 "应用"。

## <a name="prerequisites"></a>必备组件
若要完成本操作指南，需要：
- [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作方法指南要求使用 Azure CLI 版本 2.0 或更高版本。 若要确认版本，请在 Azure CLI 命令提示符下输入 `az --version`。 若要安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="enable-postgresql-server-storage-auto-grow"></a>启用 PostgreSQL 服务器存储自动增长

使用以下命令在现有服务器上启用服务器自动增长存储：

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

使用以下命令创建新服务器时启用服务器自动增长存储：

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>后续步骤

了解[如何创建有关指标的警报](howto-alert-on-metric.md)。
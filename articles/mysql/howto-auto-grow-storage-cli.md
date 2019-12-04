---
title: 自动增长存储-Azure CLI-Azure Database for MySQL
description: 本文介绍如何使用 Azure Database for MySQL 中的 Azure CLI 来启用自动增长存储。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fb39c3afa2977038df7c9a233ba6720771f545dc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770622"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>使用 Azure CLI 自动增长 Azure Database for MySQL 存储
本文介绍如何在不影响工作负荷的情况下，将 Azure Database for MySQL 服务器存储配置为扩展。

服务器[达到存储限制](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)时，将设置为只读。 如果启用了 "存储自动增长"，则对于预配的存储空间小于 100 GB 的服务器，预配的存储大小将在可用存储空间超过 1 GB 或10% 的预配存储时立即增加 5 GB。 对于预配存储超过 100 GB 的服务器，当可用存储空间低于预配的存储大小的5% 时，预配的存储大小将增加5%。 [此处](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage)指定的最大存储限制为 "应用"。

## <a name="prerequisites"></a>必备组件
若要完成本操作指南，需要：
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作方法指南要求使用 Azure CLI 版本 2.0 或更高版本。 若要确认版本，请在 Azure CLI 命令提示符下输入 `az --version`。 若要安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="enable-mysql-server-storage-auto-grow"></a>启用 MySQL server 存储自动增长

使用以下命令在现有服务器上启用服务器自动增长存储：

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

使用以下命令创建新服务器时启用服务器自动增长存储：

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>后续步骤

了解[如何创建有关指标的警报](howto-alert-on-metric.md)。
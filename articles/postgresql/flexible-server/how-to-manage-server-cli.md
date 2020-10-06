---
title: 管理服务器 Azure CLI-Azure Database for PostgreSQL-灵活的服务器
description: 了解如何从 Azure CLI 管理 Azure Database for PostgreSQL 灵活的服务器。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0f3c21d5c7f328ddef000ca7f1eaa9d5e18e6ca9
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761883"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure Database for PostgreSQL 灵活的服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文介绍如何管理在 Azure 中部署的灵活服务器。 管理任务包括计算和存储缩放、管理员密码重置，以及查看服务器详细信息。

## <a name="prerequisites"></a>先决条件
如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。 本文要求在本地运行 Azure CLI 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

你将需要使用 [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) 命令登录到你的帐户。 请注意 id 属性，该属性指的是 Azure 帐户的订阅 ID。

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令选择帐户下的特定订阅。 记下 az login 输出中的 id 值，以用作命令中订阅参数的值。 如果有多个订阅，请选择应计费的资源所在的相应订阅。 若要获取所有订阅，请使用 [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> 如果尚未创建灵活的服务器，请创建一个，以便开始使用此操作方法指南。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

可以使用以下命令轻松增加计算层、Vcore 和存储空间。 你可以查看所有服务器操作，你可以运行 [az postgres server server 概述](https://azure.microsoft.com/services/postgresql/)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

下面是上述参数的详细信息：

**设置** | **示例值** | **说明**
---|---|---
name | mydemoserver | 输入服务器的唯一名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 必须包含 3 到 63 个字符。
resource-group | myresourcegroup | 提供 Azure 资源组的名称。
sku-name|Standard_D4ds_v3|输入计算层和大小的名称。 遵循以下约定 Standard_ {VM size} （简写形式）。 有关详细信息，请参阅[定价层](../concepts-pricing-tiers.md)。
storage-size | 6144 | 服务器的存储容量（以 MB 为单位）。 最小5120，增加1024增量。

> [!IMPORTANT]
> 不能缩小存储。 

## <a name="manage-postgresql-databases-on-a-server"></a>管理服务器上的 PostgreSQL 数据库

可以通过多个应用程序连接到 Azure Database for PostgreSQL 服务器。 如果客户端计算机已安装 PostgreSQL，则可以使用 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 的本地实例连接到 Azure PostgreSQL 服务器。 现在使用 psql 命令行实用工具连接到 Azure PostgreSQL 服务器。

1. 运行以下 psql 命令连接到 Azure Database for PostgreSQL 服务器

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   例如，以下命令使用访问凭据连接到 PostgreSQL 服务器 mydemoserver.postgres.database.azure.com**** 上名为“postgres”**** 的默认数据库。 提示输入密码时，输入之前选择的 `<server_admin_password>`。
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   连接后，psql 实用工具会显示 postgres 提示符，要求在其中键入 sql 命令。 在初始连接输出中可能会显示警告，因为所使用的 psql 版本可能不同于 Azure Database for PostgreSQL 服务器版本。

   psql 输出示例：

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > 如果未将防火墙配置为允许客户端的 IP 地址，则会出现以下错误：
   >
   > psql: 致命错误: 主机 `<IP address>`、用户 "myadmin"、数据库 "postgres" 没有 pg_hba.conf 条目，SSL 出现致命错误:需要 SSL 连接。 请指定 SSL 选项，然后重试。
   >
   > 确认在上面的防火墙规则步骤中允许你的客户端 IP。

2. 在提示符处键入以下命令，创建名为 "postgresdb" 的空数据库：

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. 在提示符下，执行以下命令以切换到新创建的数据库 **postgresdb**的连接：

    ```bash
    \c postgresdb
    ```

4. 键入 `\q`，再按 Enter 键退出 psql。

已通过 psql 连接到 Azure Database for PostgreSQL 服务器并创建了一个空用户数据库。

## <a name="reset-admin-password"></a>重置管理员密码
可以通过以下命令更改管理员角色的密码
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> 请确保密码最少8个字符，最多128个字符。
> 密码必须包含以下类别中的三类：英文大写字母、英文小写字母、数字和非字母数字字符。

## <a name="delete-a-server"></a>删除服务器

如果只想删除 PostgreSQL 灵活的服务器，可以运行 [az postgres 挠性-server delete](/cli/azure/PostgreSQL/server#az-PostgreSQL-flexible-server-delete) 命令。

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤

- [了解备份和还原概念](concepts-backup-restore.md)
- [优化和监视服务器](concepts-monitoring.md)

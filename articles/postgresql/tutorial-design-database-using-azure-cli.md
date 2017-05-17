---
title: "使用 Azure CLI 设计你的第一个 Azure Database for PostgreSQL | Microsoft Docs"
description: "本教程演示如何使用 Azure CLI 设计你的第一个 Azure Database for PostgreSQL。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: tutorial
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fbac141243f0f36aab1a2ef9f28b4e107fd2d390
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>使用 Azure CLI 设计你的第一个 Azure Database for PostgreSQL 
在本教程中，需使用 Azure CLI（命令行接口）以及其他实用工具了解如何完成以下操作：
> [!div class="checklist"]
> * 创建 Azure Database for PostgreSQL
> * 配置服务器防火墙
> * 使用 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 实用工具创建数据库
> * 加载示例数据
> * 查询数据
> * 更新数据
> * 还原数据

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

## <a name="log-in-to-azure"></a>登录 Azure

使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。
```azurecli
az login
```

如果有多个订阅，请选择资源所在的相应订阅或对资源进行计费的订阅。 使用 [az account set](/cli/azure/account#set) 命令选择帐户下的特定订阅 ID。
```azurecli
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。 以下示例在 `westus` 位置创建名为 `myresourcegroup` 的资源组。
```azurecli
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器

使用 **az postgres server create** 命令创建 [Azure Database for PostgreSQL 服务器](overview.md)。 服务器包含作为组进行管理的一组数据库。 

下面的示例使用服务器管理员登录名 `mylogin` 在资源组 `myresourcegroup` 中创建名为 `mypgserver-20170401` 的服务器。 服务器的名称会映射到 DNS 名称，因此该名称在 Azure 中需具有全局级别的唯一性。 用自己的值替换 `<server_admin_password>`。
```azurecli
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> 在此处指定的服务器管理员登录名和密码是你以后在本快速入门中登录到服务器及其数据库所必需的。 请牢记或记录此信息，以后会使用到它。

默认情况下，在服务器下创建 postgres 数据库。 [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) 是供用户、实用工具和第三方应用程序使用的默认数据库。 


## <a name="configure-a-server-level-firewall-rule"></a>配置服务器级防火墙规则

使用 az postgres server firewall-rule create 命令创建 Azure PostgreSQL 服务器级防火墙规则。 服务器级防火墙规则允许外部应用程序（如 [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) 或 [PgAdmin](https://www.pgadmin.org/)）通过 Azure PostgreSQL 服务防火墙连接到服务器。 

可以设置覆盖某个 IP 范围的防火墙规则，以便能通过网络连接。 下面的示例使用 **az postgres server firewall-rule create** 为某个 IP 地址范围创建防火墙规则 `AllowAllIps`。 若要开放所有 IP 地址，请使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束地址。
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL 服务器通过端口 5432 进行通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 5432 的出站流量。 如果是这样，则无法连接到 Azure SQL 数据库服务器，除非 IT 部门打开了端口 5432。
>

## <a name="get-the-connection-information"></a>获取连接信息

若要连接到服务器，需要提供主机信息和访问凭据。
```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

结果采用 JSON 格式。 记下 administratorLogin 和 fullyQualifiedDomainName。
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>使用 psql 连接到 Azure Database for PostgreSQL 数据库

如果客户端计算机已安装 PostgreSQL，则可以使用 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 的本地实例连接到 Azure PostgreSQL 服务器。 现在使用 psql 命令行实用工具连接到 Azure PostgreSQL 服务器。

1. 运行以下 psql 命令连接到 Azure Database for PostgreSQL 服务器
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  例如，以下命令使用访问凭据连接到 PostgreSQL 服务器 mypgserver-20170401.postgres.database.azure.com 上名为“postgres”的默认数据库。 提示输入密码时，输入之前选择的 `<server_admin_password>`。
  
  ```bash
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  连接到服务器后，在出现提示时创建空数据库。
```bash
CREATE DATABASE mypgsqldb;
```

3.  出现提示时，请执行以下命令，切换为连接到新建的数据库 mypgsqldb：
```bash
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>在数据库中创建表
现已介绍了如何连接 Azure Database for PostgreSQL，接下来将演示如何完成一些基本任务。

首先，创建表并加载一些数据。 创建一个跟踪清单信息的表。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

现可通过键入以下内容在表列表中查看新创建的表：
```bash
\dt
```

## <a name="load-data-into-the-tables"></a>将数据加载到表
创建表后，可以向其中插入一些数据。 在打开的命令提示窗口中，运行以下查询来插入几行数据
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

现已将两行示例数据加载到了之前创建的表中。

## <a name="query-and-update-the-data-in-the-tables"></a>查询和更新表中的数据
执行以下查询，从数据库表中检索信息。 
```sql
SELECT * FROM inventory;
```

还可以更新表中的数据
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

检索数据时行也会相应进行更新。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>将数据库还原到以前的时间点
假设你意外删除了某个表。 这是你不能轻易还原的内容。 借助 Azure Database for PostgreSQL，可返回到任意时间点（基本版为最近 7 天内，标准版为最近 35 天内）并将此时间点还原到新的服务器。 可以使用此新服务器恢复已删除的数据。 以下步骤将示例服务器还原到添加此表之前的时间点。

执行还原需要以下信息：
- 还原点：选择更改服务器前的时间点。 必须大于或等于源数据库的最早备份值。
- 目标服务器：提供一个要还原到的新服务器名称
- 源服务器：提供想从中进行还原的服务器的名称
- 位置：不能选择区域，此区域默认与源服务器相同
- 定价层：还原服务器时不能更改此值。 此值与源服务器相同。 

```azurecli
az postgres server restore --resource-group myResourceGroup --name mypgserver-20170401-restored --restore-point-in-time "2017-04-13 03:10" --source-server-name mypgserver-20170401
```

将服务器[还原到删除这些表之前的时间点](./howto-restore-server-portal.md)。 将服务器还原到不同的时间点，在指定时间点（前提是在[服务层](./concepts-service-tiers.md)保留时间段内）创建原始服务器的一个备份新服务器。

## <a name="next-steps"></a>后续步骤
本教程介绍如何使用 Azure CLI（命令行接口）以及其他实用工具完成以下任务：
> [!div class="checklist"]
> * 创建 Azure Database for PostgreSQL
> * 配置服务器防火墙
> * 使用 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 实用工具创建数据库
> * 加载示例数据
> * 查询数据
> * 更新数据
> * 还原数据

接下来了解如何使用 Azure 门户执行类似的任务，请查看本教程：[使用 Azure 门户设计你的第一个 Azure Database for PostgreSQL](tutorial-design-database-using-azure-portal.md)


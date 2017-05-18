---
title: "使用 Azure CLI 创建 Azure Database for PostgreSQL | Microsoft Docs"
description: "有关使用 Azure CLI（命令行界面）创建和管理 Azure Database for PostgreSQL 服务器的快速入门指南。"
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start create
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fa02abd9176fa17df3b2d7f396988b72c28a473c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>使用 Azure CLI 创建 Azure Database for PostgreSQL

Azure Database for PostgreSQL 是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 PostgreSQL 数据库。 Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门指南介绍了如何使用 Azure CLI 在 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)中创建 Azure Database for PostgreSQL 服务器。

若要完成本快速入门教程，请确保已安装最新的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-azure"></a>登录 Azure

使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。
```azurecli
az login
```
如果有多个订阅，请选择要计费的资源所在的相应订阅。 使用 [az account set](/cli/azure/account#set) 命令选择帐户下的特定订阅 ID。
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

下面的示例使用服务器管理员登录名 `mylogin` 在资源组 `myresourcegroup` 中创建名为 `mypgserver-20170401` 的服务器。 服务器的名称映射到 DNS 名称，因此需要在 Azure 中全局唯一。 用自己的值替换 `<server_admin_password>`。
```azurecli
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> 在此处指定的服务器管理员登录名和密码是你以后在本快速入门中登录到服务器及其数据库所必需的。 请牢记或记录此信息，以后会使用到它。

默认情况下，在服务器下创建 postgres 数据库。 [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) 是供用户、实用工具和第三方应用程序使用的默认数据库。 


## <a name="configure-a-server-level-firewall-rule"></a>配置服务器级防火墙规则

使用 az postgres server firewall-rule create 命令创建 Azure PostgreSQL 服务器级防火墙规则。 服务器级防火墙规则允许外部应用程序（如 [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) 或 [PgAdmin](https://www.pgadmin.org/)）通过 Azure PostgreSQL 服务防火墙连接到服务器。 

可以设置覆盖某个 IP 范围的防火墙规则，以便通过网络进行连接。 下面的示例使用 **az postgres server firewall-rule create** 为某个 IP 地址范围创建防火墙规则 `AllowAllIps`。 若要开放所有 IP 地址，请使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束地址。
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

## <a name="connect-to-postgresql-database-using-psql"></a>使用 psql 连接到 PostgreSQL 数据库

如果客户端计算机已安装 PostgreSQL，则可以使用 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 的本地实例连接到 Azure PostgreSQL 服务器。 现在使用 psql 命令行实用工具连接到 Azure PostgreSQL 服务器。

1. 运行以下 psql 命令连接到 Azure Database for PostgreSQL 服务器
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  例如，以下命令可使用访问凭据连接到 PostgreSQL 服务器 mypgserver-20170401.postgres.database.azure.com 上名为“postgres”的默认数据库。 提示输入密码时，输入之前选择的 `<server_admin_password>`。
  
  ```bash
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  连接到服务器后，在出现提示时创建空数据库。
```bash
CREATE DATABASE mypgsqldb;
```

3.  出现提示时，请执行以下命令，将连接切换到新建的数据库 mypgsqldb：
```bash
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>使用 pgAdmin 连接到 PostgreSQL 数据库

使用 GUI 工具 _pgAdmin_ 连接到 Azure PostgreSQL 服务器
1.    启动客户端计算机上的 _pgAdmin_ 应用程序。 可以从 http://www.pgadmin.org/ 安装 _pgAdmin_。
2.    从“快速链接”菜单选择“添加新服务器”。
3.    在“常规”选项卡的“创建 - 服务器”对话框中，输入服务器的唯一友好名称。 例如“Azure PostgreSQL 服务器”。
 ![pgAdmin 工具 - 创建 - 服务器](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.    在“连接”选项卡的“创建 - 服务器”对话框中，执行以下操作：
    - 在“主机名/地址”框中输入完全限定的服务器名称（例如 **mypgserver-20170401.postgres.database.azure.com**）。 
    - 将端口 5432 输入到“端口”框中。 
    - 将此前在本快速入门中获得的**服务器管理器登录名 (user@mypgserver)** 以及在创建服务器时输入的密码分别输入“用户名”和“密码”框中。
    - 选择“必需”作为“SSL 模式”。 默认情况下，所有 Azure PostgreSQL 服务器在创建时都会启用 SSL 强制。 若要关闭 SSL 强制，请参阅[强制 SSL](./concepts-ssl-connection-security.md) 中的详细信息。

    ![pgAdmin - 创建 - 服务器](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.    单击“保存” 。
6.    在浏览器左窗格中，展开“服务器组”。 选择你的服务器：**Azure PostgreSQL 服务器**。
7.  选择已连接到的**服务器**，然后在其下选择“数据库”。 
8.    右键单击“数据库”创建数据库。
9.    选择数据库名称 **mypgsqldb**，并选择其所有者作为服务器管理员登录名 **mylogin**。
10. 单击“保存”创建空数据库。
11. 在“浏览器”中，展开“服务器”组。 展开已创建的服务器，此时会看到其下的数据库 **mypgsqldb**。
 ![pgAdmin - 创建 - 数据库](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>清理资源

通过删除 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)，清除在快速入门中创建的所有资源。

> [!TIP]
> 本教程系列中的其他快速入门教程是在本文的基础上制作的。 如果计划继续使用后续的快速入门，请勿清除在本快速入门中创建的资源。 如果不打算继续，请在 Azure CLI 中执行以下步骤，删除通过此快速入门创建的所有资源。

```azurecli
az group delete --name myresourcegroup
```

如果只想删除新创建的服务器，请执行以下命令
```azurecli
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>后续步骤
- 使用[导出和导入](./howto-migrate-using-export-and-import.md)或[转储和还原](./howto-migrate-using-dump-and-restore.md)迁移数据库。
- 有关此服务的技术概述，请参阅[什么是 Azure Database for PostgreSQL？](overview.md)

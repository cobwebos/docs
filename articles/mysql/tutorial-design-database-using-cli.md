---
title: 教程：使用 Azure CLI 设计 Azure Database for MySQL
description: 本教程介绍如何使用 Azure CLI 2.0 从命令行创建和管理 Azure Database for MySQL 服务器。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: 6f77b24092a80708ba82c6d42b947c00d6a69b6d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-azure-cli"></a>教程：使用 Azure CLI 设计 Azure Database for MySQL

Azure Database for MySQL 是 Microsoft 云中基于 MySQL Community Edition 数据库引擎的一种关系型数据库服务。 在本教程中，需使用 Azure CLI（命令行接口）以及其他实用工具了解如何完成以下操作：

> [!div class="checklist"]
> * 创建 Azure Database for MySQL
> * 配置服务器防火墙
> * 使用 [mysql 命令行工具](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)创建数据库
> * 加载示例数据
> * 查询数据
> * 更新数据
> * 还原数据

可在浏览器中使用 Azure Cloud Shell，或在自己的计算机上[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)，运行本教程中的代码块。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

如果有多个订阅，请选择资源所在的相应订阅或对资源进行计费的订阅。 使用 [az account set](/cli/azure/account#az_account_set) 命令选择帐户下的特定订阅 ID。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组
使用 [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) 命令创建 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在 `westus` 位置创建名为 `myresourcegroup` 的资源组。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
## <a name="add-the-extension"></a>添加扩展
使用以下命令添加更新的 Azure Database for MySQL 管理扩展：
```azurecli-interactive
az extension add --name rdbms
``` 

检查是否已安装正确的扩展版本。 
```azurecli-interactive
az extension list
```

返回的 JSON 应包括以下内容： 
```json
{
    "extensionType": "whl",
    "name": "rdbms",
    "version": "0.0.3"
}
```

如果未返回版本 0.0.3，则请运行以下命令，对扩展进行更新： 
```azurecli-interactive
az extension update --name rdbms
```

## <a name="create-an-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器
使用 az mysql server create 命令创建 Azure Database for MySQL 服务器。 一个服务器可以管理多个数据库。 通常，每个项目或每个用户使用一个单独的数据库。

以下示例在资源组 `myresourcegroup` 中的 `westus` 处创建名为 `mydemoserver` 的 Azure Database for MySQL 服务器。 该服务器的管理员登录名为 `myadmin`。 它是第 4 代常规用途服务器，带有 2 个 2 vCore。 用自己的值替换 `<server_admin_password>`。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```
> [!IMPORTANT]
> 此处指定的服务器管理员登录名和密码是以后在本快速入门中登录到服务器及其数据库所必需的。 请牢记或记录此信息，以后会使用到它。


## <a name="configure-firewall-rule"></a>配置防火墙规则
使用 az mysql server firewall-rule create 命令创建 Azure Database for MySQL 服务器级防火墙规则。 服务器级防火墙规则允许外部应用程序（如 mysql 命令行工具或 MySQL Workbench）通过 Azure MySQL 服务防火墙连接到服务器。 

以下示例创建用于预定义的地址范围的防火墙规则。 此示例展示完整的可能 IP 地址范围。

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIPs --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>获取连接信息

若要连接到服务器，需要提供主机信息和访问凭据。
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

结果采用 JSON 格式。 记下 fullyQualifiedDomainName 和 administratorLogin。
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>使用 mysql 连接服务器
使用 [mysql 命令行工具](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)建立与 Azure Database for MySQL 数据库的连接。 在此示例中，该命令是：
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>创建空数据库
连接到服务器后，创建一个空数据库。
```sql
mysql> CREATE DATABASE mysampledb;
```

出现提示时，请运行以下命令，切换为连接此新建的数据库：
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>在数据库中创建表
现已介绍了如何连接 Azure Database for MySQL 数据库，接下来可以完成一些基本任务。

首先，创建表并加载一些数据。 创建一个存储清单信息的表。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>将数据加载到表
表格创建好后，可向其插入一些数据。 在打开的命令提示窗口中，运行以下查询来插入几行数据。
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

还可以更新表中的数据。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

检索数据时行也会相应进行更新。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>将数据库还原到以前的时间点
假设意外删除了此表。 这是你不能轻易还原的内容。 借助 Azure Database for MySQL，可返回到最近 35 天内的任意时间点并将此时间点还原到新的服务器。 可以使用此新服务器恢复已删除的数据。 以下步骤将示例服务器还原到添加此表之前的时间点。

执行还原需要以下信息：

- 还原点：选择更改服务器前的时间点。 必须大于或等于源数据库的最早备份值。
- 目标服务器：提供一个要还原到的新服务器名称
- 源服务器：提供想从其还原的服务器的名称
- 位置：不能选择区域，此区域默认与源服务器相同

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

`az mysql server restore` 命令需以下参数：
| 设置 | 建议的值 | 说明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  源服务器所在的资源组。  |
| 名称 | mydemoserver-restored | 通过还原命令创建的新服务器的名称。 |
| restore-point-in-time | 2017-04-13T13:59:00Z | 选择要还原到的时间点。 此日期和时间必须在源服务器的备份保留期限内。 使用 ISO8601 日期和时间格式。 例如，可使用自己的本地时区（如 `2017-04-13T05:59:00-08:00`），或使用 UTC Zulu 格式 `2017-04-13T13:59:00Z`。 |
| source-server | mydemoserver | 要从其还原的源服务器的名称或 ID。 |

将服务器还原到某个时间点会创建一个新服务器，该服务器是通过复制所指定的时间点之前那段时间的原始服务器而生成的。 还原的服务器的位置值和定价层值与源服务器相同。

该命令是同步的，且会在服务器还原后返回。 还原完成后，找到创建的新服务器。 验证数据是否按预期还原。

## <a name="next-steps"></a>后续步骤
本教程介绍：
> [!div class="checklist"]
> * 创建 Azure Database for MySQL 服务器
> * 配置服务器防火墙
> * 使用 [mysql 命令行工具](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)创建数据库
> * 加载示例数据
> * 查询数据
> * 更新数据
> * 还原数据

> [!div class="nextstepaction"]
> [Azure Database for MySQL - Azure CLI 示例](./sample-scripts-azure-cli.md)

---
title: 快速入门：创建服务器 - Azure CLI - Azure Database for MySQL
description: 本快速入门教程介绍如何使用 Azure CLI 在 Azure 资源组中为 MySQL 服务器创建 Azure 数据库。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: acf5f3cdf761e1773d6e9384a4ceb99a645ed7cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "74773498"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>快速入门：使用 Azure CLI 为 MySQL 服务器创建 Azure 数据库

> [!TIP]
> 考虑使用更简单的 [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI 命令（当前处于预览状态）。 试用[快速入门](./quickstart-create-server-up-azure-cli.md)。

本快速入门教程介绍如何使用 Azure CLI 在大约 5 分钟内在 Azure 资源组中为 MySQL 服务器创建 Azure 数据库。 Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

如果有多个订阅，请选择资源所在的相应订阅或对资源进行计费的订阅。 使用 [az account set](/cli/azure/account#az-account-set) 命令选择帐户下的特定订阅 ID。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组
使用 [az group create](/cli/azure/group#az-group-create) 命令创建 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在 `westus` 位置创建名为 `myresourcegroup` 的资源组。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器
使用 **[az mysql server create](/cli/azure/mysql/server#az-mysql-server-create)** 命令创建 Azure Database for MySQL 服务器。 一个服务器可以管理多个数据库。 通常，每个项目或每个用户使用一个单独的数据库。

**设置** | **示例值** | **说明**
---|---|---
name | mydemoserver | 选择用于标识 Azure Database for MySQL 服务器的唯一名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 必须包含 3 到 63 个字符。
resource-group | myresourcegroup | 提供 Azure 资源组的名称。
sku-name | GP_Gen5_2 | SKU 的名称。 请遵循简写约定 {pricing tier}\_{compute generation}\_{vCores}。 请参阅下表，详细了解 sku-name 参数。
backup-retention | 7 | 保留备份的时长。 单位为天。 范围为 7-35。 
geo-redundant-backup | 已禁用 | 是否应该为此服务启用异地冗余备份。 允许的值：“Enabled”、“Disabled”。
location | westus | 服务器的 Azure 位置。
ssl-enforcement | 已启用 | 是否应该为此服务启用 ssl。 允许的值：“Enabled”、“Disabled”。
storage-size | 51200 | 服务器的存储容量（单位是兆字节）。 有效的存储大小最小为 5120MB，以 1024MB 为增量。 请参阅[定价层](./concepts-pricing-tiers.md)文档，详细了解存储大小限制。 
版本 | 5.7 | MySQL 主版本。
admin-user | myadmin | 用于管理员登录的用户名。 它不能为“azure_superuser”、“admin”、“administrator”、“root”、“guest”或“public”       。
admin-password | *安全密码* | 管理员用户的密码。 该密码必须包含 8 到 128 个字符。 密码必须包含以下三个类别的字符：英文大写字母、英文小写字母、数字和非字母数字字符。


sku-name 参数值遵循 {定价层}\_{计算层代}\_{vCore 数} 约定，如以下示例中所示：
+ `--sku-name B_Gen5_1` 映射到基本、第 5 代和 1 个 vCore。 此选项是可用的最小 SKU。
+ `--sku-name GP_Gen5_32` 映射到常规用途、第 5 层和 32 个 vCore。
+ `--sku-name MO_Gen5_2` 映射到内存优化、第 5 层和 2 个 vCore。

请参阅[定价层](./concepts-pricing-tiers.md)文档来了解适用于每个区域和每个层的有效值。

下面的示例使用服务器管理员登录名 `myadmin` 在资源组 `myresourcegroup` 中创建位于“美国西部”区域的名为 `mydemoserver` 的 MySQL 5.7 服务器。 这是第 4 代常规用途服务器，带有 2 个 vCore    。 用自己的值替换 `<server_admin_password>`。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

> [!NOTE]
> 如果轻量级计算和 I/O 足以满足工作负荷要求，请考虑使用“基本”定价层。 请注意，在“基本”定价层中创建的服务器以后不能扩展到“常规用途”或“内存优化”定价层。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/mysql/)。
> 

## <a name="configure-firewall-rule"></a>配置防火墙规则
使用 **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create)** 命令创建 Azure Database for MySQL 服务器级防火墙规则。 服务器级防火墙规则允许外部应用程序（如 **mysql.exe** 命令行工具或 MySQL Workbench）通过 Azure MySQL 服务防火墙连接到服务器。 

以下示例创建名为 `AllowMyIP` 的防火墙规则，该规则允许从特定的 IP 地址 (192.168.0.1) 进行连接。 替代与要从其进行连接的地址相对应的 IP 地址或 IP 地址范围。 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> 连接到 Azure Database for MySQL 时，经端口 3306 进行通信。 如果尝试从企业网络内部进行连接，则可能不允许经端口 3306 的出站流量。 如果是这样，则无法连接到服务器，除非 IT 部门打开了端口 3306。
> 


## <a name="configure-ssl-settings"></a>配置 SSL 设置
默认情况下，在服务器和客户端应用程序之间强制实施 SSL 连接。 此默认设置可通过 Internet 加密数据流确保“传输中”数据的安全性。 为使本快速入门教程更简单，请禁用服务器的 SSL 连接。 建议不要对生产服务器禁用 SSL。 有关详细信息，请参阅[配置应用程序中的 SSL 连接性以安全连接到 Azure Database for MySQL](./howto-configure-ssl.md)。

以下示例禁止在 MySQL 服务器上强制实施 SSL。
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>获取连接信息

若要连接到服务器，需要提供主机信息和访问凭据。

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

结果采用 JSON 格式。 记下 fullyQualifiedDomainName  和 administratorLogin  。
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
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

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>使用 mysql.exe 命令行工具连接到服务器
使用 mysql.exe  命令行工具连接到服务器。 可从[此处](https://dev.mysql.com/downloads/)下载 MySQL 并将其安装在计算机上。 

键入下一命令： 

1. 使用 **mysql** 命令行工具连接到服务器：
   ```bash
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

2. 查看服务器状态：
   ```sql
   mysql> status
   ```
   如果一切顺利，命令行工具应输出以下文本：

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> 有关其他命令，请参阅 [MySQL 5.7 参考手册 - 第 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)。

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>使用 MySQL Workbench GUI 工具连接到服务器
1. 启动客户端计算机上的 MySQL Workbench 应用程序。 可以从[此处](https://dev.mysql.com/downloads/workbench/)下载并安装 MySQL Workbench。

2. 在“设置新连接”  对话框的“参数”  选项卡上，输入以下信息：

   ![设置新连接](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **设置** | 建议的值  | **说明** |
|---|---|---|
|   连接名称 | 我的连接 | 指定此连接的标签（这可以是任何内容） |
| 连接方法 | 选择“标准(TCP/IP)” | 使用 TCP/IP 协议连接到 Azure Database for MySQL |
| 主机名 | mydemoserver.mysql.database.azure.com | 先前记下的服务器名称。 |
| 端口 | 3306 | 使用 MySQL 的默认端口。 |
| 用户名 | myadmin@mydemoserver | 先前记下的服务器管理员登录名。 |
| 密码 | **** | 使用之前配置的管理员帐户密码。 |

单击“测试连接”  以测试是否所有参数均已正确配置。
现在，可以单击该连接，成功连接到服务器。

## <a name="clean-up-resources"></a>清理资源
如果不需要将这些资源用于其他快速入门/教程，则可通过执行以下命令将其删除： 

```azurecli-interactive
az group delete --name myresourcegroup
```

若要删除新创建的服务器，可运行 **[az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete)** 命令。
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 设计 MySQL 数据库](./tutorial-design-database-using-cli.md)

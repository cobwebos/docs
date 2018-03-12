---
title: "快速入门：创建 Azure Database for MySQL 服务器 - Azure CLI"
description: "本快速入门教程介绍如何使用 Azure CLI 在 Azure 资源组中为 MySQL 服务器创建 Azure 数据库。"
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: 2cd867f09550f922479955b885f10ff329715c1c
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 为 MySQL 服务器创建 Azure 数据库
本快速入门教程介绍如何使用 Azure CLI 在大约 5 分钟内在 Azure 资源组中为 MySQL 服务器创建 Azure 数据库。 Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

如果有多个订阅，请选择资源所在的相应订阅或对资源进行计费的订阅。 使用 [az account set](/cli/azure/account#az_account_set) 命令选择帐户下的特定订阅 ID。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组
使用 [az group create](/cli/azure/group#az_group_create) 命令创建 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

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
使用 **[az mysql server create](/cli/azure/mysql/server#az_mysql_server_create)** 命令创建 Azure Database for MySQL 服务器。 一个服务器可以管理多个数据库。 通常，每个项目或每个用户使用一个单独的数据库。

下面的示例使用服务器管理员登录名 `myadmin` 在资源组 `myresourcegroup` 中创建位于“美国西部”区域的名为 `mydemoserver` 的服务器。 这是**第 4 代****常规用途**服务器，带有 2 个 **vCore**。 服务器的名称映射到 DNS 名称，因此需要在 Azure 中全局唯一。 用自己的值替换 `<server_admin_password>`。
```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```

## <a name="configure-firewall-rule"></a>配置防火墙规则
使用 **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create)** 命令创建 Azure Database for MySQL 服务器级防火墙规则。 服务器级防火墙规则允许外部应用程序（如 **mysql.exe** 命令行工具或 MySQL Workbench）通过 Azure MySQL 服务防火墙连接到服务器。 

以下示例为预定义的地址范围创建了防火墙规则，该地址范围在本示例中是整个可能的 IP 地址范围。

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
允许所有 IP 地址是不安全的。 提供此示例是为了简单起见，但在实际方案中，需了解为应用程序和用户添加的准确的 IP 地址范围。 

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

结果采用 JSON 格式。 记下 **fullyQualifiedDomainName** 和 **administratorLogin**。
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

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>使用 mysql.exe 命令行工具连接到服务器
使用 mysql.exe 命令行工具连接到服务器。 可从[此处](https://dev.mysql.com/downloads/)下载 MySQL 并将其安装在计算机上。 还可以单击代码示例上的“试用”按钮或 Azure 门户中右上角工具栏的 `>_` 按钮，启动“Azure Cloud Shell”。

键入下一命令： 

1. 使用 **mysql** 命令行工具连接到服务器：
```azurecli-interactive
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
1.  启动客户端计算机上的 MySQL Workbench 应用程序。 可以从[此处](https://dev.mysql.com/downloads/workbench/)下载并安装 MySQL Workbench。

2.  在“设置新连接”对话框的“参数”选项卡上，输入以下信息：

   ![设置新连接](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **设置** | 建议的值 | **说明** |
|---|---|---|
|   连接名称 | 我的连接 | 指定此连接的标签（这可以是任何内容） |
| 连接方法 | 选择“标准(TCP/IP)” | 使用 TCP/IP 协议连接到 Azure Database for MySQL |
| 主机名 | mydemoserver.mysql.database.azure.com | 先前记下的服务器名称。 |
| 端口 | 3306 | 使用 MySQL 的默认端口。 |
| 用户名 | myadmin@mydemoserver | 先前记下的服务器管理员登录名。 |
| 密码 | **** | 使用之前配置的管理员帐户密码。 |

单击“测试连接”以测试是否所有参数均已正确配置。
现在，可以单击该连接，成功连接到服务器。

## <a name="clean-up-resources"></a>清理资源
如果不需要将这些资源用于其他快速入门/教程，则可通过执行以下命令将其删除： 

```azurecli-interactive
az group delete --name myresourcegroup
```

若要删除新创建的服务器，可运行 **[az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete)** 命令。
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 设计 MySQL 数据库](./tutorial-design-database-using-cli.md)

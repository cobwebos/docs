---
title: "快速入门：为 MySQL 服务器创建 Azure 数据库 - Azure CLI | Microsoft Docs"
description: "本快速入门教程介绍如何使用 Azure CLI 在 Azure 资源组中为 MySQL 服务器创建 Azure 数据库。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: hero-article
ms.date: 06/13/2017
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 3d66efa6935150c665a3f568e60c35ddbd70e8be
ms.contentlocale: zh-cn
ms.lasthandoff: 09/09/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 为 MySQL 服务器创建 Azure 数据库
本快速入门教程介绍如何使用 Azure CLI 在大约 5 分钟内在 Azure 资源组中为 MySQL 服务器创建 Azure 数据库。 Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。

如果还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

如果有多个订阅，请选择资源所在的相应订阅或对资源进行计费的订阅。 使用 [az account set](/cli/azure/account#set) 命令选择帐户下的特定订阅 ID。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组
使用 [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) 命令创建 [Azure 资源组](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在 `westus` 位置创建名为 `myresourcegroup` 的资源组。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>为 MySQL 服务器创建 Azure 数据库
使用 **az mysql server create** 命令为 MySQL 服务器创建 Azure 数据库。 一个服务器可以管理多个数据库。 通常，每个项目或每个用户使用一个单独的数据库。

以下示例在资源组 `myresourcegroup` 中的 `westus` 处创建名为 `myserver4demo` 的 Azure Database for MySQL 服务器。 该服务器的管理员登录名为 `myadmin`，密码为 `Password01!`。 该服务器是使用基本性能层以及在该服务器中的所有数据库之间共享的 50 个计算单元创建的。 可以根据应用程序需求增加或减少计算和存储。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name myserver4demo --location westus --admin-user myadmin --admin-password Password01! --performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>配置防火墙规则
使用 **az mysql server firewall-rule create** 命令创建用于 MySQL 的 Azure 数据库服务器级防火墙规则。 服务器级防火墙规则允许外部应用程序（如 **mysql.exe** 命令行工具或 MySQL Workbench）通过 Azure MySQL 服务防火墙连接到服务器。 

以下示例为预定义的地址范围创建了防火墙规则，该地址范围在本示例中是整个可能的 IP 地址范围。

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server myserver4demo --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>配置 SSL 设置
默认情况下，在服务器和客户端应用程序之间强制实施 SSL 连接。  这可通过 Internet 加密数据流确保“传输中”数据的安全性。  为使本快速入门教程更简单，我们为服务器禁用 SSL 连接。  建议不要对生产服务器进行此操作。  有关详细信息，请参阅[配置应用程序中的 SSL 连接性以安全连接到 Azure Database for MySQL](./howto-configure-ssl.md)。

以下示例禁止在 MySQL 服务器上强制实施 SSL。
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name myserver4demo -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>获取连接信息

若要连接到服务器，需要提供主机信息和访问凭据。

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name myserver4demo
```

结果采用 JSON 格式。 记下 **fullyQualifiedDomainName** 和 **administratorLogin**。
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "myserver4demo.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/myserver4demo",
  "location": "westus",
  "name": "myserver4demo",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>使用 mysql.exe 命令行工具连接到服务器
使用 mysql.exe 命令行工具连接到服务器。 可从[此处](https://dev.mysql.com/downloads/)下载 MySQL 并将其安装在计算机上。 还可以单击代码示例上的“试用”按钮或 Azure 门户中右上角工具栏的 `>_` 按钮，启动“Azure Cloud Shell”。

键入下一命令： 

1. 使用 **mysql** 命令行工具连接到服务器：
```azurecli-interactive
 mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. 查看服务器状态：
```sql
 mysql> status
```
如果一切顺利，命令行工具应输出以下文本：

```dos
C:\Users\>mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
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
Connection:             myserver4demo.mysql.database.azure.com via TCP/IP
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
| 主机名 | myserver4demo.mysql.database.azure.com | 先前记下的服务器名称。 |
| 端口 | 3306 | 使用 MySQL 的默认端口。 |
| 用户名 | myadmin@myserver4demo | 先前记下的服务器管理员登录名。 |
| 密码 | **** | 使用之前配置的管理员帐户密码。 |

单击“测试连接”以测试是否所有参数均已正确配置。
现在，可以单击该连接，成功连接到服务器。

## <a name="clean-up-resources"></a>清理资源
如果不需要将这些资源用于其他快速入门/教程，则可通过执行以下命令将其删除： 

```azurecli-interactive
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 设计 MySQL 数据库](./tutorial-design-database-using-cli.md)


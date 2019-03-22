---
title: 快速入门：创建 Azure Database for MariaDB 服务器 - Azure CLI
description: 本快速入门教程介绍如何使用 Azure CLI 在 Azure 资源组中创建 Azure Database for MariaDB 服务器。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: 7d94834523e331ff048f787760561739765e7023
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842288"
---
# <a name="create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>使用 Azure CLI 创建 Azure Database for MariaDB 服务器

Azure CLI 可用于从命令行或脚本创建和管理 Azure 资源。 本快速入门教程介绍如何使用 Azure CLI 在大约 5 分钟内在 Azure 资源组中创建 Azure Database for MariaDB 服务器。 

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

就本快速入门来说，如果在本地安装并使用 CLI，则必须运行 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

如果有多个订阅，请选择要计费的资源所在的订阅，或者本身要计费的订阅。 若要选择帐户中的特定订阅 ID，请使用 [az account set](/cli/azure/account#az-account-set) 命令：

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az-group-create) 命令创建 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在 `westus` 位置创建名为 `myresourcegroup` 的资源组：

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>创建 Azure Database for MariaDB 服务器

使用 [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) 命令创建 Azure Database for MariaDB 服务器。 一个服务器可以管理多个数据库。 通常，每个项目或每个用户使用一个单独的数据库。

设置 | 示例值 | 说明
---|---|---
名称 | **mydemoserver** | 输入用于标识 Azure Database for MariaDB 服务器的唯一名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 它必须包含 3 到 63 个字符。
resource-group | myresourcegroup | 输入 Azure 资源组的名称。
sku-name | **GP_Gen5_2** | SKU 的名称。 请遵循简写约定：*定价层*\_*计算代*\_*vCore 数*。 有关 **sku-name** 参数的详细信息，请查看此表后面的部分。
backup-retention | **7** | 保留备份的时长。 单位为天。 范围：7 到 35。 
geo-redundant-backup | **已禁用** | 是否应该为此服务启用异地冗余备份。 允许的值：**Enabled**、**Disabled**。
位置 | **westus** | 服务器的 Azure 位置。
ssl-enforcement | **已启用** | 是否应该为此服务器启用 SSL。 允许的值：**Enabled**、**Disabled**。
storage-size | **51200** | 服务器的存储容量（单位是兆字节）。 有效的存储大小最小为 5120 MB，以 1024 MB 为增量。 有关存储大小限制的详细信息，请参阅[定价层](./concepts-pricing-tiers.md)。 
版本 | **10.2** | MariaDB 主要引擎版本。
admin-user | **myadmin** | 用于管理员登录的用户名。 admin-user 参数不能是“azure_superuser”、“admin”、“administrator”、“root”、“guest”或“public”。
admin-password | 你的密码 | 管理员用户的密码。 密码必须包含 8 到 128 个字符。 密码必须包含以下三个类别的字符：英文大写字母、英文小写字母、数字和非字母数字字符。

sku-name 参数值遵循 {定价层}\_{计算层代}\_{vCore 数} 约定，如以下示例中所示：
+ `--sku-name B_Gen5_1` 映射到基本、第 5 代和 1 个 vCore。 此选项是可用的最小 SKU。
+ `--sku-name GP_Gen5_32` 映射到常规用途、第 5 层和 32 个 vCore。
+ `--sku-name MO_Gen5_2` 映射到内存优化、第 5 层和 2 个 vCore。

若要按区域和层了解有效值，请参阅[定价层](./concepts-pricing-tiers.md)。

以下示例在“美国西部”区域创建一个名为 **mydemoserver** 的服务器。 该服务器在资源组 **myresourcegroup** 中，其服务器管理员登录名为 **myadmin**。 该服务器为“第 5 代”服务器，属于“常规用途”定价层，有 2 个 vCore。 服务器名称映射到 DNS 名称，必须在 Azure 中全局唯一。 将 `<server_admin_password>` 替换为你自己的服务器管理员密码。

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> 如果轻量级计算和 I/O 足以满足工作负荷要求，请考虑使用“基本”定价层。 请注意，在“基本”定价层中创建的服务器以后不能扩展到“常规用途”或“内存优化”定价层。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/mariadb/)。
> 

## <a name="configure-a-firewall-rule"></a>配置防火墙规则

使用 [az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) 命令创建 Azure Database for MariaDB 服务器级防火墙规则。 服务器级防火墙规则允许外部应用程序（如 mysql 命令行工具或 MySQL Workbench）通过 Azure Database for MariaDB 服务防火墙连接到服务器。 

以下示例创建名为 `AllowMyIP` 的防火墙规则，该规则允许从特定的 IP 地址 (192.168.0.1) 进行连接。 替换 IP 地址或 IP 地址范围，这些地址或地址范围对应于要从其进行连接的位置。 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> 连接到 Azure Database for MariaDB 时，经端口 3306 进行通信。 如果尝试从企业网络内部进行连接，则可能不允许经端口 3306 的出站流量。 在这种情况下，若要连接到服务器，必须要求 IT 部门打开端口 3306。
> 

## <a name="configure-ssl-settings"></a>配置 SSL 设置

默认情况下，在服务器和客户端应用程序之间强制实施 SSL 连接。 此默认设置可通过 Internet 加密数据流确保“传输中”数据的安全性。 就本快速入门来说，请禁用服务器的 SSL 连接。 建议不要对生产服务器禁用 SSL。 有关详细信息，请参阅[配置应用程序中的 SSL 连接性以安全连接到 Azure Database for MariaDB](./howto-configure-ssl.md)。

以下示例禁止在 Azure Database for MariaDB 服务器上强制实施 SSL：
 
```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>获取连接信息

若要连接到服务器，需要提供主机信息和访问凭据。 若要获取连接信息，请运行以下命令：

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

结果采用 JSON 格式。 记下 fullyQualifiedDomainName 和 administratorLogin 的值。

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>使用 mysql 命令行工具连接到服务器

使用 mysql 命令行工具连接到服务器。 可[下载](https://dev.mysql.com/downloads/)命令行工具并将其安装在计算机上。 还可以通过以下方式访问命令行工具：在本文的代码示例中选择“试用”按钮。 访问命令行工具的另一种方式是在 Azure 门户的右上工具栏中选择 **>_** 按钮，以便打开 **Azure Cloud Shell**。

若要使用 mysql 命令行工具连接到服务器，请执行以下操作：

1. 连接到服务器：

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. 在 `mysql>` 提示符处查看服务器状态：

   ```sql
   status
   ```
   应会看到类似于以下文本的内容：

   ```bash
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> 有关其他命令，请参阅 [MySQL 5.7 参考手册 - 第 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)。

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>使用 MySQL Workbench 连接服务器

1. 打开客户端计算机上的 MySQL Workbench。 如果尚未安装，请[下载](https://dev.mysql.com/downloads/workbench/)并安装此应用程序。

2. 在“设置新连接”对话框的“参数”选项卡上，输入以下信息：

   ![设置新连接](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | 设置 | 建议的值 | 说明 |
   |---|---|---|
   | 连接名称 | **演示连接** | 输入此连接的标签（连接名称可以是任何内容） |
   | 连接方法 | **标准 (TCP/IP)** | 使用 TCP/IP 协议连接到 Azure Database for MariaDB |
   | 主机名 | **mydemoserver.mariadb.database.azure.com** | 前面记下的服务器名称。 |
   | 端口 | **3306** | Azure Database for MariaDB 的默认端口。 |
   | 用户名 | **myadmin\@mydemoserver** | 前面记下的服务器管理员登录名。 |
   | 密码 | 你的密码 | 使用之前设置的管理员帐户密码。 |

3. 若要检查所有参数是否已正确配置，请选择“测试连接”。

4. 选择该连接，成功连接到服务器。

## <a name="clean-up-resources"></a>清理资源

如果不需要将本快速入门中使用的资源用于其他快速入门或教程，则可通过运行以下命令将其删除： 

```azurecli-interactive
az group delete --name myresourcegroup
```

如果只需删除在本快速入门中创建的服务器，请运行 [az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete) 命令：

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 设计 MariaDB 数据库](./tutorial-design-database-cli.md)

---
title: 教程：设计服务器 - Azure PowerShell - Azure Database for MySQL
description: 本教程介绍如何使用 PowerShell 创建和管理 Azure Database for MySQL 服务器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: mvc
ms.openlocfilehash: 6bb3c25d4d4d24e626ad210c78c6ac64c560e43e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613903"
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-powershell"></a>教程：使用 PowerShell 设计 Azure Database for MySQL

Azure Database for MySQL 是 Microsoft 云中基于 MySQL Community Edition 数据库引擎的一种关系型数据库服务。 本教程介绍如何使用 PowerShell 和其他实用程序来执行以下操作：

> [!div class="checklist"]
> - 创建 Azure Database for MySQL
> - 配置服务器防火墙
> - 使用 [mysql 命令行工具](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)创建数据库
> - 加载示例数据
> - 查询数据
> - 更新数据
> - 还原数据

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

如果选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块，并使用 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 尽管 Az.MySql PowerShell 模块为预览版，但必须使用以下命令从 Az PowerShell 模块单独安装它：`Install-Module -Name Az.MySql -AllowPrerelease`。
> Az.MySql PowerShell 模块正式版推出后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

如果这是你第一次使用 Azure Database for MySQL 服务，必须注册 Microsoft.DBforMySQL 资源提供程序  。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定的订阅 ID。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在“美国西部”区域中创建名为“myresourcegroup”   的资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器

使用 `New-AzMySqlServer` cmdlet 创建 Azure Database for MySQL 服务器。 一个服务器可以管理多个数据库。 通常，每个项目或每个用户使用一个单独的数据库。

以下示例使用服务器管理员登录名 myadmin 在“美国西部”区域中的“myresourcegroup”资源组内创建名为“mydemoserver”的 MySQL 服务器     。 此服务器是常规用途定价层中的第 5 代服务器，其中启用了 2 个 vCore 和异地冗余备份。 记下示例的第一行中使用的密码，因为这是 MySQL 服务器管理员帐户的密码。

> [!TIP]
> 服务器名称映射到 DNS 名称，必须在 Azure 中全局唯一。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Sku 参数值遵循 pricing-tier\_compute-generation\_vCores 约定，如以下示例所示   。

- `-Sku B_Gen5_1` 映射到基本、第 5 代和 1 个 vCore。 此选项是可用的最小 SKU。
- `-Sku GP_Gen5_32` 映射到常规用途、第 5 层和 32 个 vCore。
- `-Sku MO_Gen5_2` 映射到内存优化、第 5 层和 2 个 vCore。

有关各区域和层级的有效 Sku 值的信息，请参阅 [Azure Database for MySQL 定价层](./concepts-pricing-tiers.md)  。

如果轻量级计算和 I/O 足以满足工作负载要求，请考虑使用基本定价层。

> [!IMPORTANT]
> 在基本定价层中创建的服务器以后无法扩展到常规用途或内存优化层级，并且无法异地复制。

## <a name="configure-a-firewall-rule"></a>配置防火墙规则

使用 `New-AzMySqlFirewallRule` cmdlet 创建 Azure Database for MySQL 服务器级防火墙规则。 服务器级防火墙规则允许外部应用程序（如 `mysql` 命令行工具或 MySQL Workbench）穿过 Azure Database for MySQL 服务防火墙连接到服务器。

以下示例创建名为 AllowMyIP 的防火墙规则，该规则允许从特定的 IP 地址 (192.168.0.1) 进行连接  。 替换与要从中进行连接的位置相对应的 IP 地址或 IP 地址范围。

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> 连接到 Azure Database for MySQL 时，经端口 3306 进行通信。 如果尝试从企业网络内部进行连接，则可能不允许经端口 3306 的出站流量。 这种情况下，只有在 IT 部门打开了端口 3306 时，才能连接到服务器。

## <a name="get-the-connection-information"></a>获取连接信息

若要连接到服务器，需要提供主机信息和访问凭据。 使用以下示例来确定连接信息。 记下 FullyQualifiedDomainName  和 AdministratorLogin  的值。

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>使用 mysql 命令行工具连接到服务器

使用 `mysql` 命令行工具连接到服务器。 若要下载并安装该命令行工具，请参阅 [MySQL 社区下载](https://dev.mysql.com/downloads/shell/)。 还可以通过在本文的代码示例中选择“试用”按钮，在 Azure Cloud Shell 中访问预装版本的 `mysql` 命令行工具  。 访问 Azure Cloud Shell 的其他方式包括：在 Azure 门户右上角的工具栏上选择“>_”按钮，或访问 [shell.azure.com](https://shell.azure.com/)  。

```azurepowershell-interactive
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>创建数据库

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

可以将服务器还原到以前的某个时间点。 将还原的数据复制到新服务器，现有服务器保持不变。 例如，如果意外删除了某个表，可以还原到删除时的时间点。 然后可以从服务器的已还原副本中检索缺少的表和数据。

若要还原服务器，请使用 `Restore-AzMySqlServer` PowerShell cmdlet。

### <a name="run-the-restore-command"></a>运行还原命令

若要还原服务器，请在 PowerShell 中运行以下示例。

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

将服务器还原到以前的某个时间点时，会创建新服务器。 原始服务器及其从指定时间点开始创建的数据库会复制到新服务器。

还原的服务器的位置值和定价层值与原始服务器保持相同。

还原过程完成后，找到新服务器，验证数据是否已按预期还原。 新服务器具有相同的服务器管理员登录名和密码，该登录名和密码在开始还原时对现有服务器有效。 可以从新服务器的“概述”  页更改密码。

还原期间创建的新服务器没有原始服务器上存在的 VNet 服务终结点。 必须单独为新服务器设置这些规则。 将还原原始服务器中的防火墙规则。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何使用 PowerShell 来备份和还原 Azure Database for MySQL 服务器](howto-restore-server-powershell.md)

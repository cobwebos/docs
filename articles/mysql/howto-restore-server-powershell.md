---
title: 备份和还原-Azure PowerShell-Azure Database for MySQL
description: 了解如何使用 Azure PowerShell 在 Azure Database for MySQL 中备份和还原服务器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershel
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 871b1ba81f672459378b23705ad5b96213667a73
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609037"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mysql-server-using-powershell"></a>如何使用 PowerShell 备份和还原 Azure Database for MySQL 服务器

定期备份 Azure Database for MySQL 服务器，以启用还原功能。 通过此功能，用户可将服务器及其所有数据库还原到新服务器上的某个较早时间点。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装[Az PowerShell 模块](/powershell/azure/install-az-ps)或在浏览器中[Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az PowerShell 模块处于预览阶段，但你必须使用以下命令从 Az PowerShell 模块单独安装它： `Install-Module -Name Az.MySql -AllowPrerelease`。
> Az PowerShell 模块公开发布后，它将成为未来 Az PowerShell 模块版本的一部分，并从 Azure Cloud Shell 中的本机提供。

如果选择在本地使用 PowerShell，请使用[AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>设置备份配置

在创建服务器时，可以选择为本地冗余或地域冗余备份配置服务器。

> [!NOTE]
> 创建服务器后，不能更改其具有的冗余类型、地理冗余与本地冗余。

当通过`New-AzMySqlServer`命令创建服务器时， **GeoRedundantBackup**参数将决定您的备份冗余选项。 如果**启用**，则执行异地冗余备份。 如果**已禁用**，则会创建本地冗余备份。

备份保留期由**BackupRetentionDay**参数设置。

有关在服务器创建过程中设置这些值的详细信息，请参阅[使用 PowerShell 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)。

可以如下所述更改服务器的备份保留期：

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

前面的示例将 mydemoserver 的备份保留期更改为 10 天。

备份保留期控制可检索时间点还原的时间，因为它基于可用备份。 下一部分中进一步介绍了时间点还原。

## <a name="server-point-in-time-restore"></a>服务器时间点还原

您可以将服务器还原到以前的时间点。 将还原的数据复制到新服务器，现有服务器将保持不变。 例如，如果意外删除了某个表，则可以还原到刚刚发生的时间。 然后可以从服务器的已还原副本中检索缺少的表和数据。

若要还原服务器，请使用`Restore-AzMySqlServer` PowerShell cmdlet。

### <a name="run-the-restore-command"></a>运行还原命令

若要还原服务器，请从 PowerShell 运行以下示例。

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Cmdlet 的 PointInTimeRestore 参数集需要以下参数： **PointInTimeRestore** `Restore-AzMySqlServer`

| 设置 | 建议的值 | 说明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  源服务器所在的资源组。  |
| 名称 | mydemoserver-restored | 通过还原命令创建的新服务器的名称。 |
| RestorePointInTime | 2020-03-13T13：59：00Z | 选择要还原的时间点。 此日期和时间必须在源服务器的备份保留期限内。 使用 ISO8601 日期和时间格式。 例如，你可以使用自己的本地时区，例如**2020-03-13T05：59： 00-08： 00**。 你还可以使用 UTC 祖鲁语格式，例如， **2018-03-13T13：59： 00Z**。 |
| UsePointInTimeRestore | `<SwitchParameter>` | 使用时间点模式还原。 |

将服务器还原到较早的时间点时，将创建新服务器。 原始服务器及其在指定时间点的数据库将被复制到新服务器。

还原的服务器的位置值和定价层值与原始服务器保持相同。

还原过程完成后，找到新服务器，验证数据是否已按预期还原。 新服务器具有在启动还原时对现有服务器有效的相同服务器管理员登录名和密码。 可以从新服务器的“概述”  页更改密码。

在还原期间创建的新服务器没有原始服务器上存在的 VNet 服务终结点。 必须为新服务器单独设置这些规则。 将从原始服务器还原防火墙规则。

## <a name="geo-restore"></a>异地还原

如果你为服务器配置了地理冗余备份，则可以从现有服务器的备份创建新的服务器。 可以在 Azure Database for MySQL 可用的任何区域中创建此新服务器。

若要使用异地冗余备份创建服务器，请使用带有`Restore-AzMySqlServer` **UseGeoRestore**参数的命令。

> [!NOTE]
> 首次创建服务器时，该服务器可能不会立即可用于异地还原。 填充必需的元数据可能需要几个小时。

若要将服务器还原到异地，请从 PowerShell 运行以下示例：

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

此示例将在 "美国东部" 区域中创建一个名为**mydemoserver-georestored**的新服务器，该服务器属于**myresourcegroup**。 它是第 5 代常规用途服务器，具有 8 个 vCore。 服务器是从**mydemoserver**的异地冗余备份创建的，也是在资源组**myresourcegroup**中创建的。

若要从现有服务器在不同的资源组中创建新的服务器，请使用**ResourceGroupName**参数指定新的资源组名称，如以下示例中所示：

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Cmdlet 的 GeoRestore 参数集需要以下参数： **GeoRestore** `Restore-AzMySqlServer`

| 设置 | 建议的值 | 说明  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | 新服务器所属的资源组的名称。|
|名称 | mydemoserver-georestored | 新服务器的名称。 |
|位置 | eastus | 新服务器的位置。 |
|UseGeoRestore | `<SwitchParameter>` | 使用地理模式还原。 |

使用异地还原创建新服务器时，它将继承与源服务器相同的存储大小和定价层，除非指定了**Sku**参数。

还原过程完成后，找到新服务器，验证数据是否已按预期还原。 新服务器具有在启动还原时对现有服务器有效的相同服务器管理员登录名和密码。 可以从新服务器的“概述”  页更改密码。

在还原期间创建的新服务器没有原始服务器上存在的 VNet 服务终结点。 必须为此新服务器单独设置这些规则。 将从原始服务器还原防火墙规则。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 自定义 Azure Database for MySQL 服务器参数](howto-configure-server-parameters-using-powershell.md)

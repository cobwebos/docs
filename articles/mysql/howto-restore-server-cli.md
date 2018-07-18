---
title: 如何在 Azure Database for MySQL 中备份和还原服务器
description: 了解如何使用 Azure CLI 在 Azure Database for MySQL 中备份和还原服务器。
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: 5325f23a13a181d912bbc8b26042de72855dc41e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319082"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>如何使用 Azure CLI 在 Azure Database for MySQL 中备份和还原服务器

## <a name="backup-happens-automatically"></a>自动进行备份
Azure Database for MySQL 服务器定期进行备份以便启用还原功能。 通过此功能，用户可将服务器及其所有数据库还原到新服务器上的某个较早时间点。

## <a name="prerequisites"></a>先决条件
若要完成本操作指南，需要：
- [Azure Database for MySQL 服务器和数据库](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> 本操作方法指南要求使用 Azure CLI 版本 2.0 或更高版本。 若要确认版本，请在 Azure CLI 命令提示符下输入 `az --version`。 若要安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="set-backup-configuration"></a>设置备份配置

创建服务器时，可以选择将服务器配置为创建本地冗余备份还是创建异地冗余备份。 

> [!NOTE]
> 创建服务器后，无法在异地冗余或本地冗余之间切换服务器冗余类型。
>

通过 `az mysql server create` 命令创建服务器时，`--geo-redundant-backup` 参数描述备份冗余选项。 如果为 `Enabled`，则创建异地冗余备份。 如果为 `Disabled`，则创建本地冗余备份。 

备份保留期由参数 `--backup-retention` 设置。 

有关在创建过程中设置这些值的详细信息，请参阅 [Azure Database for MySQL 服务器 CLI 快速入门](quickstart-create-mysql-server-database-using-azure-cli.md)。

可以如下所述更改服务器的备份保留期：

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

前面的示例将 mydemoserver 的备份保留期更改为 10 天。

备份保留期控制可以往回检索多长时间的时间点还原，因为它基于可用备份。 下一部分中进一步介绍了时间点还原。

## <a name="server-point-in-time-restore"></a>服务器时间点还原
可以将服务器还原到以前的某个时间点。 将还原的数据复制到新服务器，并且现有服务器将保持不变。 例如，如果某个表在今天中午意外删除，可以还原到就在中午之前的时间。 然后可以从服务器的已还原副本中检索缺少的表和数据。 

若要还原服务器，请使用 Azure CLI [az mysql server restore](/cli/azure/mysql/server#az_mysql_server_restore) 命令。

### <a name="run-the-restore-command"></a>运行还原命令

若要还原服务器，请在 Azure CLI 命令提示符下输入以下命令：

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mysql server restore` 命令需要以下参数：
| 设置 | 建议的值 | 说明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  源服务器所在的资源组。  |
| 名称 | mydemoserver-restored | 通过还原命令创建的新服务器的名称。 |
| restore-point-in-time | 2018-03-13T13:59:00Z | 选择要还原到的时间点。 此日期和时间必须在源服务器的备份保留期限内。 使用 ISO8601 日期和时间格式。 例如，可以使用自己的本地时区，如 `2018-03-13T05:59:00-08:00`。 也可以使用 UTC Zulu 格式，如 `2018-03-13T13:59:00Z`。 |
| source-server | mydemoserver | 要从其还原的源服务器的名称或 ID。 |

将服务器还原到以前的某个时间点时，会创建新服务器。 原始服务器及其从指定时间点开始创建的数据库会复制到新服务器。

还原的服务器的位置值和定价层值与原始服务器保持相同。 

还原过程完成后，找到新服务器，验证数据是否已按预期还原。

## <a name="geo-restore"></a>异地还原
如果为服务器配置了异地冗余备份，则可以从该现有服务器的备份创建新服务器。 可以在 Azure Database for MySQL 可用的任何区域中创建此新服务器。  

若要使用异地冗余备份创建服务器，请使用 Azure CLI `az mysql server georestore` 命令。

> [!NOTE]
> 首次创建服务器时，该服务器可能不会立即可用于异地还原。 填充必需的元数据可能需要几个小时。
>

若要异地还原服务器，请在 Azure CLI 命令提示符下输入以下命令：

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
此命令在 East US 创建一台名为 *mydemoserver-georestored* 且将属于 *myresourcegroup* 的新服务器。 它是一台常规用途第 4 代服务器，具有 8 个 vCore。 该服务器是基于也在资源组 *myresourcegroup* 中的 *mydemoserver* 的异地冗余备份创建的。

如果希望在与现有服务器不同的资源组中创建新服务器，则需要如下例所示在 `--source-server` 参数中限定服务器名称：

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

`az mysql server georestore` 命令需要以下参数：
| 设置 | 建议的值 | 说明  |
| --- | --- | --- |
|resource-group| myresourcegroup | 新服务器将属于的资源组的名称。|
|名称 | mydemoserver-georestored | 新服务器的名称。 |
|source-server | mydemoserver | 将使用其异地冗余备份的现有服务器的名称。 |
|location | eastus | 新服务器的位置。 |
|sku-name| GP_Gen4_8 | 此参数设置新服务器的定价层、计算层代和 vCore 数。 GP_Gen4_8 映射为一台第 4 代常规用途服务器，具有 8 个 vCore。|


>[!Important]
>通过异地还原创建新服务器时，它将继承与源服务器相同的存储大小和定价层。 在创建过程中无法更改这些值。 创建新服务器后，可以纵向扩展其存储大小。

还原过程完成后，找到新服务器，验证数据是否已按预期还原。

## <a name="next-steps"></a>后续步骤
- 详细了解服务的[备份](concepts-backup.md)。
- 详细了解[业务连续性](concepts-business-continuity.md)选项。

---
title: "如何在 Azure Database for MySQL 中备份和还原服务器 | Microsoft Docs"
description: "了解如何使用 Azure CLI 在 Azure Database for MySQL 中备份和还原服务器。"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/30/2017
ms.openlocfilehash: 6a63cccee5be89a1da8f9656c6e7d4c7422702ca
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-cli"></a>如何使用 Azure CLI 在 Azure Database for MySQL 中备份和还原服务器

使用 Azure Database for MySQL 将服务器数据库还原到 7 至 35 天以前的日期。

## <a name="prerequisites"></a>先决条件
若要完成本操作指南，需要：
- [Azure Database for MySQL 服务器和数据库](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 如果在本地安装并使用 Azure CLI，本操作指南要求使用 Azure CLI 2.0 或更高版本。 若要确认版本，请在 Azure CLI 命令提示符下输入 `az --version`。 若要安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="backup-happens-automatically"></a>自动进行备份
使用 Azure Database for MySQL 时，数据库服务每 5 分钟自动备份一次服务。 

对于基本层，备份将保留 7 天。 对于标准层，备份将保留 35 天。 有关详细信息，请参阅 [Azure Database for MySQL 定价层](concepts-service-tiers.md)。

使用此自动备份功能，可将服务器及其数据库还原到以前的某个日期或时间点。

## <a name="restore-a-database-to-a-previous-point-in-time-by-using-the-azure-cli"></a>使用 Azure CLI 将数据库还原到以前的时间点
使用 Azure Database for MySQL 可将服务器还原到以前的时间点。 将还原的数据复制到新服务器，并且现有服务器将保持不变。 例如，如果某个表在今天中午意外删除，可以还原到就在中午之前的时间。 然后可以从服务器的已还原副本中检索缺少的表和数据。 

若要还原服务器，请使用 Azure CLI [az mysql server restore](/cli/azure/mysql/server#restore) 命令。

### <a name="run-the-restore-command"></a>运行还原命令

若要还原服务器，请在 Azure CLI 命令提示符下输入以下命令：

```azurecli-interactive
az mysql server restore --resource-group myResourceGroup --name myserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server myserver4demo
```

`az mysql server restore` 命令需要以下参数：
| 设置 | 建议的值 | 说明  |
| --- | --- | --- |
| resource-group | myResourceGroup |  源服务器所在的资源组。  |
| 名称 | myserver-restored | 通过还原命令创建的新服务器的名称。 |
| restore-point-in-time | 2017-04-13T13:59:00Z | 选择要还原到的时间点。 此日期和时间必须在源服务器的备份保留期限内。 使用 ISO8601 日期和时间格式。 例如，可以使用自己的本地时区，如 `2017-04-13T05:59:00-08:00`。 也可以使用 UTC Zulu 格式，如 `2017-04-13T13:59:00Z`。 |
| source-server | myserver4demo | 要从其还原的源服务器的名称或 ID。 |

将服务器还原到以前的某个时间点时，会创建新服务器。 原始服务器及其从指定时间点开始创建的数据库会复制到新服务器。

还原的服务器的位置值和定价层值与原始服务器保持相同。 

`az mysql server restore` 命令是同步的。 还原服务器后，可以使用此命令针对不同的时间点再次重复上述过程。 

还原过程完成后，找到新服务器，验证数据是否已按预期还原。

## <a name="next-steps"></a>后续步骤
[Azure Database for MySQL 的连接库](concepts-connection-libraries.md)

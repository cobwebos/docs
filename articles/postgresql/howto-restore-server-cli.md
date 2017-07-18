---
title: "如何在 Azure Database for PostgreSQL 中还原服务器 | Microsoft Docs"
description: "本文介绍如何使用 Azure CLI 命令行在 Azure Database for PostgreSQL 中备份和还原服务器。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: fa63ccedb455e4972bfdd0e15069ad5e932b0fa3
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---

# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>如何使用 Azure CLI 在 Azure Database for PostgreSQL 中备份和还原服务器

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for PostgreSQL 服务器和数据库](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="backup-happens-automatically"></a>自动进行备份
使用 Azure Database for PostgreSQL 时，数据库服务每 5 分钟自动备份一次服务。 

如果使用的是基本层，备份将保留 7 天；如果使用的是标准层，则备份将保留 35 天。 有关详细信息，请参阅 [Azure Database for PostgreSQL 定价层](concepts-service-tiers.md)

通过此自动备份功能，可将服务器及其所有数据库还原到某个较早时间点，并还原到新服务器。

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-cli"></a>使用 Azure CLI 将数据库还原到之前的时间点
使用 Azure Database for PostgreSQL 可以将服务器还原到之前的时间点。 将还原的数据复制到新服务器，并且现有服务器将保持不变。 例如，如果某个表在今天中午意外删除，可以还原到就在中午之前的时间。 然后从服务器的已还原副本中检索缺少的表和数据。 

使用 [az postgres server restore](/cli/azure/postgres/server#restore) Azure CLI 命令执行还原。

### <a name="run-the-restore-command"></a>运行还原命令
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

`az postgres server restore` 命令需要以下参数：
| 设置 | 建议的值 | 说明  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  源服务器所在的资源组。  |
| name | mypgserver-restored | 通过还原命令创建的新服务器的名称。 |
| restore-point-in-time | 2017-04-13T13:59:00Z | 选择要还原到的时间点。 此日期和时间必须在源服务器的备份保留期限内。 使用 ISO8601 日期和时间格式。 例如，可使用自己的本地时区（如 `2017-04-13T05:59:00-08:00`），或使用 UTC Zulu 格式 `2017-04-13T13:59:00Z`。 |
| source-server | mypgserver-20170401 | 要从其还原的源服务器的名称或 ID。 |

将服务器还原到某个时间点时会创建一个新服务器，将其作为原始服务器复制（从指定的时间点开始）。 还原的服务器的位置和定价层值与源服务器相同。 该命令是同步的，且会等到服务器还原后返回。 

还原完成后，找到创建的新服务器。 验证数据是否如期还原。

## <a name="next-steps"></a>后续步骤
[Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)


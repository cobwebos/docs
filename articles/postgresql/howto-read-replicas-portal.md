---
title: 管理只读副本 - Azure 门户 - Azure Database for PostgreSQL（单一服务器）
description: 了解如何通过 Azure 门户管理 Azure Database for PostgreSQL（单一服务器）的只读副本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 8e148a3dac8435a08c0f1735cd35d06c700e1e84
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106622"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>通过 Azure 门户创建和管理 Azure Database for PostgreSQL（单一服务器）中的只读副本

本文介绍如何使用 Azure 门户在 Azure Database for PostgreSQL 中创建和管理只读副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。


## <a name="prerequisites"></a>先决条件
用作主服务器的 [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)。

## <a name="azure-replication-support"></a>Azure 复制支持

[读取副本](concepts-read-replicas.md)和[逻辑解码](concepts-logical.md)都依赖于 "Postgres 写入" 日志（WAL）来获取详细信息。 这两个功能需要来自 Postgres 的不同级别的日志记录。 逻辑解码需要比读取副本更高的日志记录级别。

若要配置正确的日志记录级别，请使用 Azure 复制支持参数。 Azure 复制支持有三个设置选项：

* **Off** -将最少的信息放在 WAL 中。 在大多数 Azure Database for PostgreSQL 服务器上，此设置不可用。  
* **副本**-比**Off**更详细。 这是[读取副本](concepts-read-replicas.md)运行所需的最小日志记录级别。 在大多数服务器上，此设置是默认设置。
* **逻辑**上比**副本**更详细。 这是要运行的逻辑解码的最小日志记录级别。 读取副本也可以通过此设置运行。

需要在更改此参数后重新启动服务器。 在内部，此参数设置 Postgres 参数 `wal_level` 、 `max_replication_slots` 和 `max_wal_senders` 。

## <a name="prepare-the-master-server"></a>准备主服务器

1. 在 Azure 门户中，选择要用作主服务器的现有 Azure Database for PostgreSQL 服务器。

2. 从服务器的菜单中，选择 "**复制**"。 如果 Azure 复制支持至少设置为**副本**，则可以创建读取副本。 

3. 如果 Azure 复制支持至少设置为**副本**，请设置。 选择“保存”。

   ![Azure Database for PostgreSQL 复制-设置副本并保存](./media/howto-read-replicas-portal/set-replica-save.png)

4. 通过选择 **"是"** 重新启动服务器以应用更改。

   ![Azure Database for PostgreSQL-复制-确认重新启动](./media/howto-read-replicas-portal/confirm-restart.png)

5. 操作完成后，会收到两个 Azure 门户通知。 一个通知是关于更新服务器参数的。 另一个通知是关于随后立即进行的服务器重启的。

   ![成功通知](./media/howto-read-replicas-portal/success-notifications.png)

6. 刷新 Azure 门户页，更新“复制”工具栏。 现在可以为此服务器创建只读副本。
   

## <a name="create-a-read-replica"></a>创建只读副本
若要创建只读副本，请遵循以下步骤：

1. 选择要用作主服务器的现有 Azure Database for PostgreSQL 服务器。 

2. 在服务器边栏中的“设置”下，选择“复制”。********

3. 选择“添加副本”。

   ![添加副本](./media/howto-read-replicas-portal/add-replica.png)

4. 输入只读副本的名称。 

    ![为副本命名](./media/howto-read-replicas-portal/name-replica.png)

5. 选择副本的位置。 默认位置与主服务器的位置相同。

    ![选择一个位置](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

6. 选择“确定”以确认创建该副本。****

使用与主服务器相同的计算和存储设置创建副本。 创建副本后，可以独立于主服务器更改多项设置：计算代系、vCore 数、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。

> [!IMPORTANT]
> 将主服务器设置更新为新值之前，请将副本设置更新为一个相等或更大的值。 此操作可帮助副本与主服务器发生的任何更改保持同步。

创建只读副本后，可以在“复制”窗口中查看它：****

![在“复制”窗口中查看新副本](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>停止复制
可以停止主服务器与只读副本之间的复制。

> [!IMPORTANT]
> 停止复制到主服务器和只读副本后，无法撤消该操作。 只读副本将成为支持读取和写入的独立服务器。 独立服务器不能再次成为副本。

若要在 Azure 门户中停止主服务器与只读副本之间的复制，请遵循以下步骤：

1. 在 Azure 门户中，选择 Azure Database for PostgreSQL 主服务器。

2. 在服务器菜单中的“设置”下，选择“复制”。********

3. 选择要停止复制的副本服务器。

   ![选择副本](./media/howto-read-replicas-portal/select-replica.png)
 
4. 选择“停止复制”。

   ![选择停止复制](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. 选择“确定”以停止复制。****

   ![确认停止复制](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>删除主服务器
若要删除主服务器，请遵循删除独立 Azure Database for PostgreSQL 服务器的相同步骤。 

> [!IMPORTANT]
> 删除主服务器后，将停止复制到所有只读副本。 只读副本将成为支持读取和写入的独立服务器。

若要在 Azure 门户中删除服务器，请遵循以下步骤：

1. 在 Azure 门户中，选择 Azure Database for PostgreSQL 主服务器。

2. 此时会打开该服务器的“概述”页。**** 选择“删除”。

   ![在服务器的“概述”页上，选择删除主服务器](./media/howto-read-replicas-portal/delete-server.png)
 
3. 输入要删除的主服务器的名称。 选择“删除”以确认删除主服务器。****

   ![确认删除主服务器](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>删除副本
可以像删除主服务器一样删除只读副本。

- 在 Azure 门户中，打开只读副本的“概述”页。**** 选择“删除”。

   ![在副本的“概述”页上，选择删除该副本](./media/howto-read-replicas-portal/delete-replica.png)
 
也可以在“复制”窗口中遵循以下步骤删除只读副本：****

1. 在 Azure 门户中，选择 Azure Database for PostgreSQL 主服务器。

2. 在服务器菜单中的“设置”下，选择“复制”。********

3. 选择要删除的只读副本。

   ![选择要删除的副本](./media/howto-read-replicas-portal/select-replica.png)
 
4. 选择 "**删除副本**"。

   ![选择“删除副本”](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. 输入要删除的副本的名称。 选择“删除”以确认删除该副本。****

   ![确认删除副本](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>监视副本
可以使用两个指标来监视只读副本。

### <a name="max-lag-across-replicas-metric"></a>“副本的最大滞后时间”指标
“副本的最大滞后时间”指标显示主服务器与滞后时间最长的副本之间的滞后时间（以字节为单位）****。 

1.  在 Azure 门户中，选择 Azure Database for PostgreSQL 主服务器。

2.  选择“指标”。 在 "**指标**" 窗口中，选择**跨副本的最大延迟**。

    ![监视副本的最大滞后时间](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  对于“聚合”，请选择“最大”。********


### <a name="replica-lag-metric"></a>“副本滞后时间”指标
“副本滞后时间”指标显示自从在副本上最后一次执行重放事务以来所经历的时间。**** 如果主服务器上未发生任何事务，则该指标会反映此滞后时间。

1. 在 Azure 门户中，选择 Azure Database for PostgreSQL 只读副本。

2. 选择“指标”。 在 "**指标**" 窗口中，选择 "**副本滞后**时间"。

   ![监视副本滞后时间](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. 对于“聚合”，请选择“最大”。******** 
 
## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure Database for PostgreSQL 中的只读副本](concepts-read-replicas.md)。
* 了解如何[通过 Azure CLI 和 REST API 创建和管理只读副本](howto-read-replicas-cli.md)。
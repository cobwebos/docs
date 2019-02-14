---
title: 在 Azure 门户中管理 Azure Database for PostgreSQL 的只读副本
description: 本文介绍如何在 Azure 门户中管理 Azure Database for PostgreSQL 只读副本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 37150f67e29dae0357c978cfaea9abeebeef428c
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691399"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>如何在 Azure 门户中创建和管理只读副本

> [!IMPORTANT]
> 只读副本功能目前以公共预览版提供。


本文介绍如何使用 Azure 门户在 Azure Database for PostgreSQL 服务中创建和管理只读副本。 若要详细了解只读副本，请[阅读概念文档](concepts-read-replicas.md)。

## <a name="prerequisites"></a>先决条件
- 用作主服务器的 [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)。

## <a name="prepare-the-master-server"></a>准备主服务器
此主服务器准备步骤仅适用于“常规用途”和“内存优化”服务器。

主服务器上的 **azure.replication_support** 参数必须设置为 REPLICA。 更改此参数后，需要重启服务器才能使更改生效。

1. 在 Azure 门户中，选择要用作主服务器的现有 Azure Database for PostgreSQL 服务器。

2. 在左侧菜单中选择“服务器参数”。

3. 搜索 **azure.replication_support**。

   ![Azure Database for PostgreSQL - azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. 将 **azure.replication_support** 设置为 REPLICA。 **保存**更改。

   ![Azure Database for PostgreSQL - 设置为 REPLICA 并保存](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. 保存后，你会收到一条通知。

   ![Azure Database for PostgreSQL - 保存后的通知](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. 保存更改后，重启服务器以应用更改。 若要了解如何重启服务器，请参阅[重启文档](howto-restart-server-portal.md)。


## <a name="create-a-read-replica"></a>创建只读副本
可以使用以下步骤创建只读副本：
1.  选择要用作主服务器的现有 Azure Database for PostgreSQL 服务器。 

2.  在菜单中的“设置”下，选择“复制”。

   如果尚未在“常规用途”或“内存优化”主服务器上将 **azure.replication_support** 设置为 REPLICA 并重启服务器，将有一条消息指示你这样做。 请遵照指示，然后继续创建。

3.  选择“添加副本”。

   ![Azure Database for PostgreSQL - 添加副本](./media/howto-read-replicas-portal/add-replica.png)

4.  输入副本服务器的名称，然后选择“确定”以确认创建副本。

   ![Azure Database for PostgreSQL - 为副本命名](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> 只读副本使用与主服务器相同的服务器配置创建。 创建副本后，可以独立于主服务器更改定价层（“基本”除外）、计算代系、vCore 数、存储和备份保留期。

> [!IMPORTANT]
> 将主服务器的配置更新为新值之前，应将副本的配置更新为与这些新值相等或更大的值。 否则会导致出错。 这可以确保副本能够与主服务器的更改保持同步。 


创建副本服务器后，可以在“复制”窗口中查看它。

![Azure Database for PostgreSQL - 新副本](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>停止复制

> [!IMPORTANT]
> 停止复制到服务器操作不可逆。 一旦主服务器和副本服务器之间的复制停止，无法撤消。 然后，副本服务器将成为独立服务器，并且现在支持读取和写入。 此服务器不能再次成为副本服务器。

若要在 Azure 门户中停止主服务器与副本之间的复制，请使用以下步骤：
1.  在 Azure 门户中，选择 Azure Database for PostgreSQL 主服务器。

2.  在菜单中的“设置”下，选择“复制”。

3.  选择要停止复制的副本服务器。

   ![Azure Database for PostgreSQL - 选择副本](./media/howto-read-replicas-portal/select-replica.png)
 
4.  选择“停止复制”。

   ![Azure Database for PostgreSQL - 选择“停止复制”](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  单击“确定”以确认要停止复制。

   ![Azure Database for PostgreSQL - 确认停止复制](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>删除主服务器

> [!IMPORTANT]
> 删除主服务器会停止复制到所有副本服务器。 副本服务器成为现在支持读取和写入的独立服务器。
遵循删除独立 Azure Database for PostgreSQL 服务器的相同步骤删除主服务器。 若要在 Azure 门户中删除服务器，请执行以下操作：

1.  在 Azure 门户中，选择 Azure Database for PostgreSQL 主服务器。

2.  在“概述”中，选择“删除”。

   ![Azure Database for PostgreSQL - 删除服务器](./media/howto-read-replicas-portal/delete-server.png)
 
3.  键入主服务器的名称，然后选择“删除”以确认删除主服务器。

   ![Azure Database for PostgreSQL - 确认删除](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>删除副本
若要删除只读副本，可以遵循上述删除主服务器的相同步骤。 首先打开副本的“概述”页，然后选择“删除”。

   ![Azure Database for PostgreSQL - 删除副本](./media/howto-read-replicas-portal/delete-replica.png)
 
或者，可以从“复制”窗口删除它。
1.  在 Azure 门户中，选择 Azure Database for PostgreSQL 主服务器。

2.  在菜单中的“设置”下，选择“复制”。

3.  选择要删除的副本服务器。 

   ![Azure Database for PostgreSQL - 选择副本](./media/howto-read-replicas-portal/select-replica.png)
 
4.  选择“删除副本”。

   ![Azure Database for PostgreSQL - 选择“删除副本”](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  键入副本的名称，然后选择“删除”以确认删除副本。

   ![Azure Database for PostgreSQL - 确认删除副本](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>监视副本
### <a name="max-lag-across-replicas"></a>副本的最大滞后时间
“副本的最大滞后时间”指标显示主服务器与滞后时间最长的副本之间的滞后时间（以字节为单位）。 

1.  在 Azure 门户中，选择 Azure Database for PostgreSQL **主服务器**。

2.  选择“指标”。 在“指标”窗口中，选择“副本的最大滞后时间”。

    ![Azure Database for PostgreSQL - 监视副本的最大滞后时间](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  选择“最大”作为聚合。 

### <a name="replica-lag"></a>副本滞后时间
“副本滞后时间”指标显示自从在此副本上最后一次执行重放事务以来所经历的时间。 如果主服务器上未发生任何事务，则该指标会反映此滞后时间。

1.  在 Azure 门户中，选择 Azure Database for PostgreSQL **副本**服务器。

2.  选择“指标”。 在“指标”窗口中，选择“副本滞后时间”。

   ![Azure Database for PostgreSQL - 监视副本滞后时间](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  选择“最大”作为聚合。 
 
## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for PostgreSQL 中的只读副本](concepts-read-replicas.md)。
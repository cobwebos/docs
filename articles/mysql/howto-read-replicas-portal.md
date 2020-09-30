---
title: 管理只读副本 - Azure 门户 - Azure Database for MySQL
description: 了解如何使用 Azure 门户在 Azure Database for MySQL 中设置和管理只读副本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 8f3c85bb6dab906528c3c442cbdd1b762d8145e0
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533122"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>如何使用 Azure 门户在 Azure Database for MySQL 中创建和管理只读副本

本文介绍如何使用 Azure 门户在 Azure Database for MySQL 服务中创建和管理只读副本。

## <a name="prerequisites"></a>先决条件

- 将用作源服务器的 [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md) 。

> [!IMPORTANT]
> 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MySQL 服务器。 请确保源服务器位于其中一个定价层中。

## <a name="create-a-read-replica"></a>创建只读副本

> [!IMPORTANT]
> 为没有现有副本的源创建副本时，会先重新启动源以准备复制的副本。 请考虑这一点并在非高峰期执行这些操作。

可以使用以下步骤创建只读副本服务器：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择要用作主服务器的现有 Azure Database for MySQL 服务器。 此操作将打开“概述”页。

3. 从菜单中的“设置”下，选择“复制”。

4. 选择“添加副本”。

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL - 复制":::

5. 输入副本服务器的名称。

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL - 复制":::

6. 选择副本服务器的位置。 默认位置与源服务器的位置相同。

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Azure Database for MySQL - 复制":::

   > [!NOTE]
   > 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

7. 选择“确定”以确认创建副本。

> [!NOTE]
> 只读副本使用与主服务器相同的服务器配置创建。 副本服务器配置在创建后可以更改。 副本服务器始终在与源服务器相同的资源组和订阅中创建。 如果要将副本服务器创建到不同的资源组或不同的订阅，可以在创建后[移动副本服务器](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)。 建议副本服务器的配置应保留为等于或大于源的值，以确保副本能够与主副本保持同步。

一旦创建副本服务器，可以从“复制”边栏选项卡中进行查看。

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL - 复制":::

## <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器

> [!IMPORTANT]
> 停止复制到服务器操作不可逆。 源和副本之间的复制停止后，无法撤消。 然后，副本服务器将成为独立服务器，并且现在支持读取和写入。 此服务器不能再次成为副本服务器。

若要从 Azure 门户停止源服务器和副本服务器之间的复制，请执行以下步骤：

1. 在 Azure 门户中，选择源 Azure Database for MySQL 服务器。 

2. 从菜单中的“设置”下，选择“复制”。

3. 选择要停止复制的副本服务器。

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL - 复制":::

4. 选择“停止复制”。

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL - 复制":::

5. 通过单击“确定”，确认要停止复制。

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL - 复制":::

## <a name="delete-a-replica-server"></a>删除副本服务器

若要从 Azure 门户删除只读副本服务器，请使用以下步骤：

1. 在 Azure 门户中，选择源 Azure Database for MySQL 服务器。

2. 从菜单中的“设置”下，选择“复制”。

3. 选择要删除的副本服务器。

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL - 复制":::

4. 选择“删除副本”

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL - 复制":::

5. 键入副本的名称，然后单击“删除”以确认删除副本。  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL - 复制":::

## <a name="delete-a-source-server"></a>删除源服务器

> [!IMPORTANT]
> 删除源服务器会停止复制到所有副本服务器，并删除源服务器本身。 副本服务器成为现在支持读取和写入的独立服务器。

若要从 Azure 门户中删除源服务器，请使用以下步骤：

1. 在 Azure 门户中，选择源 Azure Database for MySQL 服务器。

2. 从“概览”中，选择“删除”。

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL - 复制" 以确认删除源服务器。  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL - 复制":::

## <a name="monitor-replication"></a>监视复制

1. 在 [Azure 门户](https://portal.azure.com/)中，选择要监视的副本 Azure Database for MySQL 服务器。

2. 在边栏的“监视”部分，选择“指标”：

3. 从可用指标的下拉列表中选择“复制延迟(秒)”。

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Azure Database for MySQL - 复制":::

4. 选择要查看的时间范围。 下图选择 30 分钟的时间范围。

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Azure Database for MySQL - 复制":::

5. 查看所选时间范围的复制延迟时间。 下图显示过去的 30 分钟。

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Azure Database for MySQL - 复制":::

## <a name="next-steps"></a>后续步骤

- 详细了解[只读副本](concepts-read-replicas.md)

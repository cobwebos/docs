---
title: 移动 Azure 区域 - Azure 门户 - Azure Database for MariaDB
description: 使用只读副本和 Azure 门户将 Azure Database for MariaDB 服务器从一个 Azure 区域移到另一个 Azure 区域。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: b2e4bc71a0883c6fef6f0115080a79a74ced92b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542421"
---
# <a name="move-an-azure-database-for-mariadb-server-to-another-region-by-using-the-azure-portal"></a>使用 Azure 门户将 Azure Database for MariaDB 服务器移到另一个区域

将现有 Azure Database for MariaDB 服务器从一个区域移到另一个区域的方案有多种。 例如，可以按照灾难恢复计划将生产服务器移到另一个区域。

可以使用 Azure Database for MariaDB [跨区域只读副本](concepts-read-replicas.md#cross-region-replication)来完成移到另一个区域的操作。 为此，请先在目标区域中创建一个只读副本。 接下来，停止到只读副本服务器的复制，使其成为同时接受读取和写入流量的独立服务器。 

> [!NOTE]
> 本文重点介绍如何将服务器移到不同的区域。 若要将服务器移到其他资源组或订阅，请参阅[移动](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)一文。 

## <a name="prerequisites"></a>先决条件

- 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MariaDB 服务器。 请确保源服务器位于其中一个定价层中。

- 请确保 Azure Database for MariaDB 源服务器位于要从中移动的 Azure 区域中。

## <a name="prepare-to-move"></a>准备移动

若要使用 Azure 门户在目标区域中创建跨区域只读副本服务器，请使用以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择要用作源服务器的现有 Azure Database for MariaDB 服务器。 此操作将打开“概述”页。
1. 从菜单中的“设置”下，选择“复制”。
1. 选择“添加副本”。
1. 输入副本服务器的名称。
1. 选择副本服务器的位置。 默认位置与源服务器的位置相同。 验证是否已选择要在其中部署副本的目标位置。
1. 选择“确定”以确认创建该副本。 在创建副本的过程中，数据将从源服务器复制到副本。 创建时间可能会持续几分钟或更长时间，这与源服务器的大小成正比。

>[!NOTE]
> 创建副本时，它不会继承源服务器的 VNet 服务终结点。 必须单独为副本设置这些规则。

## <a name="move"></a>移动

> [!IMPORTANT]
> 独立服务器不能再次成为副本。
> 在只读副本上停止复制之前，请确保副本包含所需的全部数据。

停止复制到副本服务器，使其成为独立服务器。 若要停止从 Azure 门户复制到副本的操作，请使用以下步骤：

1. 创建副本后，查找并选择你的 Azure Database for MariaDB 源服务器。 
1. 从菜单中的“设置”下，选择“复制”。
1. 选择副本服务器。
1. 选择“停止复制”。
1. 通过单击“确定”，确认要停止复制。

## <a name="clean-up-source-server"></a>清除源服务器

你可能想要删除 Azure Database for MariaDB 源服务器。 为此，请使用以下步骤：

1. 创建副本后，查找并选择你的 Azure Database for MariaDB 源服务器。
1. 在“概览”窗口中，选择“删除”。 
1. 键入源服务器的名称，确认你要删除它。
1. 选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Azure 门户将 Azure Database for MariaDB 服务器从一个区域移到了另一个区域，然后清理了不再需要的源资源。 

- 详细了解[只读副本](concepts-read-replicas.md)
- 详细了解如何[在 Azure 门户中管理只读副本](howto-read-replicas-portal.md)
- 详细了解[业务连续性](concepts-business-continuity.md)选项

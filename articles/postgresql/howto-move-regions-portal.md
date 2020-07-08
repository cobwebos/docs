---
title: 移动 Azure 区域-Azure 门户-Azure Database for PostgreSQL-单一服务器
description: 使用读取副本和 Azure 门户将 Azure Database for PostgreSQL 服务器从一个 Azure 区域移到另一个区域。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: 00cd291824eabfe8e1b43f35bc3618bfd793077d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567109"
---
# <a name="move-an-azure-database-for-azure-database-for-postgresql---single-server-to-another-region-by-using-the-azure-portal"></a>使用 Azure 门户将 Azure Database for PostgreSQL 单服务器的 Azure 数据库移到另一个区域

将现有 Azure Database for PostgreSQL 服务器从一个区域移到另一个区域的方案有多种。 例如，你可能想要将生产服务器作为灾难恢复计划的一部分转移到另一个区域。

你可以使用 Azure Database for PostgreSQL[跨区域读取副本](concepts-read-replicas.md#cross-region-replication)来完成移动到另一个区域。 为此，请先在目标区域中创建一个读取副本。 接下来，停止到读取副本服务器的复制，使其成为接受读和写流量的独立服务器。 

> [!NOTE]
> 本文重点介绍如何将服务器移到不同的区域。 如果要将服务器移到其他资源组或订阅，请参阅[移动](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)文章。 

## <a name="prerequisites"></a>先决条件

- 跨区域读取副本功能仅适用于常规用途或内存优化定价层中的 Azure Database for PostgreSQL 单服务器。 请确保源服务器在这些定价层中的一个。

- 请确保你的 Azure Database for PostgreSQL 源服务器位于要从中移动的 Azure 区域。

## <a name="prepare-to-move"></a>准备移动

若要使用 Azure 门户准备源服务器以进行复制，请执行以下步骤： 

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择要用作源服务器的现有 Azure Database for PostgreSQL 服务器。 此操作将打开“概述”页。
1. 从服务器的菜单中，选择 "**复制**"。 如果 Azure 复制支持至少设置为**副本**，则可以创建读取副本。 
1. 如果 Azure 复制支持至少设置为**副本**，请设置。 选择“保存”。
1. 通过选择 **"是"** 重新启动服务器以应用更改。
1. 操作完成后，会收到两个 Azure 门户通知。 一个通知是关于更新服务器参数的。 另一个通知是关于随后立即进行的服务器重启的。
1. 刷新 Azure 门户页，更新“复制”工具栏。 现在可以为此服务器创建只读副本。

若要使用 Azure 门户在目标区域中创建跨区域读取副本服务器，请使用以下步骤：

1. 选择要用作源服务器的现有 Azure Database for PostgreSQL 服务器。
1. 从菜单中的“设置”下，选择“复制”。
1. 选择“添加副本”。
1. 输入副本服务器的名称。
1. 选择副本服务器的位置。 默认位置与主服务器的位置相同。 验证是否已选择要在其中部署副本的目标位置。
1. 选择“确定”以确认创建该副本。 在副本创建过程中，数据从源服务器复制到副本。 与源服务器的大小成正比，创建时间可能持续几分钟或更多。

>[!NOTE]
> 创建副本时，它不会继承主服务器的防火墙规则和 VNet 服务终结点。 必须单独为副本设置这些规则。

## <a name="move"></a>移动

> [!IMPORTANT]
> 独立服务器不能再次成为副本。
> 在只读副本上停止复制之前，请确保副本包含所需的全部数据。

若要停止从 Azure 门户复制到副本，请使用以下步骤：

1. 创建副本后，查找并选择你的 Azure Database for PostgreSQL 源服务器。 
1. 从菜单中的“设置”下，选择“复制”。
1. 选择副本服务器。
1. 选择“停止复制”。
1. 通过单击“确定”，确认要停止复制。

## <a name="clean-up-source-server"></a>清除源服务器

你可能想要删除源 Azure Database for PostgreSQL 服务器。 为此，请按照以下步骤操作：

1. 创建副本后，查找并选择你的 Azure Database for PostgreSQL 源服务器。
1. 在 "**概述**" 窗口中，选择 "**删除**"。
1. 键入要确认删除的源服务器的名称。
1. 选择“删除”。

## <a name="next-steps"></a>后续步骤

在本教程中，你通过使用 Azure 门户将 Azure Database for PostgreSQL 服务器从一个区域移到另一个区域，然后清理了不需要的源资源。 

- 详细了解[只读副本](concepts-read-replicas.md)
- 详细了解如何[管理 Azure 门户中的读取副本](howto-read-replicas-portal.md)
- 详细了解[业务连续性](concepts-business-continuity.md)选项

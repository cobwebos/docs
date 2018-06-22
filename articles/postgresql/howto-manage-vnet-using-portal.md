---
title: 使用 Azure 门户创建和管理 Azure Database for PostgreSQL VNet 服务终结点和规则 | Microsoft Docs
description: 使用 Azure 门户创建和管理 Azure Database for PostgreSQL VNet 服务终结点和规则
services: postgresql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 8199bd98b9ae091ff2b27efa8334e8e763879359
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757359"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>使用 Azure 门户创建和管理 Azure Database for PostgreSQL VNet 服务终结点和 VNet 规则
虚拟网络 (VNet) 服务终结点和规则将虚拟网络的专用地址空间扩展到你的 Azure Database for PostgreSQL 服务器。 有关 Azure Database for PostgreSQL VNet 服务终结点（包括限制）的概述，请参阅 [Azure Database for PostgreSQL Server VNet 服务终结点](concepts-data-access-and-security-vnet.md)。 对所有支持 Azure Database for PostgreSQL 的区域提供 VNet 服务终结点的公共预览版。

> [!NOTE]
> 对 VNet 服务终结点的支持仅适用于常规用途和内存优化的服务器。

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>在 Azure 门户中创建 VNet 规则和启用服务终结点

1. 在 PostgreSQL 服务器页上的“设置”标题下，单击“连接安全性”，打开 Azure Database for PostgreSQL 的“连接安全性”窗格。 接下来，单击“+ 添加现有虚拟网络”。 如果没有现有 VNet，可以单击“+创建新的虚拟网络”以创建 VNet。 请参阅[快速入门：使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)

   ![Azure 门户 - 单击“连接安全性”](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. 输入 VNet 规则名称，选择订阅、虚拟网络和子网名称，然后单击“启用”。 这将在子网自动启用使用 Microsoft.SQL 服务标记的 VNet 服务终结点。

   ![Azure 门户 - 配置 VNet](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > 强烈建议在配置服务终结点前先阅读本文有关服务终结点配置和注意事项的内容。 虚拟网络服务终结点：[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 VNet 服务终结点使用 Microsoft.Sql 的服务类型名称，即名为“SQL 数据库”的 Azure 服务。 此服务标记也适用于 Azure SQL 数据库、Azure Database for PostgreSQL 和 MySQL 服务。 务必要注意的一点是，将 Microsoft.Sql 服务标记应用到 VNet 服务终结点时，它将为所有 Azure 数据库服务配置服务终结点流量（其中包括 Azure SQL 数据库、Azure Database for PostgreSQL 和子集上的 Azure Database for MySQL 服务器）。 
   > 

3. 启用后，单击“确认”即可看到 VNet 服务终结点与 VNet 规则一起启用。

   ![启用了 VNet 服务终结点并创建了 VNet 规则](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>后续步骤
- 同样，可以将脚本编写为[使用 Azure CLI 为 Azure Database for PostgreSQL 启用 VNet 服务终结点和创建 VNET 规则](howto-manage-vnet-using-cli.md)。
- 有关连接到 Azure Database for PostgreSQL 服务器的帮助，请参阅 [Azure Database for PostgreSQL 的连接库](./concepts-connection-libraries.md)

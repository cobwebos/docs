---
title: 请求增加配额
description: 本页介绍如何创建支持请求以增加 Azure SQL 数据库单一数据库、服务器和托管实例的配额。
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: fb576b81adeec99e4080c744749097390d1add1d
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111093"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Azure SQL 数据库的请求配额增加

本文介绍如何为单一数据库、服务器和托管实例请求 Azure SQL 数据库的配额增加。 它还说明了如何启用对区域的订阅访问。

## <a id="newquota"></a>创建新的支持请求

使用以下步骤从 SQL Database Azure 门户创建新的支持请求。

1. 在 " [Azure 门户](https://portal.azure.com)" 菜单上，选择 "**帮助 + 支持**"。

   !["帮助 + 支持" 链接](./media/quota-increase-request/help-plus-support.png)

1. 在 "**帮助 + 支持**" 中，选择 "**新建支持请求**"。

    ![创建新的支持请求](./media/quota-increase-request/new-support-request.png)

1. 对于“问题类型”，选择“服务和订阅限制(配额)”。

   ![选择问题类型](./media/quota-increase-request/select-quota-issue-type.png)

1. 对于 "**订阅**"，请选择要增加其配额的订阅。

   ![选择已增加配额的订阅](./media/quota-increase-request/select-subscription-support-request.png)

1. 对于 "**配额类型**"，请选择以下配额类型之一：

   - 适用于单一数据库和弹性池配额的**SQL 数据库**。
   - 托管实例的**SQL 数据库托管实例**。

   然后选择 "**下一步：解决方案" > >** "。

   ![选择配额类型](./media/quota-increase-request/select-quota-type.png)

1. 在**详细信息**窗口中，选择 "**提供详细**信息" 以输入其他信息。

   !["提供详细信息" 链接](./media/quota-increase-request/provide-details-link.png)

单击 "**提供详细信息**" 将显示允许您添加其他信息的 "**配额详细信息**" 窗口。 以下各节介绍了**SQL 数据库**和**SQL 数据库托管实例**配额类型的不同选项。

## <a id="sqldbquota"></a>SQL 数据库配额类型

以下各节介绍了**SQL 数据库**配额类型的三个配额增加选项：

- 每台服务器的数据库事务单位（Dtu）
- 每个订阅的服务器
- 启用对区域的订阅访问

### <a name="database-transaction-units-dtus-per-server"></a>每台服务器的数据库事务单位（Dtu）

使用以下步骤来请求增加每个服务器的 Dtu 数。

1. 选择**每个服务器配额类型的数据库事务单位（dtu）** 。

1. 在 "**资源**" 列表中，选择要作为目标的资源。

1. 在 "**新建配额**" 字段中，输入你请求的新 DTU 限制。

   ![DTU 配额详细信息](./media/quota-increase-request/quota-details-dtus.png)

有关详细信息，请参阅使用 dtu 购买模型的弹性池的[单数据库资源限制](sql-database-dtu-resource-limits-single-databases.md)和[弹性池的资源](sql-database-dtu-resource-limits-elastic-pools.md)限制。

### <a name="servers-per-subscription"></a>每个订阅的服务器

使用以下步骤来请求增加每个订阅的服务器数量。

1. 选择 "**每个订阅的服务器**" 配额类型。

1. 在 "**位置**" 列表中，选择要使用的 Azure 区域。 配额是每个区域中的每个订阅。

1. 在 "**新配额**" 字段中，输入你对该区域中服务器的最大数量的请求。

   ![服务器配额详细信息](./media/quota-increase-request/quota-details-servers.png)

有关详细信息，请参阅[SQL 数据库资源限制和资源调控](sql-database-resource-limits-database-server.md)。

### <a name="enable-subscription-access-to-a-region"></a>启用对区域的订阅访问

某些产品/服务类型在每个区域中都不可用。 你可能会看到如下所示的错误：

`This location is not available for subscription`

如果你的订阅需要特定区域的访问权限，请使用**其他配额请求**选项来请求访问权限。 在请求中，指定要为区域启用的产品/服务和 SKU 详细信息。 若要浏览产品/服务和 SKU 选项，请参阅[AZURE SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/single/)。

![其他配额详细信息](./media/quota-increase-request/quota-details-whitelisting.png)

## <a id="sqlmiquota"></a>托管实例配额类型

对于 " **SQL Server 托管实例**配额类型，请使用以下步骤：

1. 在 "**区域**" 列表中，选择要作为目标的 Azure 区域。

1. 输入要为**子网**和**vCore**请求的新限制。

   ![托管实例配额详细信息](./media/quota-increase-request/quota-details-managed-instance.png)

有关详细信息，请参阅[概述 AZURE SQL 数据库托管实例资源限制](sql-database-managed-instance-resource-limits.md)。

## <a name="submit-your-request"></a>提交请求

最后一步是填写 SQL 数据库配额请求的其他详细信息。 然后选择 "**下一步"：查看和创建 > >** ，查看请求详细信息后，单击 "**创建**" 以提交请求。

## <a name="next-steps"></a>后续步骤

提交请求后，将查看该请求。 系统会根据你在表单中提供的信息与你联系。

有关其他 Azure 限制的详细信息，请参阅[azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。

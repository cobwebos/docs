---
title: 请求增加配额
description: 本页介绍如何创建支持请求以增加 Azure SQL 数据库和 Azure SQL 托管实例的配额。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: cfcdd143a26d36ed3c4646122fce7c19c41976d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448837"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL 数据库和 SQL 托管实例的请求配额增加
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文介绍如何针对 Azure SQL 数据库和 Azure SQL 托管实例请求增加配额。 它还说明了如何启用对区域的订阅访问。

## <a name="create-a-new-support-request"></a><a id="newquota"></a> 创建新的支持请求

使用以下步骤从 SQL Database Azure 门户创建新的支持请求。

1. 在 [Azure 门户](https://portal.azure.com)菜单中，选择“帮助 + 支持”。

   ![“帮助 + 支持”链接](./media/quota-increase-request/help-plus-support.png)

1. 在“帮助 + 支持”中，选择“新建支持请求” 。

    ![创建新的支持请求](./media/quota-increase-request/new-support-request.png)

1. 对于 " **问题类型**"，请选择 " **服务和订阅限制")  (配额 **。

   ![选择问题类型](./media/quota-increase-request/select-quota-issue-type.png)

1. 对于 " **订阅**"，请选择要增加其配额的订阅。

   ![选择已增加配额的订阅](./media/quota-increase-request/select-subscription-support-request.png)

1. 对于 " **配额类型**"，请选择以下配额类型之一：

   - 适用于单一数据库和弹性池配额的**SQL 数据库**。
   - 托管实例的**SQL 数据库托管实例**。

   然后选择“下一步:**解决方案 >>** 。

   ![选择配额类型](./media/quota-increase-request/select-quota-type.png)

1. 在 **详细信息** 窗口中，选择 " **输入详细** 信息" 以输入其他信息。

   ![输入详细信息链接](./media/quota-increase-request/provide-details-link.png)

单击 " **输入详细信息** " 将显示允许您添加其他信息的 " **配额详细信息** " 窗口。 以下各节介绍了 **SQL 数据库** 和 **SQL 数据库托管实例** 配额类型的不同选项。

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> SQL 数据库配额类型

以下部分介绍了 **SQL 数据库** 配额类型的配额增加选项：

- 每台服务器的数据库事务单位 (Dtu) 
- 每个订阅的服务器数
- M 系列区域访问
- 区域访问

### <a name="database-transaction-units-dtus-per-server"></a>每台服务器的数据库事务单位 (Dtu) 

使用以下步骤来请求增加每个服务器的 Dtu 数。

1. 选择 **数据库事务单位 (dtu) 每个服务器** 配额类型。

1. 在“资源”列表中，选择要作为目标的资源。

1. 在 " **新建配额** " 字段中，输入你请求的新 DTU 限制。

   ![DTU 配额详细信息](./media/quota-increase-request/quota-details-dtus.png)

有关详细信息，请参阅使用 dtu 购买模型的弹性池的 [单数据库资源限制](resource-limits-dtu-single-databases.md) 和 [弹性池的资源](resource-limits-dtu-elastic-pools.md)限制。

### <a name="servers-per-subscription"></a>每个订阅的服务器数

使用以下步骤来请求增加每个订阅的服务器数。

1. 选择“每个订阅的服务器数”配额类型。

1. 在“位置”列表中，选择要使用的 Azure 区域。 配额按每个区域的每个订阅设置。

1. 在“新配额”字段中，输入对该区域中服务器的最大数量的请求。

   ![服务器配额详细信息](./media/quota-increase-request/quota-details-servers.png)

有关详细信息，请参阅 [SQL 数据库资源限制和资源调控](resource-limits-logical-server.md)。

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> 启用对区域的订阅访问

某些产品/服务类型在每个区域中都不可用。 你可能会看到如下所示的错误：

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

如果你的订阅需要特定区域的访问权限，请选择 " **区域访问** " 选项。 在请求中，指定要为区域启用的产品/服务和 SKU 详细信息。 若要浏览产品/服务和 SKU 选项，请参阅 [AZURE SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/single/)。

1. 选择 " **区域访问** 配额类型"。

1. 在 " **选择位置** " 列表中，选择要使用的 Azure 区域。 配额按每个区域的每个订阅设置。

1. 输入 **采购模型**和 **预期消耗** 详细信息。

   ![请求区域访问](./media/quota-increase-request/quota-request.png)

### <a name="enable-m-series-access-to-a-region"></a><a id="mseries"></a> 启用对区域的 M 系列访问

若要为订阅和区域启用 M 系列硬件，必须打开支持请求。

1. 选择 **M 系列区域访问** 配额类型。

1. 在 " **选择位置** " 列表中，选择要使用的 Azure 区域。 配额按每个区域的每个订阅设置。


   ![请求 M 系列区域访问](./media/quota-increase-request/quota-m-series.png)

## <a name="sql-managed-instance-quota-type"></a><a id="sqlmiquota"></a> SQL 托管实例配额类型

对于 " **SQL 托管实例** 配额类型，请使用以下步骤：

1. 在 " **区域** " 列表中，选择要作为目标的 Azure 区域。

1. 输入要为 **子网** 和 **vCore**请求的新限制。

   ![SQL 托管实例配额详细信息](./media/quota-increase-request/quota-details-managed-instance.png)

有关详细信息，请参阅 [概述 AZURE SQL 托管实例资源限制](../managed-instance/resource-limits.md)。

## <a name="submit-your-request"></a>提交请求

最后一步是填写 SQL 数据库配额请求的剩余详细信息。 然后选择“下一步:  查看 + 创建 >>”，在查看请求详细信息后，单击“创建”以提交请求  。

## <a name="next-steps"></a>后续步骤

提交请求后，可查看该请求。 系统会根据你在表单中提供的信息与你联系。

有关其他 Azure 限制的详细信息，请参阅 [azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

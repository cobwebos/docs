---
title: 请求增加配额
description: 本页介绍如何创建支持请求以增加 Azure SQL 数据库单个数据库、服务器和托管实例的配额。
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586149"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Azure SQL 数据库的请求配额增加

本文介绍如何请求为单个数据库、服务器和托管实例增加 Azure SQL 数据库的配额。 它还说明了如何启用对区域的订阅访问。

## <a name="create-a-new-support-request"></a><a id="newquota"></a>创建新的支持请求

使用以下步骤从 AZURE 门户为 SQL 数据库创建新的支持请求。

1. 在[Azure 门户](https://portal.azure.com)菜单上，选择 **"帮助 + 支持**"。

   !["帮助+支持"链接](./media/quota-increase-request/help-plus-support.png)

1. 在 **"帮助+支持"** 中，选择 **"新建支持请求**"。

    ![创建新的支持请求](./media/quota-increase-request/new-support-request.png)

1. 对于“问题类型”，选择“服务和订阅限制(配额)”********。

   ![选择问题类型](./media/quota-increase-request/select-quota-issue-type.png)

1. 对于**订阅**，选择要增加其配额的订阅。

   ![为增加的配额选择订阅](./media/quota-increase-request/select-subscription-support-request.png)

1. 对于**配额类型**，请选择以下配额类型之一：

   - **用于**单个数据库和弹性池配额的 SQL 数据库。
   - 托管实例的**SQL 数据库托管实例**。

   然后选择 **"下一步：解决方案 >>。 **

   ![选择配额类型](./media/quota-increase-request/select-quota-type.png)

1. 在 **"详细信息"** 窗口中，选择 **"提供详细信息**"以输入其他信息。

   !["提供详细信息"链接](./media/quota-increase-request/provide-details-link.png)

单击 **"提供详细信息**"将显示允许您添加其他信息的**配额详细信息**窗口。 以下各节介绍**SQL 数据库**和**SQL 数据库托管实例**配额类型的不同选项。

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>SQL 数据库配额类型

以下各节介绍**SQL 数据库**配额类型的三个增加配额选项：

- 每个服务器的数据库事务单元 （DUS）
- 每个订阅的服务器
- 启用对区域的订阅访问

### <a name="database-transaction-units-dtus-per-server"></a>每个服务器的数据库事务单元 （DUS）

使用以下步骤请求增加每个服务器的 DTO。

1. 根据服务器配额类型选择**数据库事务单位 （DTUs）。**

1. 在 **"资源"** 列表中，选择要定位的资源。

1. 在 **"新建配额"** 字段中，输入要请求的新 DTU 限制。

   ![DTU 配额详细信息](./media/quota-increase-request/quota-details-dtus.png)

有关详细信息，请参阅使用[DTU 采购模型的单个数据库的资源限制](sql-database-dtu-resource-limits-single-databases.md)以及[使用 DTU 采购模型的弹性池的资源限制](sql-database-dtu-resource-limits-elastic-pools.md)。

### <a name="servers-per-subscription"></a>每个订阅的服务器

使用以下步骤请求增加每个订阅的服务器数。

1. **选择每个订阅**配额类型的服务器。

1. 在 **"位置**"列表中，选择要使用的 Azure 区域。 配额是每个区域中每个订阅的配额。

1. 在"**新建配额"** 字段中，输入对该区域中服务器最大数量的请求。

   ![服务器配额详细信息](./media/quota-increase-request/quota-details-servers.png)

有关详细信息，请参阅[SQL 数据库资源限制和资源治理](sql-database-resource-limits-database-server.md)。

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>启用对区域的订阅访问

某些产品/服务类型并非在每个区域都可用。 您可能会看到如下错误：

`This location is not available for subscription`

如果您的订阅需要在特定区域进行访问，请使用 **"其他配额请求**"选项请求访问。 在请求中，指定要为该区域启用的产品/服务以及 SKU 详细信息。 要浏览产品/服务选项和 SKU 选项，请参阅[Azure SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/single/)。

![其他配额详细信息](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>托管实例配额类型

对于**SQL 服务器托管实例**配额类型，请使用以下步骤：

1. 在 **"区域**"列表中，选择要定位的 Azure 区域。

1. 输入您请求的**子网**和**vCore**的新限制。

   ![托管实例配额详细信息](./media/quota-increase-request/quota-details-managed-instance.png)

有关详细信息，请参阅概述[Azure SQL 数据库托管实例资源限制](sql-database-managed-instance-resource-limits.md)。

## <a name="submit-your-request"></a>提交请求

最后一步是填写 SQL 数据库配额请求的剩余详细信息。 然后选择 **"下一步：审阅 + 创建>>**"，在查看请求详细信息后，单击"**创建"** 以提交请求。

## <a name="next-steps"></a>后续步骤

提交请求后，将对其进行审核。 我们将根据您在表单中提供的信息与您联系并回答。

有关其他 Azure 限制的详细信息，请参阅[Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。

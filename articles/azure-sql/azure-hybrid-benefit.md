---
title: Azure 混合权益
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 使用现有 SQL Server 许可证，享受 Azure SQL 数据库和 SQL 托管实例折扣。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 11/13/2019
ms.openlocfilehash: f43d03f722617e88e7a00e71798351c88ce10e98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327589"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure 混合权益 - Azure SQL 数据库和 SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

在基于 vCore 的购买模型的预配计算机层级中，可以使用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)交换现有许可证，以获得 Azure SQL 数据库和 Azure SQL 托管实例的折扣费率。 借助这项 Azure 权益，可以通过使用具有软件保障的 SQL Server 许可证在 SQL 数据库和 SQL 托管实例上节省多达 30% 甚至更高的成本。 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)页提供可帮助确定所节省费用的计算器。  请注意，Azure 混合权益不适用于无服务器的 Azure SQL 数据库。

> [!NOTE]
> 更改为 Azure 混合权益不需要停机。

![vcore 定价结构](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>选择许可证模型

使用 Azure 混合权益，可以选择通过使用适用于 SQL Server 数据库引擎自身的现有 SQL Server 许可证仅为底层 Azure 基础结构付费（基本计算定价），或者可以选择同时为底层基础结构和 SQL Server 许可证付费（许可证涵盖的定价）。

可以在 Azure 门户中选择或更改许可证模型： 
- 对于新数据库，在创建过程中，在“基础”选项卡上选择“配置数据库”，并选择有助于节省资金的选项 。
- 对于现有数据库，在“设置”菜单中选择“配置”，然后选择有助于节省资金的选项 。

还可以使用以下 API 之一配置新的或现有的数据库：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 设置或更新许可证类型：

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 设置或更新许可证类型：

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

使用 REST API 设置或更新许可证类型：

- [数据库 - 创建或更新](/rest/api/sql/databases/createorupdate)
- [数据库 - 更新](/rest/api/sql/databases/update)
- [托管实例 - 创建或更新](/rest/api/sql/managedinstances/createorupdate)
- [托管实例 - 更新](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Azure 混合权益问题

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>面向 SQL Server 的 Azure 混合权益是否具有双倍使用权利？

我们为客户提供 180 天的许可证双倍使用权利，以确保无缝运行迁移。 在 180 天期限过后，只能在云中的 SQL 数据库内使用 SQL Server 许可证。 在本地和云中不再有双重使用权利。

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>面向 SQL Server 的 Azure 混合权益与许可证移动性有何区别？

我们为持有软件保障的 SQL Server 客户提供许可证移动权益， 以便将其许可证重新分配到合作伙伴的共享服务器。 可以在 Azure IaaS 和 AWS EC2 中使用此权益。

与许可证移动性相比，面向 SQL Server 的 Azure 混合权益的区别主要体现在两个方面：

- 提供经济权益，以便将高度虚拟化的工作负荷转移到 Azure。 对于高度虚拟化的应用程序，如果 SQL Server Enterprise Edition 客户在本地拥有一个核心，则他们可以在 Azure 的常规用途 SKU 中获得四个核心。 许可证移动性不允许使用任何特殊成本权益将虚拟化工作负荷转移到云中。
- 它适用于 Azure（SQL 托管实例）上与 SQL Server 高度兼容的 PaaS 目标。

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>面向 SQL Server 的 Azure 混合权益的特殊权利有哪些？

SQL 数据库客户将获得与面向 SQL Server 的 Azure 混合权益相关的以下权利：

|许可证足迹|面向 SQL Server 的 Azure 混合权益可带来哪些好处？|
|---|---|
|具有 SA 的 SQL Server Enterprise Edition 核心客户|<li>可以根据“常规用途”或“业务关键”SKU 支付基准费率</li><br><li>1 个本地核心 =“常规用途”SKU 中的 4 个核心</li><br><li>1 个本地核心 =“业务关键”SKU 中的 1 个核心</li>|
|具有 SA 的 SQL Server Standard Edition 核心客户|<li>只能根据“常规用途”SKU 支付基准费率</li><br><li>1 个本地核心 =“常规用途”SKU 中的 1 个核心</li>|
|||


## <a name="next-steps"></a>后续步骤

- 若要深入了解如何在 Azure SQL 部署选项之间进行选择，请参阅[在 Azure SQL 中选择适当的部署选项](azure-sql-iaas-vs-paas-what-is-overview.md)。
- 有关 SQL 数据库和 SQL 托管实例功能的比较，请参阅 [SQL 数据库和 SQL 托管实例功能](database/features-comparison.md)。

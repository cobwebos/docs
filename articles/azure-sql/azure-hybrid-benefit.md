---
title: Azure 混合权益
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 使用 Azure SQL 数据库和 SQL 托管实例折扣的现有 SQL Server 许可证。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: b4af9fce3c5ca779866055d2835ebfebe9fbb9cf
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987002"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure 混合权益-Azure SQL 数据库 & SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

在基于 vCore 的购买模型的预配计算层中，你可以通过使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)，为 Azure sql 数据库和 azure sql 托管实例上的折扣率交换现有许可证。 此 Azure 权益使你可以通过软件保障使用 SQL Server 许可证，将 SQL 数据库 & SQL 托管实例节省多达30% 甚至更高。 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)页有一个计算器来帮助确定节省量。  请注意，Azure 混合权益不适用于 Azure SQL 数据库无服务器。

> [!NOTE]
> 更改为 Azure 混合权益不需要停机。

![定价](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>选择许可证模型

使用 Azure 混合权益，你可以选择只为底层 Azure 基础结构支付 SQL Server 数据库引擎本身的 SQL Server 许可证（基础计算定价），也可以为底层基础结构和 SQL Server 许可证（许可证包括的价格）付费。

可以在 Azure 门户中选择或更改许可模式： 
- 对于新数据库，在创建过程中，在 "**基本**信息" 选项卡上选择 "**配置数据库**"，然后选择相应的选项以节省资金。
- 对于现有数据库，请在 "**设置**" 菜单中选择 "**配置**"，然后选择用于节省资金的选项。

你还可以使用以下 Api 之一配置新的或现有的数据库：

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

- [数据库-创建或更新](/rest/api/sql/databases/createorupdate)
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
- 它在 Azure （SQL 托管实例）上提供与 SQL Server 高度兼容的 PaaS 目标。

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>面向 SQL Server 的 Azure 混合权益的特殊权利有哪些？

SQL 数据库客户将获得与面向 SQL Server 的 Azure 混合权益相关的以下权利：

|许可证足迹|面向 SQL Server 的 Azure 混合权益可带来哪些好处？|
|---|---|
|具有 SA 的 SQL Server Enterprise Edition 核心客户|<li>可以根据“常规用途”或“业务关键”SKU 支付基准费率</li><br><li>1 个本地核心 =“常规用途”SKU 中的 4 个核心</li><br><li>1 个本地核心 =“业务关键”SKU 中的 1 个核心</li>|
|具有 SA 的 SQL Server Standard Edition 核心客户|<li>只能根据“常规用途”SKU 支付基准费率</li><br><li>1 个本地核心 =“常规用途”SKU 中的 1 个核心</li>|
|||


## <a name="next-steps"></a>后续步骤

- 有关选择 Azure SQL 部署选项的帮助，请参阅[在 AZURE sql 中选择正确的部署选项](azure-sql-iaas-vs-paas-what-is-overview.md)。
- 有关 SQL 数据库和 SQL 托管实例功能的比较，请参阅[Sql 数据库 & sql 托管实例功能](database/features-comparison.md)。

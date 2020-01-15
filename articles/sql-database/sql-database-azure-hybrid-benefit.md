---
title: Azure 混合权益
description: 将现有 SQL Server 许可证用于 SQL 数据库折扣。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945618"
---
# <a name="azure-hybrid-benefit"></a>Azure 混合权益

在基于 vCore 的购买模型的预配计算层中，你可以使用[Azure 混合权益 SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/)来交换现有许可证，以获得 SQL 数据库的折扣率。 此 Azure 权益使你可以通过使用本地 SQL Server 具有软件保障的许可证，在 Azure SQL 数据库上节省高达30% 甚至更高。 请使用之前提到的链接来使用 Azure 混合权益计算器以获取正确的值。 

> [!NOTE]
> 更改为 Azure 混合权益不需要停机。

![定价](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>选择许可证模型

使用 Azure 混合权益，你可以选择仅为基础 Azure 基础结构付费（通过使用 SQL 数据库引擎本身的 SQL Server 许可证（基础计算定价），也可以为底层基础结构和 SQL Server 付费许可证（许可证包括的定价）。

您可以通过使用 Azure 门户或使用以下 Api 之一选择或更改许可模式：

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 设置或更新许可证类型：

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 设置或更新许可证类型：

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-apitabrest"></a>[REST API](#tab/rest)

使用 REST API 设置或更新许可证类型：

- [数据库 - 创建或更新](/rest/api/sql/databases/createorupdate)
- [数据库 - 更新](/rest/api/sql/databases/update)
- [托管实例 - 创建或更新](/rest/api/sql/managedinstances/createorupdate)
- [托管实例 - 更新](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>后续步骤

- 若要在 SQL 数据库部署选项之间进行选择，请参阅[在 AZURE sql 中选择正确的部署选项](sql-database-paas-vs-sql-server-iaas.md)。
- 有关 SQL 数据库功能的比较，请参阅[AZURE SQL 数据库功能](sql-database-features.md)。

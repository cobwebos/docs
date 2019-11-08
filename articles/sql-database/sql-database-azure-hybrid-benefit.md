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
ms.date: 10/08/2019
ms.openlocfilehash: 17252f6544ee56647315dc44ace4db3e3abe9726
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821876"
---
# <a name="azure-hybrid-benefit"></a>Azure 混合权益

在基于 vCore 的购买模型的预配计算机层级中，可以使用[适用于 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)交换现有许可证，以获得 SQL 数据库的折扣价格。 借助这项 Azure 权益，可以使用附带软件保障的本地 SQL Server 许可证，将 Azure SQL 数据库的成本最多节省 30%。

![定价](./media/sql-database-service-tiers/pricing.png)


## <a name="choose-a-license-model"></a>选择许可证模型

使用 Azure 混合权益，可以选择仅为底层 Azure 基础结构付费且将现有的 SQL Server 许可证用于 SQL 数据库引擎自身（基本计算定价），或者可以选择同时为底层基础结构和 SQL Server 许可证付费（许可证涵盖的定价）。

可以使用 Azure 门户或下列 API 之一来选择或更改许可模型：

- 使用 PowerShell 设置或更新许可证类型：

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- 使用 Azure CLI 设置或更新许可证类型：

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- 使用 REST API 设置或更新许可证类型：

  - [Databases - Create Or Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [数据库 - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [托管实例 - 创建或更新](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [托管实例 - 更新](https://docs.microsoft.com/rest/api/sql/managedinstances/update)



## <a name="next-steps"></a>后续步骤

- 若要在 SQL 数据库部署选项之间进行选择，请参阅[在 AZURE sql 中选择正确的部署选项](sql-database-paas-vs-sql-server-iaas.md)。
- 有关 SQL 数据库功能的比较，请参阅[AZURE SQL 数据库功能](sql-database-features.md)。

---
title: 启用自动优化
description: 可以使用 Azure 门户轻松地在数据库中启用自动优化。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 12/03/2019
ms.openlocfilehash: 35fc4b18ee5a98270f715a969354df2be5000150
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444090"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>在 Azure 门户中启用自动优化以监视查询并提高工作负载性能
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Azure SQL 数据库是自动托管的数据服务，可持续监视查询并识别可为改善工作负载性能执行的操作。 可以查看建议并手动应用这些建议，或者让 Azure SQL 数据库自动应用纠正措施 - 这称为**自动优化模式**。

可通过以下方法在服务器或数据库级别启用自动优化：

- [Azure 门户](automatic-tuning-enable.md#azure-portal)
- [REST API](automatic-tuning-enable.md#rest-api) 调用
- [T-SQL](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) 命令

> [!NOTE]
> 对于 Azure SQL 托管实例，支持的选项 FORCE_LAST_GOOD_PLAN 只能通过 [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) 进行配置。 本文中所述的基于 Azure 门户的配置和自动索引优化选项不适用于 Azure SQL 托管实例。

> [!NOTE]
> 目前不支持通过 ARM（Azure 资源管理器）模板配置自动优化选项。

## <a name="enable-automatic-tuning-on-server"></a>在服务器上启用自动优化

在服务器级别上，可选择从“Azure 默认值”继承自动优化配置，或选择不继承配置。 Azure 默认设置为启用 FORCE_LAST_GOOD_PLAN，禁用 CREATE_INDEX 并禁用 DROP_INDEX。

> [!IMPORTANT]
> 从 2020 年 3 月开始，用于自动优化的新 Azure 默认设置如下所示：
>
> - 启用 FORCE_LAST_GOOD_PLAN，禁用 CREATE_INDEX，禁用 DROP_INDEX。
> - 未配置自动优化首选项的现有服务器将自动配置为继承 Azure 默认设置。 这适用于当前有用于自动优化的服务器设置处于未定义状态的所有客户。
> - 创建的新服务器将自动配置为继承 Azure 默认设置（与之前创建新服务器时自动优化配置处于未定义状态时不同）。

### <a name="azure-portal"></a>Azure 门户

若要在 Azure SQL 数据库中的[服务器](logical-servers.md)上启用自动优化，请在 Azure 门户中导航到该服务器，然后在菜单中选择“自动优化”。

![屏幕截图显示 Azure 门户中的自动优化，你可以在其中对服务器应用选项。](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> 请注意，此时 DROP_INDEX 选项与使用分区切换和索引提示的应用程序不兼容，并且不应在这些情况下打开。 高级和业务关键服务层不支持删除未使用的索引。

选择想要启用的自动优化选项，然后选择“应用”。

服务器上的自动优化选项将应用到此服务器上的所有数据库。 默认情况下，所有数据库将从其父服务器继承配置，但可替代此配置并为每个数据库单独指定配置。

### <a name="rest-api"></a>REST API

了解有关使用 REST API 在服务器上启用自动优化的详细信息，请参阅[服务器自动优化 UPDATE 和 GET HTTP 方法](/rest/api/sql/serverautomatictuning)。

## <a name="enable-automatic-tuning-on-an-individual-database"></a>对单个数据库启用自动优化

Azure SQL 数据库支持为每个数据库单独指定自动优化配置。 在数据库级别中，可选择从“Azure 默认值”继承自动优化配置，或选择不继承配置。 Azure 默认设置为启用 FORCE_LAST_GOOD_PLAN，禁用 CREATE_INDEX 并禁用 DROP_INDEX。

> [!TIP]
> 常规建议是在服务器级别管理自动优化配置，以便为每个数据库自动应用相同的配置设置。 仅在需要该数据库与其他从相同服务器继承设置的数据库有不同设置时，在单个数据库上配置自动优化。

### <a name="azure-portal"></a>Azure 门户

若要对单个数据库启用自动优化，请在 Azure 门户中导航到该数据库，然后选择“自动优化” 。

可以为每个数据库单独配置各自的自动优化设置。 可以手动配置单个自动优化选项，或指定选项从服务器继承其设置。

![屏幕截图显示 Azure 门户中的自动优化，你可以在其中对单个数据库应用选项。](./media/automatic-tuning-enable/database.png)

请注意，此时 DROP_INDEX 选项与使用分区切换和索引提示的应用程序不兼容，并且不应在这些情况下启用。

选择所需配置后，单击“应用”。

### <a name="rest-api"></a>REST API

了解有关使用 REST API 在单个数据库上启用自动优化的详细信息，请参阅 [Azure SQL 数据库自动优化 UPDATE 和 GET HTTP 方法](/rest/api/sql/databaseautomatictuning)。

### <a name="t-sql"></a>T-SQL

要通过 T-SQL 在单个数据库上启用自动优化，请连接至数据库，并执行以下查询：

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

将自动优化设置为 AUTO 时，会应用 Azure 默认值。 将其设置为 INHERIT 时，会从父服务器继承自动优化配置。 选择 CUSTOM 时，需手动配置自动优化。

要通过 T-SQL 配置单个自动优化选项，请连接至数据库并执行如下所示的查询：

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

将单个自动优化选项设置为 ON 时，数据库所继承的任何设置都将被替代，并会启用优化选项。 将其设置为 OFF 时，数据库所继承的任何设置亦将被替代，并会禁用优化选项。 自动优化选项（指定为 DEFAULT）将从服务器级别设置中继承自动优化配置。  

> [!IMPORTANT]
> 对于[活动异地复制](auto-failover-group-overview.md)，只需在主数据库上配置自动优化。 自动应用的优化操作（例如索引创建或删除）将自动复制到只读辅助数据库。 尝试在只读辅助数据库上通过 T-SQL 启用自动优化将导致失败，因为不支持在只读辅助数据库上使用不同的优化配置。
>

了解有关用于配置自动优化的 T-SQL 选项的详细信息，请参阅 [ALTER DATABASE SET 选项 (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true)。

## <a name="disabled-by-the-system"></a>已被系统禁用

自动优化监视着自身在数据库上进行的一切操作，在某些情况下，它可以判断自身在数据库中无法正常运行。 在此情况下，系统将禁用自动优化。 造成此情况的主要原因是未启用查询数据存储，或在指定数据库中查询数据存储处于只读状态。

## <a name="permissions"></a>权限

由于自动调整是 Azure 功能，因此若要使用它，需使用 Azure 的内置角色。 若要使用 Azure 门户中的功能，仅使用 SQL 身份验证还不够。

若要使用自动优化，必须为用户授予权限，最低必需权限是 Azure 的内置 [SQL 数据库参与者](../../role-based-access-control/built-in-roles.md#sql-db-contributor)角色。 也可考虑使用特权较高的角色，例如 SQL Server 参与者、SQL 托管实例参与者、参与者和所有者。

## <a name="configure-automatic-tuning-e-mail-notifications"></a>配置自动优化电子邮件通知

请参阅 [自动优化电子邮件通知](automatic-tuning-email-notifications-configure.md) 指南。

## <a name="next-steps"></a>后续步骤

- 请参阅[自动优化文章](automatic-tuning-overview.md)，详细了解自动优化以及如何借助它来提高性能。
- 请参阅[性能建议](database-advisor-implement-performance-recommendations.md)，获取 Azure SQL 数据库性能建议的概述。
- 请参阅[查询性能见解](query-performance-insight-use.md)，了解排名靠前的查询的性能影响。

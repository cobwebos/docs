---
title: 为 Azure SQL 数据库启动自动优化 | Microsoft Docs
description: 可轻松对 Azure SQL 数据库启用自动优化。
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: vvasic
ms.openlocfilehash: d4d3b7f54c7393b57339ea149e8a79f97891dc20
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646025"
---
# <a name="enable-automatic-tuning"></a>启用自动优化

Azure SQL 数据库是自动托管的数据服务，可持续监视查询并识别可为改善工作负荷性能执行的操作。 可查看建议并手动应用，或让 Azure SQL 数据库自动应用正确的操作 - 这称为“自动优化模式”。 可在服务器或数据库级别启用自动优化。

## <a name="enable-automatic-tuning-on-server"></a>对服务器启用自动优化
在服务器级别上，可选择从“Azure 默认值”继承自动优化配置，或选择不继承配置。 Azure 默认值为启用 FORCE_LAST_GOOD_PLAN 和 CREATE_INDEX，禁用 DROP_INDEX。

### <a name="azure-portal"></a>Azure 门户
若要对 Azure SQL 数据库逻辑服务器启用自动优化，请在 Azure 门户中导航到该服务器，然后选择菜单中的“自动优化”。

![服务器](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> 请注意，此时 DROP_INDEX 选项与使用分区切换和索引提示的应用程序不兼容，并且不应在这些情况下启用。
>

选择想要启用的自动优化选项，然后选择“应用”。

服务器上的自动优化选项将应用到此服务器上的所有数据库。 默认情况下，所有数据库将从其父服务器继承配置，但可替代此配置并为每个数据库单独指定配置。

### <a name="rest-api"></a>REST API
[单击此处，详细了解如何通过 REST API 在服务器级别启用自动优化](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>对单个数据库启用自动优化

Azure SQL 数据库支持为每个数据库单独指定自动优化配置。 在数据库级别中，可选择从“Azure 默认值”继承自动优化配置，或选择不继承配置。 Azure 默认值设为启用 FORCE_LAST_GOOD_PLAN 和 CREATE_INDEX，禁用 DROP_INDEX。

> [!NOTE]
> 常规建议是在服务器级别管理自动优化配置，以便为每个数据库自动应用相同的配置设置。 仅在需要该数据库与其他从相同服务器继承设置的数据库有不同设置时，在单个数据库上配置自动优化。
>

### <a name="azure-portal"></a>Azure 门户

若要对单个数据库启用自动优化，请在 Azure 门户中导航到该数据库，然后选择“自动优化”。

可以为每个数据库单独配置各自的自动优化设置。 可以手动配置单个自动优化选项，或指定选项从服务器继承其设置。

![数据库](./media/sql-database-automatic-tuning-enable/database.png)

请注意，此时 DROP_INDEX 选项与使用分区切换和索引提示的应用程序不兼容，并且不应在这些情况下启用。

选择所需配置后，单击“应用”。

### <a name="rest-api"></a>REST API
[单击此处，详细了解如何通过 REST API 在单个数据库上启用自动优化](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

要通过 T-SQL 在单个数据库上启用自动优化，请连接至数据库，并执行以下查询：

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
将自动优化设置为 AUTO 时，会应用 Azure 默认值。 将其设置为 INHERIT 时，会从父服务器继承自动优化配置。 选择 CUSTOM 时，需手动配置自动优化。

要通过 T-SQL 配置单个自动优化选项，请连接至数据库并执行如下所示的查询：

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
将单个自动优化选项设置为 ON 时，数据库所继承的任何设置都将被替代，并会启用优化选项。 将其设置为 OFF 时，数据库所继承的任何设置亦将被替代，并会禁用优化选项。 自动优化选项（指定为 DEFAULT）将从数据库级别自动优化设置中继承配置。  

## <a name="disabled-by-the-system"></a>已被系统禁用
自动优化监视着自身在数据库上进行的一切操作，在某些情况下，它可以判断自身在数据库中无法正常运行。 在此情况下，系统将禁用自动优化。 造成此情况的主要原因是未启用查询数据存储，或在指定数据库中查询数据存储处于只读状态。

## <a name="configure-automatic-tuning-e-mail-notifications"></a>配置自动优化电子邮件通知

请参阅[自动优化电子邮件通知](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>后续步骤
* 请阅读[自动优化文章](sql-database-automatic-tuning.md)，详细了解自动优化及其如何帮助提高性能。
* 请参阅[性能建议](sql-database-advisor.md)，了解 Azure SQL 数据库性能建议的概述。
* 若要了解排名靠前的查询的性能影响，请参阅[查询性能见解](sql-database-query-performance.md)。

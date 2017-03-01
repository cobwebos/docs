---
title: "Azure SQL 数据库 - 审核 | Microsoft Docs"
description: "Azure SQL 数据库审核跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 8c9da10e6ebc67c464bf7b5e569cb2113a082d0c
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-auditing-concepts"></a>SQL 数据库审核概念
Azure SQL 数据库审核跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。

审核可帮助你一直保持遵从法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。

审核有助于遵从法规标准，但不能保证遵从法规。 有关可帮助你遵从标准的 Azure 计划的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。

* [Azure SQL 数据库审核概述]
* [为数据库设置审核]
* [分析审核日志和报告]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Azure SQL 数据库审核概述
SQL 数据库审核可让你：

* **保留**选定事件的审核痕迹。 可以定义要审核的数据库操作的类别。
* **报告**数据库活动。 可以使用预配置的报告和仪表板快速开始使用活动和事件报告。
* **分析**报告。 可以查找可疑事件、异常活动和趋势。

有两种**审核方法**：

* **Blob 审核** - 将日志写入 Azure Blob 存储。 这是一种较新的审核方法，可提供更高性能，支持更高粒度的对象级审核，且更具成本效益。
* **表审核** - 将日志写入 Azure 表存储。

> [!IMPORTANT]
> 引入新的 blob 审核为数据库继承服务器审核策略的方式带来了重大改变。 

可为不同类型的事件类别配置审核。

* 若要使用 Azure 门户配置和管理审核，请参阅[使用 Azure 门户审核](sql-database-auditing-portal.md)。
* 若要使用 PowerShell 配置和管理审核，请参阅[使用 PowerShell 审核](sql-database-auditing-powershell.md)。
* 若要使用 REST API 配置和管理审核，请参阅[使用 REST API 审核](sql-database-auditing-rest.md)。

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

可以为特定数据库定义审核策略，也可以将审核策略定义为默认服务器策略。 默认的服务器审核策略将应用到服务器上的所有现有数据库和新建的数据库。

## <a name="blob-auditing"></a>Blob 审核

如果启用了服务器 blob 审核，它将始终应用于数据库（对服务器上的所有数据库进行审核），而不考虑：
    - 数据库审核设置。
    - 是否在数据库边栏选项卡中选中了“从服务器继承设置”复选框。

> [!IMPORTANT]
> 除了在服务器上启用 blob 审核外，对数据库启用 blob 审核**不**会覆盖或更改服务器 blob 审核的任何设置 - 这两种审核将并存。 换言之，将并行对数据库执行两次审核（一次按服务器策略审核，一次按数据库策略审核）。

应避免同时启用服务器 blob 审核和数据库 blob 审核，除非：
 * 需要对特定数据库使用不同*存储帐户*或*保留期*。
 * 希望为特定数据库审核事件类型或类别，而这些事件类型或类别不同于要为此服务器上其余数据库审核的事件类型或类别（例如，只有特定数据库需要审核表插入）。

否则，建议仅启用服务器级 blob 审核，并保持禁用所有数据库的数据库级审核。

## <a name="table-auditing"></a>表审核

如果启用了服务器级表审核，则仅当在数据库边栏选项卡中选中了“从服务器继承设置”复选框（将默认对所有现有数据库和新创建的数据库选中此框）时，它才应用于数据库。

- 如果*选中了*此复选框，则对数据库中的审核设置所做的任何更改将**覆盖**此数据库的服务器设置。

- 如果*未选中*此复选框且数据库级审核处于禁用状态，则不会审核数据库。

## <a name="analyze-audit-logs-and-reports"></a>分析审核日志和报告
审核日志将在安装期间选择的 Azure 存储帐户中进行聚合。

可以使用 [Azure 存储资源管理器](http://storageexplorer.com/)等工具浏览审核日志。

## <a name="next-steps"></a>后续步骤

* 若要使用 Azure 门户配置和管理审核，请参阅[在 Azure 门户中配置审核](sql-database-auditing-portal.md)。
* 若要使用 PowerShell 配置和管理审核，请参阅[在 PowerShell 中配置审核](sql-database-auditing-powershell.md)。
* 若要使用 REST API 配置和管理审核，请参阅[使用 REST API 配置审核](sql-database-auditing-rest.md)。


<!--Anchors-->
[Azure SQL 数据库审核概述]: #subheading-1
[为数据库设置审核]: #subheading-2
[分析审核日志和报告]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png


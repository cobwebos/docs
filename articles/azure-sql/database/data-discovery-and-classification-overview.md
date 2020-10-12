---
title: 数据发现和分类
description: Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 的数据发现和分类
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 09/21/2020
tags: azure-synapse
ms.openlocfilehash: 6f324b1b0b5ed1882050684e7ac1c8ec4ea573dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886497"
---
# <a name="data-discovery--classification"></a>数据发现和分类
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

数据发现和分类内置于 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 中。 它提供用于发现、分类、标记和报告数据库中的敏感数据的高级功能。

最敏感的数据可能包括业务、财务、医疗保健或个人信息。 发现和分类这些数据在组织的信息保护方法中发挥举足轻重的作用。 它可以充当基础结构，用于：

- 帮助满足数据隐私标准和法规符合性要求。
- 各种安全方案，如监视（审核）并在敏感数据存在异常访问时发出警报。
- 控制对包含高度敏感数据的数据库的访问并增强其安全性。

> [!NOTE]
> 要了解本地 SQL Server，请参阅 [SQL 数据发现和分类](https://go.microsoft.com/fwlink/?linkid=866999)。

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>什么是数据发现和分类？

数据发现和分类在 Azure 中引入了一组高级服务和新功能。 它构成适用于 SQL 数据库、SQL 托管实例和 Azure Synapse 的信息保护范例，旨在保护数据，而不仅仅是数据库。 范例包括：

- **发现和建议：** 分类引擎扫描数据库，并识别包含潜在敏感数据的列。 使用此功能可以通过 Azure 门户轻松地查看和应用建议的分类。

- **标记：** 可通过使用已添加到 SQL Server 数据库引擎的新元数据属性，将敏感度分类标签永久应用于列。 然后，此元数据可用于基于敏感度的高级审核和保护方案。

- **查询结果集敏感度：** 出于审核目的实时计算查询结果集的敏感度。

- **可见性：** 可以在 Azure 门户的详细仪表板中查看数据库分类状态。 此外，还可下载用于符合性和审核目的以及其他需求的报表（Excel 格式）。

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>发现、分类和标记敏感列

本部分介绍用于以下方案的步骤：

- 发现、分类和标记数据库中包含敏感数据的列。
- 查看数据库的当前分类状态并导出报表。

分类包含两种元数据属性：

- **标签**：主要分类属性，用于定义列中存储的数据的敏感度级别。  
- **信息类型**：提供有关列中存储的数据类型的更详尽信息的属性。

### <a name="define-and-customize-your-classification-taxonomy"></a>定义和自定义分类

数据发现和分类附带了一组内置的敏感度标签和一组内置的信息类型和发现逻辑。 现在，可以自定义此分类并专门针对你的环境定义分类构造的集合和级别。

可在一个中心位置针对整个 Azure 组织定义和自定义分类。 该位置在 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)内，是安全策略的一部分。 仅对组织根管理组具有管理权限的人员可以执行此任务。

在信息保护的策略管理过程中，可定义自定义标签、对其进行分级，并将其与选定的一组信息类型相关联。 还可以添加自己的自定义信息类型，并使用字符串模式对其进行配置。 这些模式已添加到用于识别数据库中的此类型数据的发现逻辑。

有关详细信息，请参阅 [在 Azure 安全中心中自定义 SQL 信息保护策略 (预览) ](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)。

定义组织范围的策略后，可以继续使用自定义策略对各个数据库进行分类。

### <a name="classify-your-database"></a>对数据库进行分类

> [!NOTE]
> 下面的示例使用的是 Azure SQL 数据库，但你应选择要配置数据发现和分类的适当产品。

1. 转到 [Azure 门户](https://portal.azure.com)。

1. 请参阅 "Azure SQL 数据库" 窗格中 "安全" 标题下的 " **数据发现" & 分类** 。 "概述" 选项卡包含数据库的当前分类状态的摘要。 该摘要包含所有分类列的详细列表，你还可以对其进行筛选，以便仅显示特定的架构部分、信息类型和标签。 如果尚未对任何列进行分类，请 [跳到步骤 4](#step-4)。

1. 若要下载 Excel 格式的报表，请选择窗格顶部菜单中的“导出”。

1. <a id="step-4"></a>若要开始对数据进行分类，请选择“数据发现和分类”页面的“分类”选项卡 。

    分类引擎扫描数据库，寻找包含潜在敏感数据的列，并提供建议的列分类列表。

1. 查看并应用分类建议：

   - 若要查看建议的列分类列表，请选择窗格底部的“建议”面板。

   - 若要接受针对特定列的建议，请选中相关行左侧列中的复选框。 若要将所有建议标记为已接受，请选中建议表标题中最左侧的复选框。

   - 若要应用所选建议，请选择“接受所选建议”。

1. 还可以手动对列进行分类，这是基于建议分类的替代选项：

   1. 选择窗格顶部菜单中的“添加分类”。

   1. 在打开的上下文窗口中，选择要分类的架构、表和列，并选择信息类型和敏感度标签。

   1. 选择上下文窗口底部的“添加分类”。

1. 若要完成分类并永久使用新分类元数据标记数据库列，请在窗口顶部菜单中选择“保存”。

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>审核对敏感数据的访问

信息保护范例的一个重要方面是能够监视对敏感数据的访问。 [Azure SQL 审核](../../azure-sql/database/auditing-overview.md)已得到增强，在审核日志中包括了名为 `data_sensitivity_information` 的新字段。 此字段记录查询返回的数据的敏感度分类（标签）。 下面是一个示例：

![审核日志](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>权限

以下内置角色可读取数据库的数据分类：

- 所有者
- 读取器
- 参与者
- SQL 安全管理器
- 用户访问管理员

以下内置角色可修改数据库的数据分类：

- 所有者
- 参与者
- SQL 安全管理器

在 [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) 中了解有关基于角色的权限的详细信息。

## <a name="manage-classifications"></a><a id="manage-classification"></a>管理分类

可以使用 T-SQL、REST API 或 PowerShell 来管理分类。

### <a name="use-t-sql"></a>使用 T-SQL

可以使用 T-SQL 添加或删除列分类，以及检索整个数据库的所有分类。

> [!NOTE]
> 如果使用 T-SQL 管理标签，则不会验证组织信息保护策略（门户建议中显示的标签集）中是否存在添加到列的标签。 因此，是否要验证这一点完全由你决定。

有关使用 T-SQL 进行分类的信息，请参阅以下参考内容：

- 添加/更新一个列或多个列的分类：[添加敏感度分类](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 删除一个列或多个列的分类：[删除敏感度分类](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 查看数据库上的所有分类：[sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>使用 PowerShell cmdlet
可使用 PowerShell 管理 Azure SQL 数据库和 Azure SQL 托管实例的分类和建议。

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>适用于 Azure SQL 数据库的 PowerShell cmdlet

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>适用于 Azure SQL 托管实例的 PowerShell cmdlet

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>使用 REST API

可以使用 REST API 以编程方式管理分类和建议。 已发布的 REST API 支持以下操作：

- [创建或更新](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate)：创建或更新指定列的敏感度标签。
- [删除](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete)：删除指定列的敏感度标签。
- [禁用建议](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation)：禁用指定列的敏感度建议。
- [启用建议](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation)：启用指定列的敏感度建议。 （默认在所有列上启用建议。）
- [获取](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get)：获取指定列的敏感度标签。
- [按数据库列出当前敏感度](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase)：获取指定数据库的当前敏感度标签。
- [按数据库列出建议敏感度](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)：获取指定数据库的建议敏感度标签。

## <a name="next-steps"></a><a id="next-steps"></a>后续步骤

- 请考虑配置 [Azure SQL 审核](../../azure-sql/database/auditing-overview.md)来监视和审核对已分类敏感数据的访问。
- 对于包含数据发现 & 分类的演示文稿，请参阅 [查找、分类、标记 & 保护 SQL 数据 |公开的数据](https://www.youtube.com/watch?v=itVi9bkJUNc)。

---
title: 数据发现和分类
description: Azure SQL 数据库和 Azure Synapse Analytics 的数据发现和分类
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/21/2020
tags: azure-synapse
ms.openlocfilehash: f05b4d4fec99aaa2fb79da46e2167d883d1f15ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81766984"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL 数据库和 Azure Synapse Analytics 的数据发现和分类

& 分类的数据发现内置于 Azure SQL 数据库中。 它提供高级功能，用于发现、分类、标记和报告数据库中的敏感数据。

最敏感的数据可能包括业务、财务、医疗保健或个人信息。 发现和分类这些数据可以在组织的信息保护方法中扮演 pivotal 角色。 它可以充当基础结构，用于：

- 帮助满足数据隐私和法规遵从性要求的标准。
- 各种安全方案，如监视（审核）并在敏感数据存在异常访问时发出警报。
- 控制对包含高度敏感数据的数据库的访问并增强其安全性。

数据发现 & 分类是[高级数据安全](sql-database-advanced-data-security.md)产品/服务的一部分，它是一个用于高级 SQL 安全功能的统一包。 您可以通过 Azure 门户的中央**SQL 高级数据安全**部分访问和管理数据发现 & 分类。

> [!NOTE]
> 本文介绍 Azure SQL 数据库和 Azure Synapse 分析。 为简单起见，我们在此处使用*Sql 数据库*来引用 SQL 数据库和 Azure Synapse。 有关 SQL Server （本地）的信息，请参阅[SQL 数据发现和分类](https://go.microsoft.com/fwlink/?linkid=866999)。

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>什么是数据发现 & 分类？

数据发现 & 分类引入了一套高级服务和新的 SQL 数据库功能。 它构成了 SQL 数据库的新信息保护模式，旨在保护数据，而不仅仅是数据库。 范例包括：

- **发现和建议：** 分类引擎扫描数据库，并识别包含潜在敏感数据的列。 然后，它通过 Azure 门户提供一种简单的方法来查看和应用建议的分类。

- **标记：** 您可以使用已添加到 SQL 数据库引擎中的新元数据属性，将敏感度分类标签永久应用于列。 然后，此元数据可用于基于敏感的高级审核和保护方案。

- **查询结果集敏感度：** 出于审核目的，会实时计算查询结果集的敏感度。

- **可见性：** 可以在 Azure 门户的详细仪表板中查看数据库分类状态。 此外，还可以下载 Excel 格式的报表以用于符合性和审核目的以及其他需求。

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>发现、分类和标记敏感列

本部分介绍的步骤：

- 发现、分类和标记包含数据库中敏感数据的列。
- 查看数据库的当前分类状态和导出报告。

分类包含两种元数据属性：

- **标签**：主要分类属性，用于定义列中存储数据的敏感度级别。  
- **信息类型**：可提供有关列中存储的数据类型的更详细信息的属性。

### <a name="define-and-customize-your-classification-taxonomy"></a>定义和自定义分类

数据发现 & 分类附带了一组内置的敏感度标签和一个内置的信息类型和发现逻辑集。 现在，可以自定义此分类并专门针对你的环境定义分类构造的集合和级别。

你可以在一个中心位置为你的整个 Azure 组织定义和自定义你的分类分类。 此位置位于[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)，作为安全策略的一部分。 只有对组织根管理组具有管理权限的人员才能执行此任务。

作为 SQL 信息保护策略管理的一部分，你可以定义自定义标签，对其进行分级，并将其与一组选定的信息类型相关联。 你还可以添加自己的自定义信息类型，并通过字符串模式对它们进行配置。 将模式添加到发现逻辑，以便在数据库中标识此类型的数据。

详细了解如何自定义和管理策略，请参阅[SQL 信息保护策略操作方法指南](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)。

定义组织范围的策略后，可以继续使用自定义策略对单个数据库进行分类。

### <a name="classify-your-sql-database"></a>对 SQL 数据库进行分类

1. 转到 [Azure 门户](https://portal.azure.com)。

2. 在 "Azure SQL 数据库" 窗格中的 "**安全**" 标题下，中转到 "**高级数据安全性**"。 选择 "**高级数据安全性**"，然后选择 "**数据发现 & 分类**卡"。

   ![Azure 门户中的高级数据安全窗格](./media/sql-data-discovery-and-classification/data_classification.png)

3. 在 "**数据发现 & 分类**" 页上，"**概述**" 选项卡包含数据库的当前分类状态的摘要。 摘要包含所有已分类列的详细列表，你还可以筛选该列表以仅显示特定的架构部件、信息类型和标签。 如果尚未对任何列进行分类，请[跳到步骤 5](#step-5)。

   ![当前分类状态摘要](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. 若要下载 Excel 格式的报表，请选择窗格顶部菜单中的 "**导出**"。

5. <a id="step-5"></a>若要开始对数据进行分类，请选择 "**数据发现 & 分类**" 页上的 "**分类**" 选项卡。

    分类引擎会扫描你的数据库中是否包含可能敏感的数据，并提供建议的列分类列表。

6. 查看并应用分类建议：

   - 若要查看建议的列分类列表，请选择窗格底部的 "建议" 面板。

   - 若要接受特定列的建议，请选中相关行左侧列中的复选框。 若要将所有建议标记为 "已接受"，请在 "建议" 表标题中选择最左侧的复选框。

       ![查看并从分类建议列表中选择](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - 要应用所选建议，请选择 "**接受所选建议**"。

7. 还可以手动对列进行分类，作为替代分类或除基于建议的分类以外的其他分类：

   1. 选择窗格顶部菜单中的 "**添加分类**"。

   1. 在打开的上下文窗口中，选择要分类的架构、表和列，以及 "信息类型" 和 "敏感度" 标签。

   1. 选择上下文窗口底部的 "**添加分类**"。

      ![选择要分类的列](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. 若要通过新的分类元数据完成分类并永久标记（标记）数据库列，请在窗口顶部菜单中选择 "**保存**"。

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>审核对敏感数据的访问

信息保护范例的一个重要方面是能够监视对敏感数据的访问。 [AZURE SQL 数据库审核](sql-database-auditing.md)已经过增强，在审核日志中包含一个名`data_sensitivity_information`为的新字段。 此字段记录查询返回的数据的敏感度分类（标签）。 下面是一个示例：

![审核日志](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>访问

这些内置角色可以读取 Azure SQL 数据库的数据分类：

- “所有者”
- 读取器
- 参与者
- SQL 安全管理器
- 用户访问管理员

这些内置角色可以修改 Azure SQL 数据库的数据分类：

- “所有者”
- 参与者
- SQL 安全管理器

详细了解[适用于 Azure 资源的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)中基于角色的权限。

## <a name="manage-classifications"></a><a id="manage-classification"></a>管理分类

可以使用 T-sql、REST API 或 PowerShell 来管理分类。

### <a name="use-t-sql"></a>使用 T-SQL

您可以使用 T-sql 来添加或删除列分类，并检索整个数据库的所有分类。

> [!NOTE]
> 使用 T-sql 管理标签时，不会验证您添加到某一列中的标签是否存在于组织的信息保护策略（门户中显示的标签集）中。 那么，就是由您来验证这一点。

有关使用 T-sql 进行分类的信息，请参阅以下参考资料：

- 添加或更新一个或多个列的分类：[添加敏感度分类](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 删除一个或多个列的分类：删除[敏感度分类](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 查看数据库中的所有分类： [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>使用 Rest API

您可以使用 REST API 以编程方式管理分类和建议。 已发布的 REST API 支持以下操作：

- [创建或更新](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate)：创建或更新指定列的敏感度标签。
- [删除](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete)：删除指定列的敏感度标签。
- [禁用建议](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation)：对指定列禁用敏感性建议。
- [启用建议](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation)：对指定列启用敏感性建议。 （默认情况下，对所有列启用建议。）
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get)：获取指定列的敏感度标签。
- [按数据库列出当前的列表](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase)：获取指定数据库的当前敏感度标签。
- [数据库推荐的列表](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)：获取指定数据库的建议敏感度标签。

### <a name="use-powershell-cmdlets"></a>使用 PowerShell cmdlet
可以使用 PowerShell 管理 Azure SQL 数据库和托管实例的分类和建议。

#### <a name="powershell-cmdlets-for-sql-database"></a>用于 SQL 数据库的 PowerShell cmdlet

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>托管实例的 PowerShell cmdlet

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>后续步骤

- 详细了解[高级数据安全性](sql-database-advanced-data-security.md)。
- 请考虑配置 [Azure SQL 数据库审核](sql-database-auditing.md) 来监视和审核对已分类敏感数据的访问。
- 对于包含数据发现和分类的演示文稿，请参阅[发现、分类、标记 & 保护 SQL 数据 |公开的数据](https://www.youtube.com/watch?v=itVi9bkJUNc)。

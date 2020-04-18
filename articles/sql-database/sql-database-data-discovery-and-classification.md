---
title: 数据发现和分类
description: Azure SQL 数据库和 Azure 突触分析的数据发现&分类
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
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 599b2a280e386e49eb114f448f55b17ed7e823d7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616766"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL 数据库和 Azure 突触分析的数据发现&分类

数据发现&分类内置于 Azure SQL 数据库中。 它提供了用于发现、分类、标记和报告数据库中敏感数据的高级功能。

您最敏感的数据可能包括业务、财务、医疗保健或个人信息。 发现和分类此数据可以在组织的信息保护方法中发挥关键作用。 它可以充当基础结构，用于：

- 帮助满足数据隐私标准和法规遵从性要求。
- 各种安全方案，如监视（审核）并在敏感数据存在异常访问时发出警报。
- 控制对包含高度敏感数据的数据库的访问并强化安全性。

数据发现&分类是[高级数据安全](sql-database-advanced-data-security.md)产品（用于高级 SQL 安全功能的统一包）的一部分。 您可以通过 Azure 门户的中央**SQL 高级数据安全**部分访问和管理数据发现&分类。

> [!NOTE]
> 本文涉及 Azure SQL 数据库和 Azure 突触分析。 为简单起见，我们在此处使用*SQL 数据库*来引用 SQL 数据库和 Azure 突触。 有关 SQL 服务器（本地）的信息，请参阅[SQL 数据发现和分类](https://go.microsoft.com/fwlink/?linkid=866999)。

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>什么是数据发现&分类？

数据发现&分类引入了一组高级服务和新的 SQL 数据库功能。 它为 SQL 数据库形成了一种新的信息保护范例，旨在保护数据，而不仅仅是数据库。 范例包括：

- **发现和建议：** 分类引擎扫描数据库并标识包含潜在敏感数据的列。 然后，它提供了一种通过 Azure 门户查看和应用建议分类的简单方法。

- **标签：** 通过使用已添加到 SQL 数据库引擎的新元数据属性，可以持久地将敏感度分类标签应用于列。 然后，此元数据可用于高级、基于灵敏度的审核和保护方案。

- **查询结果集敏感度：** 查询结果集的灵敏度是实时计算的，以便进行审核。

- **可见性：** 您可以在 Azure 门户中的详细仪表板中查看数据库分类状态。 此外，您还可以下载 Excel 格式的报告，用于合规性和审核目的以及其他需求。

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>发现、分类和标记敏感列

本节介绍以下步骤：

- 发现、分类和标记数据库中包含敏感数据的列。
- 查看数据库的当前分类状态并导出报表。

分类包含两种元数据属性：

- **标签**：用于定义存储在列中的数据的敏感度级别的主要分类属性。  
- **信息类型**：提供有关列中存储的数据类型的更精细信息的属性。

### <a name="define-and-customize-your-classification-taxonomy"></a>定义和自定义分类

数据发现&分类附带一组内置的敏感度标签和一组内置的信息类型和发现逻辑。 现在，可以自定义此分类并专门针对你的环境定义分类构造的集合和级别。

在整个 Azure 组织的一个中心位置定义和自定义分类分类。 该位置位于[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)中，作为安全策略的一部分。 只有具有组织根管理组管理权限的人员才能执行此任务。

作为 SQL 信息保护策略管理的一部分，您可以定义自定义标签、对它们进行排名，并将它们与一组选定的信息类型相关联。 您还可以添加自己的自定义信息类型，并配置它们与字符串模式。 模式将添加到发现逻辑中，用于标识数据库中的此类数据。

在[SQL 信息保护策略"指南"](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)中了解有关自定义和管理策略的详细信息。

定义全组织策略后，可以使用自定义策略继续对各个数据库进行分类。

### <a name="classify-your-sql-database"></a>对 SQL 数据库进行分类

1. 转到 [Azure 门户](https://portal.azure.com)。

2. 转到 Azure SQL 数据库窗格中 **"安全**"标题下的 **"高级数据安全**"。 选择**高级数据安全性**，然后选择 **"数据发现&分类**卡。

   ![Azure 门户中的高级数据安全窗格](./media/sql-data-discovery-and-classification/data_classification.png)

3. 在 **"数据发现&分类**页上，"**概述"** 选项卡包含数据库当前分类状态的摘要。 摘要包括所有分类列的详细列表，您还可以筛选该列表以仅显示特定的架构部件、信息类型和标签。 如果尚未对任何列进行分类，[请跳到步骤 5](#step-5)。

   ![当前分类状态摘要](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. 要下载 Excel 格式的报表，请在窗格的顶部菜单中选择 **"导出**"。

5. <a id="step-5"></a>要开始对数据进行分类，请选择 **"数据发现&分类**页上的 **"分类"** 选项卡。

    分类引擎扫描数据库，以寻找包含潜在敏感数据的列，并提供建议的列分类列表。

6. 查看和应用分类建议：

   - 要查看建议的列分类列表，请选择窗格底部的建议面板。

   - 要接受特定列的建议，请选择相关行左列中的复选框。 要将所有建议标记为已接受，请在建议表标题中选择最左侧的复选框。

       ![从分类建议列表中查看和选择](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - 要应用所选建议，请选择"**接受所选建议**"。

7. 您还可以手动对列进行分类，作为替代分类，或者除基于建议的分类之外：

   1. 在窗格的顶部菜单中选择 **"添加分类**"。

   1. 在打开的上下文窗口中，选择要分类的架构、表和列以及信息类型和敏感度标签。

   1. 选择 **"在**上下文窗口底部添加分类"。

      ![选择要分类的列](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. 要使用新的分类元数据完成分类并持续标记数据库列，请选择"**保存在**窗口的顶部菜单"。

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>审核对敏感数据的访问

信息保护范式的一个重要方面是能够监视对敏感数据的访问。 [Azure SQL 数据库审核](sql-database-auditing.md)已得到增强，以在审核日志中包括一个名为`data_sensitivity_information`的新字段。 此字段记录查询返回的数据的敏感度分类（标签）。 下面是一个示例：

![审核日志](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>权限

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

详细了解[Azure 资源的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)中基于角色的权限。

## <a name="manage-classifications"></a><a id="manage-classification"></a>管理分类

您可以使用 TSQL、REST API 或 PowerShell 来管理分类。

### <a name="use-t-sql"></a>使用 T-SQL

可以使用 T-SQL 添加或删除列分类，并检索整个数据库的所有分类。

> [!NOTE]
> 使用 T-SQL 管理标签时，无法验证添加到列中的标签存在于组织的信息保护策略（门户建议中显示的标签集）中。 因此，由您来验证这一点。

有关将 T-SQL 用于分类的信息，请参阅以下引用：

- 要添加或更新一个或多个列的分类：[添加敏感分类](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 要从一个或多个列中删除分类[：DROP 敏感分类](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 要查看数据库上的所有分类[：sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>使用休息 API

您可以使用 REST API 以编程方式管理分类和建议。 已发布的 REST API 支持以下操作：

- [创建或更新](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate)：创建或更新指定列的敏感度标签。
- [删除](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete)：删除指定列的敏感度标签。
- [禁用建议](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation)：禁用指定列上的敏感度建议。
- [启用建议](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation)：启用指定列上的敏感度建议。 （默认情况下在所有列上启用建议。
- [获取](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get)：获取指定列的敏感度标签。
- [按数据库列出当前](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase)值值 ：获取指定数据库的当前敏感度标签。
- [按数据库推荐列表](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)：获取指定数据库的建议敏感度标签。

### <a name="use-powershell-cmdlets"></a>使用 PowerShell cmdlet
可以使用 PowerShell 管理 Azure SQL 数据库和托管实例的分类和建议。

#### <a name="powershell-cmdlets-for-sql-database"></a>用于 SQL 数据库的 PowerShell cmdlet

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>用于托管实例的 PowerShell cmdlet

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>后续步骤

- 了解有关[高级数据安全性](sql-database-advanced-data-security.md)的更多。
- 请考虑配置 [Azure SQL 数据库审核](sql-database-auditing.md) 来监视和审核对已分类敏感数据的访问。
- 有关包含数据发现和分类的演示文稿，请参阅[发现、分类、标记&保护 SQL 数据 |数据暴露](https://www.youtube.com/watch?v=itVi9bkJUNc)。

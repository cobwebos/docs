---
title: 在 Azure 安全中心中自定义 SQL 信息保护策略 | Microsoft Docs
description: 了解如何在 Azure 安全中心中自定义信息保护策略。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: ac4aa3c8aafae24736cc7b48a9ca4e8ba9fd0742
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125181"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>在 Azure 安全中心（预览版）中自定义 SQL 信息保护策略
 
可以在 Azure 安全中心为整个 Azure 租户定义和定制 SQL 信息保护策略。

信息保护是一种高级安全功能，用于发现、分类、标记和保护 Azure 数据资源中的敏感数据。 发现最敏感的数据（业务、财务、医疗保健、 PII 等）并进行分类可在组织的信息保护方面发挥关键作用。 它可以作为基础结构，用于：
- 帮助满足数据隐私标准和法规符合性要求
- 各种安全方案，如监视（审核）并在敏感数据存在异常访问时发出警报
- 控制对包含高度敏感数据的数据存储的访问并增强其安全性
 
[SQL 信息保护](../sql-database/sql-database-data-discovery-and-classification.md)为 SQL 数据存储实现此范例，目前 Azure SQL 数据库支持该范例。 SQL 信息保护自动发现和分类潜在的敏感数据，提供标记机制，用于通过分类属性永久标记敏感数据，并提供显示数据库分类状态的详细仪表板。 此外，它还会计算 SQL 查询的结果集敏感性，以便可以显式审核提取敏感数据的查询，并且可以保护数据。 有关 SQL 信息保护的更多详细信息，请参阅 [Azure SQL 数据库数据发现和分类](../sql-database/sql-database-data-discovery-and-classification.md)。
 
该分类机制基于构成分类的两个主要构造：标签和信息类型。
- **标签**：主要分类属性，用于定义列中存储的数据的敏感度级别。 
- **信息类型**：为列中存储的数据的类型提供其他粒度。
 
信息保护服务随内置的一组标签和信息类型（默认使用）提供。 要自定义这些内容，可以在 Azure 安全中心中自定义信息保护策略。
 
## <a name="customize-the-information-protection-policy"></a>自定义信息保护策略
要自定义 Azure 租户的信息保护策略，需要拥有[租户根管理组的管理权限](security-center-management-groups.md)。 
 
1. 在安全中心主菜单中，选择“安全策略”。
2. 选择“层次结构视图 (预览)”，然后在“租户根组”下，单击“编辑设置”。
 
   ![配置信息保护策略](./media/security-center-info-protection-policy/security-policy.png) 
 
3. 在“策略组件”下，单击“信息保护”。 在“信息保护设置”页中，可以查看当前的标签集。 这些是用于对数据的敏感度级别进行分类的主要分类属性。 此处，可以为租户配置信息保护标签和信息类型。 
 
### <a name="customizing-labels"></a>自定义标签
 
1. 可以编辑或删除任何现有标签，也可以添加新标签。 要编辑现有标签，请选择该标签，然后单击顶部或右侧上下文菜单中的“配置”。 要添加新标签，请单击顶部菜单栏或标签表底部的“创建标签”。
2. 在“配置敏感度标签”屏幕中，可以创建或更改标签名称和描述。 还可以通过打开或关闭“已启用”开关来设置标签的状态（活动或禁用）。 最后，可以添加或删除与标签关联的信息类型。 若发现与该信息类型匹配的任何数据，该数据将自动在分类建议中包含关联的敏感度标签。
3. 单击“确定”。
 
   ![配置敏感度标签](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. 标签按敏感度升序排列。 要更改标签之间的排名，请拖动标签以在表格中重新排序，或使用“上移”和“下移”按钮更改顺序。 
 
    ![配置信息保护策略](./media/security-center-info-protection-policy/move-up.png)
 
5. 完成设置后，请务必单击屏幕顶部的“保存”。
 
 
## <a name="adding-and-customizing-information-types"></a>添加和自定义信息类型
 
1. 可以通过单击“管理信息类型”来管理和自定义信息类型。
2. 要添加新的信息类型，请在顶部菜单中选择“创建信息类型”。 可以为“信息类型”配置名称、描述和搜索模式字符串。 搜索模式字符串可以选择使用带有通配符的关键字（使用字符 %），自动发现引擎根据列的元数据使用该关键字来识别数据库中的敏感数据。
 
    ![配置信息保护策略](./media/security-center-info-protection-policy/info-types.png)
 
3. 还可以通过添加其他搜索模式字符串、禁用某些现有字符串或更改说明来配置内置信息类型。 无法删除内置信息类型或编辑其名称。 
4. 信息类型按发现排名升序列出，这意味着列表中排名较高的类型将先尝试匹配。 要更改信息类型之间的排名，请将类型拖动到表格中的正确位置，或使用“上移”和“下移”按钮更改顺序。 
5. 完成后单击“确定”。
6. 完成信息类型管理后，请确保关联相关类型和相关标签，方法是单击特定标签的“配置”，然后根据需要添加或删除信息类型。
7. 请务必单击主“标签”边栏选项卡中的“保存”以应用所有更改。
 
完全定义并保存信息保护策略后，该策略将应用于租户中所有 Azure SQL 数据库的数据分类。
 
 
## <a name="next-steps"></a>后续步骤
 
本文介绍了如何在 Azure 安全中心中定义 SQL 信息保护策略。 若要详细了解如何使用 SQL 信息保护对 SQL 数据库中的敏感数据进行分类和保护，请参阅 [Azure SQL 数据库数据发现和分类](../sql-database/sql-database-data-discovery-and-classification.md)。 

有关 Azure 安全中心中的安全策略和数据安全性的详细信息，请参阅以下文章：
 
- [安全策略概述](security-center-policies-overview.md)：获取安全中心中的安全策略概述
- [在 Azure 安全中心中设置安全策略](security-center-policies.md)：了解如何配置 Azure 订阅和资源组的安全策略
- [Azure 安全中心的数据安全](security-center-data-security.md)：了解安全中心如何管理数据并确保数据安全性



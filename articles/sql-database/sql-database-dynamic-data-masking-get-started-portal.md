---
title: Azure 门户：动态数据掩码
description: 如何开始在 Azure 门户中使用 SQL 数据库动态数据掩码
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: a8098b31c6b389b640fc03e756da44c70d9f3a70
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722112"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Azure 门户的 SQL 数据库动态数据掩码入门

本文介绍如何使用 Azure 门户实现[动态数据掩码](sql-database-dynamic-data-masking-get-started.md)。 还可以使用 [Azure SQL 数据库 cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 实现动态数据掩码。

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>使用 Azure 门户为数据库设置动态数据掩码

1. 在 [https://portal.azure.com](https://portal.azure.com) 中启动 Azure 门户。
2. 导航到要掩码的敏感数据所在数据库的设置页。
3. 单击“动态数据掩码”磁贴，这会启动“动态数据掩码”配置页。

   * 或者，可以向下滚动到“操作”部分并单击“动态数据屏蔽”。

     ![“导航”窗格](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. 在“动态数据掩码”配置页中，可能会看到一些数据库列，建议引擎已将这些列标记为需要进行掩码。 要接受这些建议，可直接针对一个或多个列单击“添加掩码”，系统就会根据该列的默认类型来创建掩码。 可以更改屏蔽函数，只需单击屏蔽规则，并将屏蔽字段格式编辑成所选的其他格式即可。 请确保单击“保存”以保存设置。

    ![“导航”窗格](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. 若要为数据库中的任意列添加掩码，请在“动态数据掩码”配置页的顶部单击“添加掩码”以打开“添加掩码规则”配置页。

    ![“导航”窗格](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. 选择“架构”、“表”和“列”，以定义要掩码的指定字段。
7. 从敏感数据屏蔽类别列表中选择“屏蔽字段格式”。

    ![“导航”窗格](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. 在数据掩码规则页中单击“保存”，以更新动态数据掩码策略中的掩码规则集。
9. 键入不应对其进行屏蔽的 SQL 用户或 AAD 标识，允许其访问未屏蔽的敏感数据。 这些用户在键入时应该采用分号分隔用户列表的形式。 拥有管理员权限的用户始终可以访问原始的未掩码数据。

    ![“导航”窗格](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > 若要使应用程序层向应用程序特权用户显示敏感数据，请添加应用程序查询数据库时需要使用的 SQL 用户或 AAD 标识。 强烈建议在此列表中包含最少量的特权用户，以最大程度地降低泄露敏感数据的风险。

10. 在数据掩码配置页中单击“保存”，以保存新的或更新的掩码策略。

## <a name="next-steps"></a>后续步骤

* 有关动态数据掩码的概述，请参阅[动态数据掩码](sql-database-dynamic-data-masking-get-started.md)。
* 还可以使用 [Azure SQL 数据库 cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 实现动态数据掩码。

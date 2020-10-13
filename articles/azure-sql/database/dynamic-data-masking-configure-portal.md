---
title: Azure 门户：动态数据屏蔽
description: 如何开始在 Azure 门户中使用 Azure SQL 数据库动态数据掩码
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: 1d4c29cdb9cf24f87df4acc3556b318a1079810a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443616"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Azure 门户的 SQL 数据库动态数据掩码入门
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文介绍如何使用 Azure 门户实现[动态数据掩码](dynamic-data-masking-overview.md)。 还可以使用 [Azure SQL 数据库 cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 实现动态数据掩码。

> [!NOTE]
> 不能使用 Azure Synapse 的门户（请使用 PowerShell 或 REST API）或 SQL 托管实例设置此功能。 有关详细信息，请参阅 [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)。

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>使用 Azure 门户为数据库设置动态数据掩码

1. 在 [https://portal.azure.com](https://portal.azure.com) 中启动 Azure 门户。
2. 导航到要掩码的敏感数据所在数据库的设置页。
3. 单击数据库“安全”部分下的“动态数据掩码”边栏选项卡 。

   ![显示动态数据掩码突出显示的 "安全" 部分的屏幕截图。](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. 在“动态数据掩码”配置页中，可能会看到一些数据库列，建议引擎已将这些列标记为需要进行掩码。 要接受这些建议，可直接针对一个或多个列单击“添加掩码”，系统就会根据该列的默认类型来创建掩码。 用户可以更改掩码函数，只需单击掩码规则，并将掩码字段格式编辑成用户所选择的其他格式即可。 请确保单击“保存”以保存设置。

    ![显示 "动态数据掩码配置" 页的屏幕截图。](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. 若要为数据库中的任意列添加掩码，请在“动态数据掩码”配置页的顶部单击“添加掩码”以打开“添加掩码规则”配置页。

    ![显示 "添加屏蔽规则" 配置页的屏幕截图。](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. 选择“架构”、“表”和“列”，以定义要掩码的指定字段。
7. 从敏感数据掩码类别列表中“选择掩码方式”。

    ![屏幕截图，显示 "选择如何屏蔽" 部分下的敏感数据掩码类别。](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. 在数据掩码规则页中单击“添加”，以更新动态数据掩码策略中的掩码规则集。
9. 键入不应对其进行掩码的 SQL 用户或 Azure Active Directory (Azure AD) 标识，允许其访问未掩码的敏感数据。 这些用户在键入时应该采用分号分隔用户列表的形式。 拥有管理员权限的用户始终可以访问原始的未掩码数据。

    ![导航窗格](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > 若要使应用程序层向应用程序特权用户显示敏感数据，请添加应用程序查询数据库时需要使用的 SQL 用户或 Azure AD 标识。 强烈建议在此列表中包含最少量的特权用户，以最大程度地降低泄露敏感数据的风险。

10. 在数据掩码配置页中单击“保存”，以保存新的或更新的掩码策略。

## <a name="next-steps"></a>后续步骤

- 有关动态数据掩码的概述，请参阅[动态数据掩码](dynamic-data-masking-overview.md)。
- 还可以使用 [Azure SQL 数据库 cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 实现动态数据掩码。

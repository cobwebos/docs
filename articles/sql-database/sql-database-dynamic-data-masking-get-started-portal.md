---
title: "Azure 门户：SQL 数据库动态数据掩码 | Microsoft Docs"
description: "如何开始在 Azure 门户中使用 SQL 数据库动态数据掩码"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: 2
ms.service: sql-database
ms.custom: security-protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/22/2016
ms.author: ronitr; ronmat
translationtype: Human Translation
ms.sourcegitcommit: 07913359addaa49fe8d408a8e363f554419e4de2
ms.openlocfilehash: 16db41c83ca5c582aca7581ae9b6a03a3d99ab15
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Azure 门户的 SQL 数据库动态数据掩码入门

本主题介绍如何使用 Azure 门户实现[动态数据掩码](sql-database-dynamic-data-masking-get-started.md)。 还可以使用 [Azure SQL 数据库 cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx) 或 [REST API](https://msdn.microsoft.com/library/dn505719.aspx) 实现动态数据掩码。


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>使用 Azure 门户为数据库设置动态数据屏蔽
1. 启动 Azure 门户 [https://portal.azure.com](https://portal.azure.com)。
2. 导航到你要屏蔽的敏感数据所在数据库的设置边栏选项卡。
3. 单击“动态数据屏蔽”磁贴，这会启动“动态数据屏蔽”配置边栏选项卡。
   
   * 或者，可以向下滚动到“操作”部分并单击“动态数据屏蔽”。
     
     ![导航窗格](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. 在“动态数据屏蔽”配置边栏选项卡中，你可能会看到一些数据库列，建议引擎已将这些列标记为需要进行屏蔽。 若要接受这些建议，可直接针对一个或多个列单击“添加屏蔽”，然后系统就会根据该列的默认类型来创建屏蔽。 你可以更改屏蔽函数，只需单击屏蔽规则，然后将屏蔽字段格式编辑成你所选择的其他格式即可。 请确保单击“保存”以保存你的设置。
   
    ![导航窗格](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. 若要为数据库中的任意列添加屏蔽，请在“动态数据屏蔽”配置边栏选项卡的顶部单击“添加屏蔽”以打开“添加屏蔽规则”配置边栏选项卡
   
    ![导航窗格](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. 选择“架构”、“表”和“列”，以定义要屏蔽的指定字段。
7. 从敏感数据屏蔽类别列表中选择“屏蔽字段格式”。
   
    ![导航窗格](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. 在数据屏蔽规则边栏选项卡中单击“保存”，以更新动态数据屏蔽策略中的屏蔽规则集。
9. 键入不应对其进行屏蔽的 SQL 用户或 AAD 标识，允许其访问未屏蔽的敏感数据。 这些用户在键入时应该采用分号分隔用户列表的形式。 请注意，拥有管理员权限的用户始终可以访问原始的未屏蔽数据。
   
    ![导航窗格](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > 若要使应用程序层向应用程序特权用户显示敏感数据，请添加应用程序查询数据库时需要使用的 SQL 用户或 AAD 标识。 强烈建议在此列表中包含最少量的特权用户，以最大程度地降低泄露敏感数据的风险。
   > 
   > 
10. 在数据屏蔽配置边栏选项卡中单击“保存”，以保存新的或更新的屏蔽策略。


## <a name="next-steps"></a>后续步骤

* 有关动态数据掩码的概述，请参阅[动态数据掩码](sql-database-dynamic-data-masking-get-started.md)。
* 还可以使用 [Azure SQL 数据库 cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx) 或 [REST API](https://msdn.microsoft.com/library/dn505719.aspx) 实现动态数据掩码。

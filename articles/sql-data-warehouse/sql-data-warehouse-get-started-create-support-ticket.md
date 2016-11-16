---
title: "如何创建 SQL 数据仓库的支持票证 | Microsoft Docs"
description: "如何在 SQL 数据仓库中创建支持票证。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ede330870f451bd1e0ed6805fa937e74438fd644


---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>如何创建 SQL 数据仓库的支持票证
如果你在 SQL 数据仓库方面遇到任何问题，请创建支持票证，以便我们的工程团队可提供帮助。

## <a name="create-a-support-ticket"></a>创建支持票证
1. 打开 [Azure 门户][Azure portal]。
2. 在“主页”屏幕上，单击“帮助 + 支持”磁贴。
   
    ![帮助 + 支持](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)
3. 在“帮助 + 支持”边栏选项卡上，单击“创建支持请求”。
   
    ![新建支持请求](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
   
    <a name="request-quota-change"></a> 
4. 选择“请求类型”
   
    ![请求类型](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
   
   > [!NOTE]
   > 默认情况下，每个 SQL server (例如 myserver.database.windows.net) 具有 45,000 的 **DTU 配额** 。 此配额仅仅只是安全限制。 可以通过创建支持票证并选择“配额”  作为请求类型来增加配额。 若要计算 DTU 需求，可将总 [DWU][DWU] 需求乘以 7.5。 例如，如果想要在一个 SQL Server 上托管两个 DW6000，应请求 90,000 的 DTU 配额。  您可以在门户中的 SQL server 边栏选项卡中查看您当前的 DTU 消耗量。 已暂停和未暂停的数据库都计入 DTU 配额。 
   > 
   > 
5. 选择发生了您要报告其问题的数据库所在的 **订阅** 。
   
    ![订阅](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)
6. 选择“SQL 数据仓库”作为资源。
   
    ![资源](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)
7. 选择 [Azure 支持计划][Azure support plan]。
   
   * **计费、配额和订阅管理** 支持可用于所有支持级别。
   * **故障维修服务**支持通过 [开发人员支持][Developer]、[标准支持][Standard]、[专业直接支持][Professional Direct]或[顶级支持][Premier]提供。 故障维修服务问题是客户在使用 Azure 时遇到的确定是由 Microsoft 引起的问题。
   * 我们在 [专业直接][Professional Direct] 和 [顶级][Premier] 支持级别提供**开发人员辅导**与**咨询服务**。 
     
     如果购买了顶级支持计划，则还可以在 [Microsoft Premier 在线门户][Microsoft Premier online portal]中报告 SQL 数据仓库的相关问题。  若要深入了解各种支持计划（包括范围、响应时间、定价等），请参阅 [Azure 支持计划][Azure support plan]。有关 Azure 支持的常见问题，请参阅 [Azure 支持常见问题][Azure support FAQs]。  
     
     ![支持计划](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)
8. 选择“问题类型”和“类别”。
   
    ![问题类型和类别](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)
9. 描述问题并选择业务影响级别。
   
    ![问题描述](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)
10. 系统会在此支持票证中预先填充你的 **联系信息** 。 请根据需要更新这些信息。
    
    ![联系信息](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)
11. 单击“创建”提交支持请求。

## <a name="monitor-a-support-ticket"></a>监视支持票证
在你提交支持请求后，Azure 支持团队将与你取得联系。 若要查看请求状态和详细信息，请单击仪表板上的“管理支持请求”。

![查看状态](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>其他资源
此外，可在 [Stack Overflow][Stack Overflow] 或 [Azure SQL 数据仓库 MSDN 论坛][Azure SQL Data Warehouse MSDN forum]上与 SQL 数据仓库社区互动。

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Azure 门户]: https://portal.azure.com/
[Azure 支持计划]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[开发人员]: https://azure.microsoft.com/support/plans/developer/  
[标准]: https://azure.microsoft.com/support/plans/standard/  
[专业直接]: https://azure.microsoft.com/support/plans/prodirect/  
[顶级支持]: https://azure.microsoft.com/support/plans/premier/  
[Azure 支持常见问题]: https://azure.microsoft.com/support/faq/
[Microsoft Premier 在线门户]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL 数据仓库 MSDN 论坛]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/




<!--HONumber=Nov16_HO2-->



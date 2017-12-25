---
title: "如何创建 SQL 数据仓库的支持票证 | Microsoft Docs"
description: "如何在 SQL 数据仓库中创建支持票证。"
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: 66aa12431e39ce295ce76411693bd20535a5c70c
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2017
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>如何创建 SQL 数据仓库的支持票证
如果在 SQL 数据仓库方面遇到任何问题，请创建支持票证，让我们的工程支持团队帮助你。

## <a name="create-a-support-ticket"></a>创建支持票证
1. 打开 [Azure 门户][Azure portal]。
2. 在“主页”屏幕上，单击“帮助 + 支持”选项卡。
   
    ![帮助 + 支持](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. 在“帮助 + 支持”边栏选项卡上单击“新建支持请求”，然后填充“基本信息”边栏选项卡。

   选择 [Azure 支持计划][Azure support plan]。
   
   * **计费、配额和订阅管理**支持适用于所有支持级别。
   * **故障维修服务**支持通过[开发人员支持][Developer]、[标准支持][Standard]、[专业直接支持][Professional Direct]或[顶级支持][Premier]提供。 故障维修服务问题是客户在使用 Azure 时遇到的确定是由 Microsoft 引起的问题。
   * 我们在[专业直接支持][Professional Direct]和[顶级支持][Premier]级别提供**开发人员辅导**与**咨询服务**。 
     
     如果购买了顶级支持计划，还可以在 [Microsoft Premier 在线门户][Microsoft Premier online portal]中报告 SQL 数据仓库的相关问题。  若要深入了解各种支持计划（包括范围、响应时间、定价等），请参阅 [Azure 支持计划][Azure support plan]。有关 Azure 支持的常见问题，请参阅 [Azure 支持常见问题][Azure support FAQs]。  
        
    ![“基本信息”边栏选项卡](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
    ![“基本信息”边栏选项卡 1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. 填写“问题”边栏选项卡。
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > 默认情况下，每个 SQL Server（例如 myserver.database.windows.net）都有 45,000 的 **DTU 配额**。 此配额仅仅只是安全限制。 可以通过创建支持票证并选择“配额”  作为请求类型来增加配额。 若要计算 DTU 需求，请将所需的 [DWU][DWU] 总数乘以 7.5。 例如，如果想要在一个 SQL Server 上托管两个 DW6000，应请求 90,000 的 DTU 配额。  可以在门户中的 SQL server 边栏选项卡中查看当前 DTU 消耗量。 已暂停和未暂停的数据库都计入 DTU 配额。 
   > 
   > 
   
5. 填写**联系信息**。
![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. 单击“创建”提交支持请求。

## <a name="monitor-a-support-ticket"></a>监视支持票证
在提交支持请求后，Azure 支持团队将与你取得联系。 若要查看请求状态和详细信息，请单击仪表板上的“所有支持请求”。

![查看状态](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>其他资源
此外，可在 [Stack Overflow][Stack Overflow] 或 [Azure SQL 数据仓库 MSDN 论坛][Azure SQL Data Warehouse MSDN forum]上与 SQL 数据仓库社区互动。

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/


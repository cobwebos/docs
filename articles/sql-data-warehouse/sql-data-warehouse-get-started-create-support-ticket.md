---
title: 如何创建支持票证
description: 如何在 Azure Synapse Analytics 中创建支持票证。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195653"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>如何为 Azure Synapse Analytics 创建支持票证
如果你在使用 Azure Synapse 分析时遇到任何问题，请创建支持票证，以便工程支持团队可提供帮助。

## <a name="create-a-support-ticket"></a>创建支持票证
1. 打开 [Azure 门户](https://portal.azure.com/)。
1. 在“主页”屏幕上，单击“帮助 + 支持”选项卡。
   
    ![帮助 + 支持](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. 查看[Azure 支持计划](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。
   
   * **计费、配额和订阅管理**支持适用于所有支持级别。
   * **中断-修复**支持通过[开发人员](https://azure.microsoft.com/support/plans/developer/)、[标准](https://azure.microsoft.com/support/plans/standard/)、[专业直接](https://azure.microsoft.com/support/plans/prodirect/)或[顶级](https://azure.microsoft.com/support/plans/premier/)支持提供。 故障维修服务问题是客户在使用 Azure 时遇到的确定是由 Microsoft 引起的问题。
   * **开发人员辅导**和**咨询服务**在[专业直接](https://azure.microsoft.com/support/plans/prodirect/)和[顶级](https://azure.microsoft.com/support/plans/premier/)支持级别提供。 
     
     如果有顶级支持计划，还可以在[Microsoft Premier online 门户](https://premier.microsoft.com/)上报告 SQL 数据仓库的相关问题。 若要深入了解各种支持计划（包括范围、响应时间、定价等），请参阅[Azure 支持计划](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。 有关 Azure 支持的常见问题，请参阅[azure 支持常见问题](https://azure.microsoft.com/support/faq/)。
1. 在 "**帮助 + 支持**" 页上，选择 "**新建支持请求**"。 从下拉菜单中选择 "问题类型"。 然后继续填写 "**基本**信息" 选项卡中的信息。输入问题**摘要**，并从菜单中选择**问题类型**，然后选择 "保存"。

    ![帮助 + 支持](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > 默认情况下，每个 SQL Server（例如 myserver.database.windows.net）都有 45,000 的 **DTU 配额**。 此配额仅仅只是安全限制。 可以通过创建支持票证并选择“配额” 作为请求类型来增加配额。 若要计算 DTU 需求，请将7.5 乘以[DWU](sql-data-warehouse-overview-what-is.md)所需的总大小。 例如，如果想要在一个 SQL Server 上托管两个 DW6000，应请求 90,000 的 DTU 配额。  可以在门户中的 SQL server 边栏选项卡中查看当前 DTU 消耗量。 已暂停和未暂停的数据库都计入 DTU 配额。 
   > 

1. 你可能会看到用于帮助解决问题的解决方案。 如果提供的解决方案无法解决问题，请选择 "**下一步：详细信息**"。 提交问题的详细信息和联系信息。 选择 "**下一步"：查看和创建**
![详细信息](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. 查看信息，然后选择窗体底部的 "**创建**"，提交支持请求。

## <a name="monitor-a-support-ticket"></a>监视支持票证
提交支持请求后，Azure 支持团队将与你联系。 若要查看请求状态和详细信息，请单击仪表板上的“所有支持请求”。

![查看状态](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>其他资源
你还可以在[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)上或通过[Azure SQL 数据仓库 MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)连接到 SQL 数据仓库社区。

 

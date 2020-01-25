---
title: 如何创建支持票证
description: Azure SQL Data Warehouse에서 지원 티켓을 만드는 방법
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717837"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>SQL Data Warehouse에 대한 지원 티켓을 만드는 방법
如果你在使用 SQL 数据仓库时遇到任何问题，请创建支持票证，以便工程支持团队帮助你。

## <a name="create-a-support-ticket"></a>지원 티켓 만들기
1. [Azure Portal](https://portal.azure.com/)을 엽니다.
2. 홈 화면에서 **도움말 + 지원** 탭을 클릭합니다.
   
    ![도움말 + 지원](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. 도움말 + 지원 블레이드에서 **새 지원 요청**을 클릭하고 **기본** 블레이드에 정보를 입력합니다.

   [Azure 지원 계획](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)을 선택합니다.
   
   * **청구, 할당량 및 구독 관리** 지원은 모든 지원 수준에서 제공됩니다.
   * **中断-修复**支持通过[开发人员](https://azure.microsoft.com/support/plans/developer/)、[标准](https://azure.microsoft.com/support/plans/standard/)、[专业直接](https://azure.microsoft.com/support/plans/prodirect/)或[顶级](https://azure.microsoft.com/support/plans/premier/)支持提供。 중단-수정 문제는 고객이 Azure를 사용하는 동안 고객에게 발생한 문제 중 Microsoft로 인해 문제가 발생했다는 합리적인 이유가 존재하는 문제를 의미합니다.
   * **开发人员辅导**和**咨询服务**在[专业直接](https://azure.microsoft.com/support/plans/prodirect/)和[顶级](https://azure.microsoft.com/support/plans/premier/)支持级别提供。 
     
     如果有顶级支持计划，还可以在[Microsoft Premier online 门户](https://premier.microsoft.com/)上报告 SQL 数据仓库的相关问题。 若要深入了解各种支持计划（包括范围、响应时间、定价等），请参阅[Azure 支持计划](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。 有关 Azure 支持的常见问题，请参阅[azure 支持常见问题](https://azure.microsoft.com/support/faq/)。  
        
     ![기본 블레이드](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![기본 블레이드1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. **문제** 블레이드에 정보를 입력합니다.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > 기본적으로 각 SQL 서버(예: myserver.database.windows.net)의 **DTU 할당량**은 45,000입니다. 이 할당량은 안전을 위한 제한일 뿐입니다. 지원 티켓을 만들고 *할당량* 을 요청 형식으로 선택하여 할당량을 늘릴 수 있습니다. 若要计算 DTU 需求，请将7.5 乘以[DWU](sql-data-warehouse-overview-what-is.md)所需的总大小。 예를 들어 하나의 SQL Server에서 두 개의 DW6000을 호스트하려면 DTU 할당량인 90,000을 요청해야 합니다.  포털의 SQL Server 블레이드에서 현재 DTU 사용량을 볼 수 있습니다. 일시 중지되거나 일시 중지되지 않은 데이터베이스는 모두 DTU 할당량에 포함됩니다. 
   > 
   > 
   
5. **연락처 정보**를 입력합니다.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. **만들기** 를 클릭하여 지원 요청을 제출합니다.

## <a name="monitor-a-support-ticket"></a>지원 티켓 모니터링
提交支持请求后，Azure 支持团队将与你联系。 요청 상태 및 세부 정보를 확인하려면 대시보드에서 **모든 지원 요청**을 클릭합니다.

![상태 확인](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>기타 리소스
你还可以在[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)上或通过[Azure SQL 数据仓库 MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)连接到 SQL 数据仓库社区。

 

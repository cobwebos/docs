---
title: "Azure 门户：配置 SQL 数据库威胁检测 | Microsoft Docs"
description: "使用 Azure 门户配置和管理威胁检测"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 0222847321b82c9572e361cc5e578b779d859792
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-azure-sql-database-threat-detection-in-the-azure-portal"></a>在 Azure 门户中配置和管理 Azure SQL 数据库威胁检测

本主题演示如何设置威胁检测和查找异常数据库活动。 有关 Azure SQL 数据库威胁检测的概述，请参阅[威胁检测概述](sql-database-threat-detection.md)。

## <a name="set-up-threat-detection-for-your-database"></a>为数据库设置威胁检测
1. 启动 Azure 门户 [https://portal.azure.com](https://portal.azure.com)。
2. 导航到你要监视的 SQL 数据库的配置边栏选项卡。 在“设置”边栏选项卡中，选择“审核和威胁检测”。
   
    ![导航窗格][1]
3. 在“审核和威胁检测”配置边栏选项卡中，将审核设置为“打开”，随后会显示威胁检测设置。
   
    ![导航窗格][2]
4. 将威胁检测设置为“打开”。
5. 配置在检测到异常数据库活动时需要接收安全警报的电子邮件列表。
6. 在“审核和威胁检测”边栏选项卡中单击“保存”，以保存新的或更新的审核和威胁检测策略。
   
    ![导航窗格][3]

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>检测到可疑事件时探查异常数据库活动
1. 检测到异常数据库活动时，你将收到电子邮件通知。 <br/>
   电子邮件将提供可疑安全事件的相关信息，包括异常活动的性质、数据库名称、服务器名称和事件时间。 此外，还会提供可能原因和建议操作的相关信息，以帮助你调查和缓解数据库的潜在威胁。<br/>
   
    ![导航窗格][4]
2. 在电子邮件中，单击“**Azure SQL 审核日志**”链接以启动 Azure 门户，并显示发生可疑事件前后的相关审核记录。
   
    ![导航窗格][5]
3. 单击审核记录可以查看有关可疑数据库活动的详细信息，例如 SQL 语句、失败原因和客户端的 IP。
   
    ![导航窗格][6]
4. 在“审核记录”边栏选项卡中，单击“在 Excel 中打开”以打开预配置的 Excel 模板，以导入发生可疑事件前后的审核日志，并运行更深入的分析。<br/>
   **注意：**在 Excel 2010 或更高版本中，需要配置 Power Query 和“快速合并”****设置。
   
    ![导航窗格][7]
5. 若要配置“快速合并”设置 - 请在“POWER QUERY”功能区选项卡中，选择“选项”以显示“选项”对话框。 选择“隐私”部分，然后选择第二个选项 -“忽略隐私级别并潜在地改善性能”：
   
    ![导航窗格][8]
6. 若要加载 SQL 审核日志，请确保设置选项卡中的参数设置正确，选择“数据”功能区，然后单击“全部刷新”按钮。
   
    ![导航窗格][9]
7. 结果将显示在“SQL 审核日志”工作表中，使你能够对检测到的异常活动运行更深入的分析，并缓解应用程序中安全事件造成的影响。

## <a name="next-steps"></a>后续步骤

* 有关 SQL 数据库威胁检测的概述，请参阅[威胁检测](sql-database-threat-detection.md)。
* 有关 SQL 数据库审核的概述，请参阅[数据库审核](sql-database-auditing.md)。


<!--Image references-->
[1]: ./media/sql-database-threat-detection-get-started/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection-get-started/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection-get-started/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection-get-started/4_td_email.png
[5]: ./media/sql-database-threat-detection-get-started/5_td_audit_records.png
[6]: ./media/sql-database-threat-detection-get-started/6_td_audit_record_details.png
[7]: ./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png
[8]: ./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png
[9]: ./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png


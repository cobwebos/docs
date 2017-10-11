---
title: "SQL 数据仓库威胁检测入门"
description: "如何开始使用威胁检测"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: c9073dd9-6c62-4735-8457-dfb9f859c900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: f4a2376fe4fb710d031c35ca7fdbf4c7bb0f3caa
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-threat-detection"></a>威胁检测入门
> [!div class="op_single_selector"]
> * [审核](sql-data-warehouse-auditing-overview.md)
> * [威胁检测](sql-data-warehouse-security-threat-detection.md)
> 
> 

## <a name="overview"></a>概述
威胁检测检测到异常数据库活动，指示数据库的潜在安全威胁。 威胁检测处于预览阶段且适用于 SQL 数据仓库。

威胁检测提供新层的安全性，这使客户能够检测并应对潜在威胁，在发生这些异常活动提供安全警报。 用户可以浏览可疑的事件使用[Azure SQL 数据仓库审核](sql-data-warehouse-auditing-overview.md)以确定它们是否来自尝试访问、 破坏或利用数据仓库中的数据。
威胁检测轻松解决潜在威胁到数据仓库，而无需为安全专家或管理监视系统的高级的安全。

例如，威胁检测检测到某些异常数据库活动，指示潜在 SQL 注入尝试。 SQL 注入是在 Internet 上，用于攻击数据驱动的应用程序的常见 Web 应用程序安全问题之一。 攻击者利用应用程序漏洞将恶意应用程序入口字段，以破坏或修改数据库中的数据的 SQL 语句。

## <a name="set-up-threat-detection-for-your-database"></a>设置你的数据库的威胁检测
1. 启动 Azure 门户在[https://portal.azure.com](https://portal.azure.com)。
2. 导航到你想要监视的 SQL 数据仓库的配置边栏选项卡。 在设置边栏选项卡，选择**审核和威胁检测**。
   
    ![导航窗格][1]
3. 在**审核和威胁检测**配置边栏选项卡打开**ON**审核，这将显示威胁检测设置。
   
    ![导航窗格][2]
4. 打开**ON**威胁检测。
5. 配置要接收在检测到的异常数据仓库活动时的安全警报的电子邮件的列表。
6. 单击**保存**中**审核和威胁检测**配置边栏选项卡，以保存新的或更新的审核与威胁检测策略。
   
    ![导航窗格][3]

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>浏览在检测到可疑事件时的异常数据仓库活动
1. 您将收到电子邮件通知时检测到异常数据库活动。 <br/>
   电子邮件将提供有关可疑安全事件，包括异常活动、 数据库名称、 服务器名称和事件时间的性质的信息。 此外，它将提供有关可能的原因的信息，并建议调查和缓解对数据库的潜在威胁的操作。<br/>
   
    ![导航窗格][4]
2. 在电子邮件中，单击**Azure SQL 审核日志**链接，可将启动 Azure 经典门户，并显示时间的可疑事件前后的相关审核记录。
   
    ![导航窗格][5]
3. 单击审核记录，若要查看更多详细信息上可疑数据库活动，例如 SQL 语句，失败原因和客户端 IP。
   
    ![导航窗格][6]
4. 在审核记录边栏选项卡，单击**在 Excel 中打开**以打开预配置的 excel 模板导入并运行时间的可疑事件前后的审核日志的更深入的分析。<br/>
   **注意：**在 Excel 2010 或更高版本时，Power 查询和**快速合并**设置是必需的
   
    ![导航窗格][7]
5. 若要配置**快速合并**设置-请在**POWER QUERY**功能区选项卡上，将其选中**选项**以显示选项对话框。 选择隐私部分，选择第二个选项-忽略隐私级别并潜在地提高性能:
   
    ![导航窗格][8]
6. 若要加载 SQL 审核日志，请确保正确设置选项卡并选择数据功能区，然后单击全部刷新按钮的设置中的参数。
   
    ![导航窗格][9]
7. 结果显示在**SQL 审核日志**表可使你能够运行更深入的分析已检测到异常活动以及减轻应用程序中的安全事件的影响。

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png

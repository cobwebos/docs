---
title: 排查 Azure Active Directory 活动日志中缺少数据的问题 | Microsoft Docs
description: 为你提供了一种解决方法，解决在 Azure Active Directory 活动日志中缺少数据的问题。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b25c09b140102c0788a939c48f48300242fc6ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285066"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>故障排除：Azure Active Directory 活动日志中缺少数据 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>在 Azure 门户中找不到针对最近操作的审核日志

### <a name="symptoms"></a>症状

我在 Azure 门户中执行了一些操作，本应在`Activity logs > Audit Logs`边栏选项卡中看到这些操作的审核日志，但却找不到。

 ![报告](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>原因

操作不会立即显示在活动日志中。 下表枚举了活动日志的延迟数字。 

| 报表 | &nbsp; | 延迟 (P95) | 延迟 (P99) |
|--------|--------|---------------|---------------|
| 目录审核 | &nbsp; | 2 分钟 | 5 分钟 |
| 登录活动 | &nbsp; | 2 分钟 | 5 分钟 | 

### <a name="resolution"></a>解决方法

等待 15 分钟到 2 小时，再看操作是否显示在日志中。 如果 2 小时后仍未看到日志，请[提交支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，我们会进行调查。

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>在 Azure Active Directory 登录活动日志中找不到最近的用户登录活动

### <a name="symptoms"></a>症状

我最近登录了 Azure 门户，本应在`Activity logs > Sign-ins`边栏选项卡中看到这些操作的登录日志，但却找不到。

 ![报告](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>原因

操作不会立即显示在活动日志中。 下表枚举了活动日志的延迟数字。 

| 报表 | &nbsp; | 延迟 (P95) | 延迟 (P99) |
|--------|--------|---------------|---------------|
| 目录审核 | &nbsp; | 2 分钟 | 5 分钟 |
| 登录活动 | &nbsp; | 2 分钟 | 5 分钟 | 

### <a name="resolution"></a>解决方法

等待 15 分钟到 2 小时，再看操作是否显示在日志中。 如果 2 小时后仍未看到日志，请[提交支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，我们会进行调查。

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>无法在 Azure 门户中查看 30 天以上的报表数据

### <a name="symptoms"></a>症状

无法在 Azure 门户中查看 30 天以上的登录和审核数据。 为什么？ 

 ![报告](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>原因

根据你持有的许可证，Azure Active Directory 操作会按以下持续时间存储活动报告：

| 报表           | &nbsp; |  Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| 目录审核  | &nbsp; |   7 天     | 30 天             | 30 天             |
| 登录活动 | &nbsp; | 不可用。 可以在单个用户配置文件边栏选项卡中访问自己在 7 天内的登录活动 | 30 天 | 30 天             |

有关详细信息，请参阅 [Azure Active Directory 报告保留策略](reference-reports-data-retention.md)。  

### <a name="resolution"></a>解决方法

可以通过两个选项将数据保留 30 天以上。 可以使用 [Azure AD 报告 API](concept-reporting-api.md) 以编程方式检索数据并将其存储在数据库中。 也可将审核日志集成到第三方 SIEM 系统（例如 Splunk 或 SumoLogic）中。

## <a name="next-steps"></a>后续步骤

* [Azure AD 报告保留期](reference-reports-data-retention.md)。
* [Azure Active Directory 报告延迟](reference-reports-latencies.md)。
* [Azure Active Directory 报告常见问题解答](reports-faq.md)。


---
title: "Azure Active Directory 报告延迟 | Microsoft 文档"
description: "在 Azure Active Directory 中显示报告事件所花费的时间"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 346b14f8-d16d-4b07-8211-e6c5eec07062
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e15c82330ac27f58f3faec3a224123cb45c9b28b


---
# <a name="azure-active-directory-report-latencies"></a>Azure Active Directory 报告延迟
*本文档是 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)的一部分。*

| 报表 | 最小值 | 平均值 | 最大值 |
| --- | --- | --- | --- |
| **安全报告** | | | |
| 异常登录活动 |2 小时 |4 小时 |8 小时 |
| 从未知源登录 |2 小时 |4 小时 |8 小时 |
| 多次失败后登录 |2 小时 |4 小时 |8 小时 |
| 从多个地理区域登录 |2 小时 |4 小时 |8 小时 |
| 从具有可疑活动的 IP 地址登录 |2 小时 |4 小时 |8 小时 |
| 从可能受感染的设备登录 |2 小时 |4 小时 |8 小时 |
| 具有异常登录活动的用户 |2 小时 |4 小时 |8 小时 |
| 具有已泄漏凭据的用户 |2 小时 |4 小时 |8 小时 |
| **应用程序报告** | | | |
| 帐户设置活动 |2 小时 |4 小时 |8 小时 |
| 帐户设置错误 |2 小时 |4 小时 |8 小时 |
| 应用程序使用情况 |2 小时 |4 小时 |8 小时 |
| 密码滚动更新状态 |2 小时 |4 小时 |8 小时 |
| **审核与活动报告** | | | |
| 审核报告 |1 分钟 |15 分钟 |30 分钟 |
| 密码重置活动 (Azure AD) |2 小时 |4 小时 |8 小时 |
| 密码重置活动 (Identity Manager) |2 小时 |4 小时 |8 小时 |
| 密码重置注册活动 (Azure AD) |2 小时 |4 小时 |8 小时 |
| 密码重置注册活动 (Identity Manager) |2 小时 |4 小时 |8 小时 |
| 自助服务组活动 (Azure AD) |2 小时 |4 小时 |8 小时 |
| 自助服务组活动 (Identity Manager) |2 小时 |4 小时 |8 小时 |
| **RMS 报告** | | | |
| 最活跃的 RMS 用户 |2 小时 |4 小时 |8 小时 |
| RMS 使用情况 |2 小时 |4 小时 |8 小时 |
| RMS 设备使用情况 |2 小时 |4 小时 |8 小时 |
| 启用了 RMS 的应用程序使用情况 |2 小时 |4 小时 |8 小时 |
| **个人预览版报告** | | | |
| 所有用户登录活动 |2 小时 |4 小时 |8 小时 |




<!--HONumber=Nov16_HO3-->



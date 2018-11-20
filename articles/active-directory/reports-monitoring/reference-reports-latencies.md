---
title: Azure Active Directory 报告延迟 | Microsoft Docs
description: 了解在 Azure 门户中显示报告事件所花费的时间
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e5ceae2959f79c677f5b89c0c3f0a487f92ad1c6
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623164"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory 报告延迟

延迟是 Azure Active Directory (Azure AD) 报告数据在 [Azure 门户](https://portal.azure.com)中显示所需的时间。 本文列出了不同类型报告的预期延迟。 

## <a name="activity-reports"></a>活动报表

有两种类型的活动报告：

- [登录](concept-sign-ins.md) - 提供有关托管应用程序的使用情况和用户登录活动的信息
- [审核日志](concept-audit-logs.md) - 提供有关用户和组、托管应用程序和目录活动的系统活动信息

下表列出了活动报表的延迟信息。 

> [!NOTE]
> **延迟 (95%)** 是指报告 95% 的日志所用的时间，**延迟 (99%)** 是指报告 99% 的日志所用的时间。 
>

| 报表 | 延迟 (95%) |延迟 (99%)|
| :-- | --- | --- | 
| 审核日志 | 2 分钟  | 5 分钟  |
| 登录 | 2 分钟  | 5 分钟 |

## <a name="security-reports"></a>安全报表

有两种类型的安全报告：

- [风险登录](concept-risky-sign-ins.md) - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。 
- [已标记为存在风险的用户](concept-user-at-risk.md) - 风险用户是指可能已泄露的用户帐户。 

下表列出了安全报表的延迟信息。

| 报表 | 最小值 | 平均值 | 最大值 |
| :-- | --- | --- | --- |
| 有风险的用户          | 5 分钟   | 15 分钟  | 2 小时  |
| 有风险的登录         | 5 分钟   | 15 分钟  | 2 小时  |

## <a name="risk-events"></a>风险事件

Azure AD 使用自适应机器学习算法和试探法来检测与用户帐户相关的可疑操作。 检测到的每个可疑操作都存储在称为“风险事件”的记录中。

下表列出了风险事件的延迟信息。

| 报表 | 最小值 | 平均值 | 最大值 |
| :-- | --- | --- | --- |
| 从匿名 IP 地址登录 |5 分钟 |15 分钟 |2 小时 |
| 从不熟悉的位置登录 |5 分钟 |15 分钟 |2 小时 |
| 具有已泄漏凭据的用户 |2 小时 |4 小时 |8 小时 |
| 不可能前往异常位置 |5 分钟 |1 小时	 |8 小时  |
| 从受感染的设备登录 |2 小时 |4 小时 |8 小时  |
| 从具有可疑活动的 IP 地址登录 |2 小时 |4 小时 |8 小时  |


## <a name="next-steps"></a>后续步骤

* [Azure AD 报告概述](overview-reports.md)
* [以编程方式访问 Azure AD 报告](concept-reporting-api.md)
* [Azure Active Directory 风险事件](concept-risk-events.md)

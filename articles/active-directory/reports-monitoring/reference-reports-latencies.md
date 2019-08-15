---
title: Azure Active Directory 报告延迟 | Microsoft Docs
description: 了解在 Azure 门户中显示报告事件所花费的时间
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f57f09f146e542768c83fa034f0b4e65bc6b2ae
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987932"
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

| 报告 | 延迟 (95%) |延迟 (99%)|
| :-- | --- | --- |
| 审核日志 | 2 分钟  | 5 分钟  |
| 登录 | 2 分钟  | 5 分钟 |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>获得高级许可证后多久可看见活动数据？

如果已经拥有免费许可证的活动数据，则可在升级时立即看到这些数据。 升级到高级许可证后，如果没有任何数据，则需要在一到两天后，数据才会显示在报告中。

## <a name="security-reports"></a>安全报表

有两种类型的安全报表：

- [风险登录](concept-risky-sign-ins.md) - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。 
- [已标记为存在风险的用户](concept-user-at-risk.md) - 风险用户是指可能已泄露的用户帐户。 

下表列出了安全报表的延迟信息。

| 报告 | 最低要求 | Average | 最大值 |
| :-- | --- | --- | --- |
| 有风险的用户          | 5 分钟   | 15 分钟  | 2 小时  |
| 风险登录         | 5 分钟   | 15 分钟  | 2 小时  |

## <a name="risk-events"></a>风险事件

Azure AD 使用自适应机器学习算法和试探法来检测与用户帐户相关的可疑操作。 检测到的每个可疑操作都存储在称为**风险事件**的记录中。

下表列出了风险事件的延迟信息。

| 报告 | 最低要求 | Average | 最大值 |
| :-- | --- | --- | --- |
| 从匿名 IP 地址登录 |5 分钟 |15 分钟 |2 小时 |
| 从不熟悉的位置登录 |5 分钟 |15 分钟 |2 小时 |
| 凭据泄漏的用户 |2 小时 |4 小时 |8 小时 |
| 不可能前往这些异常位置 |5 分钟 |1 小时 |8 小时  |
| 从受感染的设备登录 |2 小时 |4 小时 |8 小时  |
| 从具有可疑活动的 IP 地址登录 |2 小时 |4 小时 |8 小时  |


## <a name="next-steps"></a>后续步骤

* [Azure AD 报告概述](overview-reports.md)
* [以编程方式访问 Azure AD 报告](concept-reporting-api.md)
* [Azure Active Directory 风险事件](concept-risk-events.md)

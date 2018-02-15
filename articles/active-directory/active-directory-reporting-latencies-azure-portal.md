---
title: "Azure Active Directory 报告延迟 | Microsoft Docs"
description: "了解在 Azure 门户中显示报告事件所花费的时间"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: markvi;dhanyahk
ms.reviewer: dhanyahk
ms.openlocfilehash: 5ec41817fede495b8262e28d2d614a480d98ff3b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory 报告延迟

通过使用 Azure Active Directory 中的[报表](active-directory-preview-explainer.md)，可以获取确定环境运行状况所需的所有信息。 在 Azure 门户中显示报告数据所花费的时间也被称为延迟。 

本主题列出了 Azure 门户中所有报告类别的相关延迟信息。 


## <a name="activity-reports"></a>活动报表

活动报告有两个方面：

- **登录活动** — 有关托管应用程序的使用和用户登录活动的信息
- **审核日志** - 有关用户和组管理、托管应用程序和目录活动的系统活动信息

下表列出了活动报表的延迟信息。

| 报表 | 最小值 | 平均值 | 备注 |
| :-- | --- | --- | :-- |
| 审核日志 | 30 分钟  | 1 小时	  |在某些实例中，可能需要多达 2 个小时才能显示审核活动数据。|
| 登录 | 15 分钟  | 2 小时 |在某些实例中，可能需要多达 24 个小时才能显示登录活动数据。 这包括来自旧版 Office 应用程序的登录活动数据。 |







## <a name="security-reports"></a>安全报表

安全报告有两个方面：

- **风险登录** - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。 
- **已标记为存在风险的用户** - 风险用户是指可能已泄露的用户帐户。 

下表列出了安全报表的延迟信息。

| 报表 | 最小值 | 平均值 | 最大值 |
| :-- | --- | --- | --- |
| 有风险的用户          | 5 分钟   | 15 分钟  | 2 小时  |
| 有风险的登录         | 5 分钟   | 15 分钟  | 2 小时  |

## <a name="risk-events"></a>风险事件

Azure Active Directory 使用自适应机器学习算法和试探法来检测与用户帐户相关的可疑操作。 每个检测到的可疑操作都存储在称为“风险事件”的记录中。

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

如果想要深入了解 Azure 门户中的活动报表，请参阅：

- [Azure Active Directory 门户中的“登录活动”报表](active-directory-reporting-activity-sign-ins.md)
- [Azure Active Directory 门户中的“审核活动”报表](active-directory-reporting-activity-audit-logs.md)

如果想要深入了解 Azure 门户中的安全报表，请参阅：

- [Azure Active Directory 门户中的“有风险的用户”安全报表](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory 门户中的“有风险的登录”报表](active-directory-reporting-security-risky-sign-ins.md)

如果想要深入了解风险事件，请参阅 [Azure Active Directory 风险事件](active-directory-reporting-risk-events.md)。

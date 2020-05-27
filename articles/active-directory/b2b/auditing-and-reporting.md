---
title: 审核和报告 B2B 协作用户 - Azure AD
description: 可在 Azure Active Directory B2B 协作中配置来宾用户属性
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2f1478391eccaabcc4dbcd150b54376494d3f56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587521"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>审核和报告 B2B 协作用户
对于来宾用户，具有与成员用户类似的审核功能。 

## <a name="access-reviews"></a>访问评审
可以使用访问评审来定期验证来宾用户是否仍需要访问资源。 “访问评审”功能在 Azure Active Directory 中的“外部标识” > “访问评审”下提供。 也可以在 Azure 门户上的“所有服务”中搜索“访问评审”。 若要了解如何使用访问评审，请参阅[使用 Azure AD 访问评审管理来宾访问权限](../governance/manage-guest-access-with-access-reviews.md)。

## <a name="audit-logs"></a>审核日志

Azure AD 审核日志提供系统和用户活动的记录，包括来宾用户发起的活动。 若要访问审核日志，请在“Azure Active Directory”中的“监视”下，选择“审核日志”。 下面是受邀人 Sam Oogle 的邀请和兑换历史记录的示例：

![显示审核日志输出示例的屏幕截图](./media/auditing-and-reporting/audit-log.png)

用户可以深入了解上述每个事件，以获取详细信息。 例如，让我们看一下验收详细信息。

![显示活动详细信息输出示例的屏幕截图](./media/auditing-and-reporting/activity-details.png)

还可以从 Azure AD 导出这些日志，并使用所选报表工具获取自定义报表。

### <a name="next-steps"></a>后续步骤

- [B2B 协作用户属性](user-properties.md)


---
title: 审核和报告 B2B 协作用户的 Azure Active Directory |Microsoft Docs
description: 可在 Azure Active Directory B2B 协作中配置来宾用户属性
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5763a7e5f122702ddaf86246fbfbd18326878146
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294189"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>审核和报告 B2B 协作用户
对于来宾用户，具有与成员用户类似的审核功能。 

## <a name="access-reviews"></a>访问评审
可以使用访问评审来定期验证来宾用户是否仍需要访问资源。 “访问评审”功能在“Azure Active Directory”中的“管理” > “组织关系”下提供。 （还可以从 Azure 门户中的“所有服务”搜索“访问评审”。）若要了解如何使用访问评审，请参阅[使用 Azure AD 访问评审管理来宾访问权限](../governance/manage-guest-access-with-access-reviews.md)。

## <a name="audit-logs"></a>审核日志

Azure AD 审核日志提供系统和用户活动的记录，包括来宾用户发起的活动。 若要访问审核日志，请在“Azure Active Directory”中的“监视”下，选择“审核日志”。 下面是受邀人 Sam Oogle 的邀请和兑换历史记录的示例：

![屏幕截图显示和示例中的审核日志输出](./media/auditing-and-reporting/audit-log.png)

用户可以深入了解上述每个事件，以获取详细信息。 例如，让我们看一下验收详细信息。

![屏幕截图显示和活动的详细信息输出的示例](./media/auditing-and-reporting/activity-details.png)

还可以从 Azure AD 导出这些日志，并使用所选报表工具获取自定义报表。

### <a name="next-steps"></a>后续步骤

- [B2B 协作用户属性](user-properties.md)


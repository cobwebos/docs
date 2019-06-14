---
title: 批准或拒绝访问在 Azure AD 授权管理 （预览版）-Azure Active Directory 中的请求
description: 了解如何使用我访问门户来批准或拒绝请求向 Azure Active Directory 权利管理 （预览版） 中访问包。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541520"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>批准或拒绝访问请求在 Azure AD 授权管理 （预览版）

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

与 Azure AD 授权管理，可以将策略配置为需要审批才可访问包，并选择一个或多个审批者。 本文介绍如何指定审批者可以批准或拒绝访问包的请求。

## <a name="open-request"></a>打开请求

批准或拒绝访问请求的第一步是查找和打开挂起的审批访问请求。 有两种方法打开访问请求。

**必备角色：** 审批者

1. 查找从 Microsoft Azure，要求您批准或拒绝的请求的电子邮件。 下面是示例电子邮件：

    ![批准请求以访问包电子邮件](./media/entitlement-management-shared/email-approve-request.png)

1. 单击**批准或拒绝请求**链接以打开访问请求。

1. 登录到我访问门户。

如果没有电子邮件，您可以通过执行以下步骤挂起你的审批找到访问请求。

1. 登录到我访问门户[ https://myaccess.microsoft.com ](https://myaccess.microsoft.com)。

1. 在左侧菜单中，单击**审批**若要查看挂起的审批访问请求的列表。

1. 上**挂起**选项卡上，查找的请求。

## <a name="approve-or-deny-request"></a>批准或拒绝请求

打开挂起的审批访问请求后，可以看到将帮助你做出批准或拒绝决策的详细信息。

**必备角色：** 审批者

1. 单击“查看”链接打开“访问请求”窗格。 

1. 单击**详细信息**若要查看有关访问请求的详细信息。

    详细信息包括用户的名称、 组织、 访问开始和结束日期，如果提供，业务理由，以及时已提交请求，请求将过期。

1. 单击**批准**或**拒绝**。

1. 如有必要，输入原因。

    ![我的访问权限门户 - 访问请求](./media/entitlement-management-shared/my-access-approve-request.png)

1. 单击“提交”以提交所做的决定。 

    如果使用多个审批者配置策略，则只有一个审批者将需要决定如何对挂起的审批。 审批者提交其对访问请求的决策后，请求完成，并且不再可用于其他审批者批准或拒绝该请求。 其他审批者可以看到我访问门户的决策制定者的请求决定。 在此期间，支持仅单阶段审批。

    如果已配置的审批者都可以批准或拒绝访问请求，请求将在配置的请求持续时间后过期。 用户收到通知其访问请求已过期，他们需要重新提交访问请求。

## <a name="next-steps"></a>后续步骤

- [请求的访问权限访问包](entitlement-management-request-access.md)
- [请求过程和电子邮件通知](entitlement-management-process.md)

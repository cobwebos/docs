---
title: 请求对 Azure AD 授权管理 （预览版）-Azure Active Directory 中的访问包
description: 了解如何使用我访问门户来请求访问 Azure Active Directory 权利管理 （预览版） 中的访问包。
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
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541550"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>请求对 Azure AD 授权管理 （预览版） 中的访问包

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="sign-in-to-the-my-access-portal"></a>登录到我访问门户

第一步是登录到我访问门户，可以请求对访问包访问权限。

**必备角色：** 请求者

1. 你正在使用的项目或企业管理器从查找电子邮件或一条消息。 电子邮件应包含你将需要访问访问包的链接。 该链接将开始：

    `https://myaccess.microsoft.com`

1. 打开该链接。

1. 登录到我访问门户。

    请确保使用你的组织帐户。 如果您不确定，请咨询你的项目或业务经理。

## <a name="request-an-access-package"></a>请求访问包

一旦我访问门户中找到访问包，你可以提交请求。

**必备角色：** 请求者

1. 单击复选标记以选择访问包。

    ![我的访问权限门户 - 访问包](./media/entitlement-management-shared/my-access-access-packages.png)

1. 单击“请求访问”打开“请求访问”面板。 

1. 如果**业务理由**显示框中，键入需要进行访问的理由。

1. 如果**请求指定的时间段？** 的已启用，请选择**是**或**否**。

1. 如有必要，指定开始日期和结束日期。

    ![我的访问权限门户 - 请求访问](./media/entitlement-management-shared/my-access-request-access.png)

1. 完成后，单击**提交**提交请求。

1. 单击**请求历史记录**若要查看你的请求和状态的列表。

    如果访问包需要审批，请求现在都处于挂起的审批状态。

## <a name="cancel-a-request"></a>取消请求

如果提交访问请求并且该请求仍在**挂起的审批**状态中时，您可以取消请求。

**必备角色：** 请求者

1. 在我访问门户中，在左侧，单击**请求历史记录**若要查看你的请求和状态的列表。

1. 单击**视图**你想要取消请求的链接。

1. 如果请求仍处于**挂起的审批**状态，则可以单击**取消请求**取消请求。

    ![我访问门户-取消请求](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. 单击**请求历史记录**以确认请求已取消。

## <a name="select-a-policy"></a>选择策略

如果你要请求对具有多个适用策略的访问包的访问，您可能需要选择一个策略。 例如，访问包管理器可访问包使用配置两个策略的两个内部员工组。 第一个策略可能 60 天内允许访问，需要批准。 第二个策略可能允许访问 2 天，不需要批准。 如果遇到这种情况下，必须选择你想要使用的策略。

**必备角色：** 请求者

## <a name="next-steps"></a>后续步骤

- [批准或拒绝访问请求](entitlement-management-request-approve.md)
- [请求过程和电子邮件通知](entitlement-management-process.md)

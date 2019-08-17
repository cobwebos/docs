---
title: 请求访问 Azure AD 权限管理 (预览版) 中的访问包-Azure Active Directory
description: 了解如何使用 "我的访问门户" 请求对 Azure Active Directory 授权管理 (预览版) 中访问包的访问权限。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b5be74b0734a27b02030e2c6995b43ef0c02162
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562183"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>请求访问 Azure AD 授权管理 (预览版) 中的访问包

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

利用 Azure AD 的权利管理, 访问包可以一次性设置资源和策略, 并在访问包的生命周期内自动管理访问权限。 

访问包管理器可以配置策略, 以要求用户有权访问程序包。 需要访问访问包的用户可以提交请求以获取访问权限。 本文介绍如何提交访问请求。

## <a name="sign-in-to-the-my-access-portal"></a>登录到 "我的 Access" 门户

第一步是登录到 "我的访问门户", 您可以在其中请求访问包的访问权限。

**必备角色：** 请求者

1. 查找使用的项目或业务经理发来的电子邮件或消息。 电子邮件应该包含指向需要访问的访问包的链接。 该链接将以开头:

    `https://myaccess.microsoft.com`

1. 打开链接。

1. 登录到 "我的访问门户"。

    请确保使用组织 (工作或学校) 帐户。 如果你不确定, 请与你的项目或业务经理联系。

## <a name="request-an-access-package"></a>请求访问包

在 "我的访问" 门户中找到访问包后, 可以提交请求。

**必备角色：** 请求者

1. 在列表中查找访问包。  如果需要, 可以通过键入搜索字符串, 然后选择 "**名称**"、"**目录**" 或 "**资源**" 筛选器进行搜索。

    ![我的访问门户-资源搜索](./media/entitlement-management-request-access/elm-myaccess-resource-search.png)
1. 单击复选标记以选择访问包。

    ![我的访问权限门户 - 访问包](./media/entitlement-management-shared/my-access-access-packages.png)

1. 单击“请求访问”打开“请求访问”面板。

1. 如果显示 "**业务理由**" 框, 请键入需要访问的理由。

1. 如果启用了 "**特定时间段的请求？** ", 请选择 **"是" 或 "** **否**"。

1. 如有必要, 请指定开始日期和结束日期。

    ![我的访问权限门户 - 请求访问](./media/entitlement-management-shared/my-access-request-access.png)

1. 完成后, 单击 "**提交**" 以提交请求。

1. 单击 "**请求历史记录**" 以查看请求和状态的列表。

    如果访问包需要批准, 则请求现在处于待定审批状态。

## <a name="cancel-a-request"></a>取消请求

如果提交了访问请求, 但该请求仍处于 "**等待审批**" 状态, 则可以取消该请求。

**必备角色：** 请求者

1. 在 "我的访问" 门户的左侧, 单击 "**请求历史记录**" 以查看请求和状态的列表。

1. 单击要取消的请求的 "**查看**" 链接。

1. 如果请求仍处于**待定审批**状态, 可以单击 "**取消请求**" 以取消请求。

    ![我的访问门户-取消请求](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. 单击 "**请求历史记录**" 以确认请求已取消。

## <a name="select-a-policy"></a>选择策略

如果你正在请求访问具有多个应用策略的访问包, 则系统可能会要求你选择一种策略。 例如, 访问包管理器可能会配置访问包, 其中包含两组内部员工的两个策略。 第一个策略可能允许访问60天, 需要批准。 第二个策略可能允许访问2天, 无需批准。 如果遇到这种情况, 则必须选择要使用的策略。

**必备角色：** 请求者

## <a name="next-steps"></a>后续步骤

- [批准或拒绝访问请求](entitlement-management-request-approve.md)
- [请求进程和电子邮件通知](entitlement-management-process.md)

---
title: 请求访问包 - Azure AD 授权管理
description: 了解如何使用"我的访问"门户请求访问 Azure 活动目录授权管理中的访问包。
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261744"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>请求访问 Azure AD 授权管理中的访问包

使用 Azure AD 授权管理，访问包支持一次性设置资源和策略，从而自动管理访问包生命周期内的访问。 

访问包管理器可以配置策略，以要求用户获得访问访问包的批准。 需要访问访问包的用户可以提交请求来获得访问权限。 本文介绍如何提交访问请求。

## <a name="sign-in-to-the-my-access-portal"></a>登录到“我的访问权限”门户

第一步是登录到“我的访问权限”门户，然后即可在其中请求访问某个访问包。

**先决条件角色：** 请求

1. 查找与你合作的项目经理或业务经理发出的电子邮件或消息。 该电子邮件应该包含需访问的访问包的链接。 链接以 开头`myaccess`，包括目录提示，以访问包 ID 结尾。  （对于美国政府来说，域名可能是`https://myaccess.microsoft.us`。
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 打开链接。

1. 登录到“我的访问权限”门户。

    请确保使用组织（工作或学校）帐户。 如果不确定，请咨询项目或业务经理。

## <a name="request-an-access-package"></a>请求访问包

在“我的访问权限”门户中找到访问包以后，即可提交请求。

**先决条件角色：** 请求

1. 在列表中查找访问包。  如有必要，可以键入搜索字符串，然后选择“名称”****、“目录”**** 或“资源”**** 筛选器进行搜索。

    ![“我的访问权限”门户 - 资源搜索](./media/entitlement-management-request-access/my-access-resource-search.png)

1. 单击复选标记，选中该访问包。

1. 单击“请求访问”打开“请求访问”面板。****

    ![我的访问权限门户 - 访问包](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. 如果显示“业务理由”框，请键入需要访问的理由。****

1. 如果“请求在特定的时间段访问”已启用，请选择“是”或“否”。************

1. 必要时指定开始日期和结束日期。

    ![我的访问权限门户 - 请求访问](./media/entitlement-management-shared/my-access-request-access.png)

1. 完成后，单击“提交”以提交请求。****

1. 单击“请求历史记录”，查看请求和状态的列表。****

    如果访问包需要审批，则请求现在为等待审批状态。

### <a name="select-a-policy"></a>选择策略

如果请求访问具有多个应用策略的访问包，系统可能会要求您选择策略。 例如，访问包管理员可能会为两组内部员工配置一个使用两个策略的访问包。 第一个策略可能允许访问 60 天，但需要批准。 第二个策略可能允许访问 2 天，但不需要批准。 如果遇到这种情况，则必须选择要使用的策略。

![我的访问门户 - 请求访问 - 多个策略](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>重新提交请求

当您请求访问包时，如果审批者不及时响应，您的请求可能会被拒绝，或者您的请求可能会过期。 如果您需要访问权限，可以重试并重新提交请求。 以下过程说明如何重新提交访问请求：

**先决条件角色：** 请求

1. 登录到 **"我的访问"** 门户。

1. 单击从左侧导航菜单**请求历史记录**。

1. 查找您要为其重新提交请求的访问包。

1. 单击复选标记以选择访问包。

1. 单击所选访问包右侧的蓝色**视图**链接。
    
    ![选择访问包和查看链接](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    右侧将打开一个窗格，其中将打开访问包的请求历史记录。
    
    ![选择重新提交按钮](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. 单击窗格底部的 **"重新提交**"按钮。

## <a name="cancel-a-request"></a>取消请求

如果在提交访问请求后请求仍为“待审批”状态，可以取消请求。****

**先决条件角色：** 请求

1. 在“我的访问权限”门户左侧，单击“请求历史记录”，查看请求和状态的列表。****

1. 单击要取消的请求的“查看”链接。****

1. 如果请求仍为“待审批”状态，则可单击“取消请求”，将请求取消。********

    ![“我的访问权限”门户 - 取消请求](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. 单击“请求历史记录”，确认请求已取消。****

## <a name="next-steps"></a>后续步骤

- [批准或拒绝访问请求](entitlement-management-request-approve.md)
- [请求过程和电子邮件通知](entitlement-management-process.md)

---
title: Azure AD 权限管理 (预览版) 疑难解答-Azure Active Directory
description: 了解你应检查哪些项以帮助你解决 Azure Active Directory 授权管理 (预览版) 问题。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ec27c75ff5ba9164b44b0524f90a4e28ab20f1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488978"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Azure AD 授权管理 (预览版) 疑难解答

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍你应检查哪些项目以帮助你解决 Azure Active Directory (Azure AD) 权利管理。

## <a name="checklist-for-entitlement-management-administration"></a>用于授权管理管理的清单

* 如果在配置授权管理时收到 "访问被拒绝" 消息, 并且你是全局管理员, 请确保目录具有[Azure AD Premium P2 (或 EMS E5) 许可证](entitlement-management-overview.md#license-requirements)。  
* 如果在创建或查看访问包时收到 "访问被拒绝" 消息, 并且您是目录创建者组的成员, 则必须在创建第一个访问包之前创建目录。

## <a name="checklist-for-adding-a-resource"></a>用于添加资源的清单

* 要使应用程序成为访问包中的资源, 它必须具有至少一个可分配的资源角色。 角色由应用程序本身定义, 并在 Azure AD 中进行管理。 请注意, Azure 门户可能还会显示不能选择为应用程序的服务的服务主体。  特别是, **Exchange online**和**SharePoint online**是服务, 而不是在目录中具有资源角色的应用程序, 因此它们不能包含在访问包中。  而是使用基于组的许可为需要访问这些服务的用户建立适当的许可证。

* 要使组成为访问包中的资源, 它必须能够在 Azure AD 中进行修改。  无法将源自本地 Active Directory 的组分配为资源, 因为在 Azure AD 中无法更改其所有者或成员属性。  

* 不能将 SharePoint Online 文档库和单独的文档添加为资源。  相反, 请创建一个 Azure AD 安全组, 在 access 包中包含该组和站点角色, 然后在 SharePoint Online 中使用该组来控制对文档库或文档的访问。

* 如果已将用户分配到你想要使用访问包管理的资源, 请确保已将用户分配到具有相应策略的访问包。 例如, 你可能想要在访问包中包含组中已有用户的组。 如果组中的用户需要持续访问, 则他们必须对访问包具有适当的策略, 以便它们不会失去对组的访问权限。 你可以通过要求用户请求包含该资源的访问包, 或者通过将其直接分配到访问包来分配访问包。 有关详细信息, 请参阅[编辑和管理现有访问包](entitlement-management-access-package-edit.md)。

## <a name="checklist-for-providing-external-users-access"></a>为外部用户提供访问权限的清单

* 如果存在 B2B[允许列表](../b2b/allow-deny-list.md), 则不允许其目录的用户将无法请求访问。

* 确保没有任何条件性[访问策略](../conditional-access/require-managed-devices.md)会阻止外部用户请求访问, 或者无法使用访问包中的应用程序。

## <a name="checklist-for-request-issues"></a>请求问题清单

* 当用户要请求访问包的访问权限时, 请确保他们使用访问包的 "**我的访问门户" 链接**。 有关详细信息, 请参阅[复制我的访问门户链接](entitlement-management-access-package-edit.md#copy-my-access-portal-link)。

* 当用户登录到 "我的访问门户" 以请求访问包时, 请确保他们使用其组织帐户进行身份验证。 组织帐户可以是资源目录中的帐户, 也可以是在访问包的其中一个策略中包含的目录中。 如果用户的帐户不是组织帐户, 或者该目录未包括在策略中, 则用户将不会看到访问包。 有关详细信息, 请参阅[请求访问访问包](entitlement-management-request-access.md)。

* 如果阻止用户登录到资源目录, 则他们将无法在 "我的访问门户" 中请求访问。 在用户可以请求访问之前, 必须从用户的配置文件中删除登录块。 若要删除登录块, 请在 Azure 门户中单击 " **Azure Active Directory**", 单击 "**用户**", 单击用户, 然后单击 "**配置文件**"。 编辑**设置**部分, 并将 **"阻止登录**" 更改为 "**否**"。 有关详细信息, 请参阅[使用 Azure Active Directory 添加或更新用户的配置文件信息](../fundamentals/active-directory-users-profile-azure-portal.md)。  你还可以检查用户是否因[Identity Protection 策略](../identity-protection/howto-unblock-user.md)而被阻止。

* 在 "我的 Access" 门户中, 如果某个用户既是请求者又是审批者, 他们将不会在 "**审批**" 页上看到他们对访问包的请求。 此行为是有意的-用户无法批准自己的请求。 确保他们请求的访问包在策略上配置了其他审批者。 有关详细信息, 请参阅[编辑现有策略](entitlement-management-access-package-edit.md#edit-an-existing-policy)。

* 如果之前未在目录中登录的新外部用户接收到包含 SharePoint Online 站点的访问包, 则在 SharePoint Online 中预配其帐户之前, 其访问包将显示为 "未完全传递"。

## <a name="next-steps"></a>后续步骤

- [查看用户在权利管理中获取访问权限的报告](entitlement-management-reports.md)

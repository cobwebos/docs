---
title: 故障排除授权管理 - Azure AD
description: 了解应检查的一些项目，以帮助您解决 Azure 活动目录授权管理。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c38e1a61827da547bb39a699a0e92043e63466c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128478"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>排查 Azure AD 权利管理的问题

本文介绍应检查哪些项目来帮助排查 Azure Active Directory (Azure AD) 权利管理的问题。

## <a name="administration"></a>管理

* 如果在配置授权管理时收到拒绝访问的消息，并且您是全局管理员，请确保目录具有 Azure AD 高级[P2（或 EMS E5）许可证](entitlement-management-overview.md#license-requirements)。

* 如果在创建或查看访问包时收到拒绝访问的消息，并且您是目录创建者组的成员，则必须在创建第一个访问包之前[创建目录](entitlement-management-catalog-create.md)。

## <a name="resources"></a>资源

* 应用程序的角色由应用程序自身定义，并在 Azure AD 中进行管理。 如果应用程序没有任何资源角色，则授权管理会将用户分配给**默认访问**角色。

    请注意，Azure 门户可能还会显示不能选作应用程序的服务的服务主体。  特别是，**联机交换**和**SharePoint Online**是服务，而不是目录中具有资源角色的应用程序，因此它们不能包含在访问包中。  相反，使用基于组的许可为需要访问这些服务的用户建立适当的许可。

* 要使组成为访问包中的资源，该组必须能够在 Azure AD 中可修改。  源自本地 Active Directory 的组无法分配为资源，因为无法在 Azure AD 中更改其所有者或成员属性。   也无法在 Azure AD 中修改作为通讯组在 Exchange Online 中创建的组。 

* 无法将 SharePoint 联机文档库和单个文档添加为资源。  相反，创建[Azure AD 安全组](../fundamentals/active-directory-groups-create-azure-portal.md)，在访问包中包括该组和站点角色，在 SharePoint Online 中使用该组来控制对文档库或文档的访问。

* 如果有已分配给要使用访问包管理的资源的用户，请确保已使用适当的策略将这些用户分配给访问包。 例如，你可能希望在访问包中包含一个组，该组中已有用户。 如果该组中的这些用户需要继续访问，则他们必须具有访问包的适当策略，以便不会失去对组的访问权限。 你可以通过请求用户请求包含该资源的访问包，或直接将其分配给访问包来分配访问包。 有关详细信息，请参阅[更改访问包的请求和审批设置](entitlement-management-access-package-request-policy.md)。

* 删除团队成员时，也会将其从 Office 365 组中删除。 从团队的聊天功能中删除可能会延迟。 有关详细信息，请参阅[组成员身份](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)。

* 确保你的目录未在多地域进行配置。 权利管理当前不支持 SharePoint Online 的多地域位置。 SharePoint Online 站点必须位于默认地理位置，才能由权利管理进行管理。 有关详细信息，请参阅[OneDrive 中的多地理功能和联机共享点](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365)。

## <a name="access-packages"></a>访问包

* 如果您尝试删除访问包或策略，并看到一条错误消息，指出存在活动分配，如果您没有看到任何具有分配的用户，请检查最近删除的任何用户是否仍有分配。 在用户删除后的 30 天时段内，可以还原用户帐户。   

## <a name="external-users"></a>外部用户

* 当外部用户想要请求访问包时，请确保他们使用访问包的 **"我的访问"门户链接**。 有关详细信息，请参阅[共享链接以请求访问包](entitlement-management-access-package-settings.md)。 如果外部用户只是访问**myaccess.microsoft.com**并且不使用完整的"我的访问"门户链接，那么他们将在自己的组织中看到可供他们访问的程序包，而不是您的组织中。

* 如果外部用户无法请求访问包或无法访问资源，请确保[检查外部用户的设置](entitlement-management-external-users.md#settings-for-external-users)。

* 如果以前未在目录中登录的新外部用户接收到包含 SharePoint Online 站点的访问包，则其访问包将显示为未完全传递，直到其帐户在 SharePoint Online 中设置。 有关共享设置的详细信息，请参阅查看[SharePoint 联机外部共享设置](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)。

## <a name="requests"></a>Requests

* 当用户要请求访问包时，请确保他们使用访问包的 **"我的访问"门户链接**。 有关详细信息，请参阅[共享链接以请求访问包](entitlement-management-access-package-settings.md)。

* 如果在浏览器设置为“专用”或“匿名”模式时打开“我的访问权限”门户，这可能会与登录行为产生冲突。 建议在访问“我的访问权限”门户时不要在浏览器的“专用”或“匿名”模式下使用。

* 当尚未在你的目录中的用户登录到“我的访问权限”门户以请求访问包时，请确保他们使用其组织帐户进行身份验证。 组织帐户可以是资源目录中的帐户，也可以是其他目录中的帐户，该目录包含在访问包的其中一个策略中。 如果用户的帐户不是组织帐户，或者策略中不包含他们验证的目录，那么用户将看不到访问包。 有关详细信息，请参阅[请求访问包](entitlement-management-request-access.md)。

* 如果阻止用户登录到资源目录，则他们将无法在“我的访问权限”门户中请求访问。 必须从用户配置文件中删除登录块，用户才可以请求访问。 要删除登录块，请在 Azure 门户中单击**Azure 活动目录**，单击 **"用户**"，单击"用户"，然后单击 **"配置文件**"。 编辑 **"设置"** 部分并将 **"阻止登录"** 更改为 **"否**"。 有关详细信息，请参阅使用[Azure 活动目录添加或更新用户的配置文件信息](../fundamentals/active-directory-users-profile-azure-portal.md)。  您还可以检查用户是否由于[身份保护策略](../identity-protection/howto-unblock-user.md)而被阻止。

* 在"我的访问"门户中，如果用户既是请求者和审批者，他们将不会在 **"审批"** 页上看到他们的访问包请求。 此行为是有意行为 - 用户无法批准自己的请求。 确保他们请求的访问包在策略上配置了其他审批者。 有关详细信息，请参阅[更改访问包的请求和审批设置](entitlement-management-access-package-request-policy.md)。

### <a name="view-a-requests-delivery-errors"></a>查看请求的传递错误

**先决条件角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 单击“请求”。****

1. 选择要查看的请求。

    如果请求有任何传递错误，则请求状态将**取消传递**或**部分传递**。

    如果发生了任何传递错误，请求详细信息窗格中会显示传递错误计数。

1. 单击计数可查看该请求的所有传递错误。

### <a name="reprocess-a-request"></a>重新处理请求

如果请求遇到错误，您可以重新处理请求以重试它。 您只能重新处理状态**为"交付失败**"或**部分送达**且完成日期少于一周的请求。

**先决条件角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 单击“请求”。****

1. 单击要重新处理的请求。

1. 在请求详细信息窗格中，单击"**重新处理请求**"。

    ![重新处理失败的请求](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>取消挂起的请求

您只能取消尚未送达或其传递失败的请求。

**先决条件角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 单击“请求”。****

1. 单击要取消的请求。

1. 在请求详细信息窗格中，单击“取消请求”。****

## <a name="multiple-policies"></a>多个策略

* 授权管理遵循最低特权最佳做法。 当用户请求访问具有多个应用策略的访问包时，授权管理包括逻辑，以帮助确保对泛型策略设置更严格或更具体的策略优先级。 如果策略是通用的，则授权管理可能不会向请求者显示策略，或者可能自动选择更严格的策略。

* 例如，考虑一个访问包，其中两个策略都适用于请求者，其中两个策略都适用于请求者。 第一个策略适用于包含请求者的特定用户。 第二个策略适用于请求者是该目录的所有用户。 在这种情况下，自动为请求者选择第一个策略，因为它更加严格。 请求者没有选择第二个策略的选项。

* 应用多个策略时，自动选择的策略或向请求者显示的策略基于以下优先级逻辑：

    | 政策优先级 | 范围 |
    | --- | --- |
    | P1 | 目录中的特定用户和组或特定连接的组织 |
    | P2 | 目录中的所有成员（不包括客人） |
    | P3 | 目录中的所有用户（包括来宾）或特定连接的组织 |
    | P4 | 所有连接的组织或所有用户（所有连接的组织 + 任何新的外部用户） |
    
    如果任何策略属于较高优先级类别，则忽略优先级较低的类别。 有关如何向请求者显示具有相同优先级的多个策略的示例，请参阅[选择策略](entitlement-management-request-access.md#select-a-policy)。

## <a name="next-steps"></a>后续步骤

- [管控外部用户的访问权限](entitlement-management-external-users.md)
- [查看有关用户如何在权利管理中获取访问权限的报告](entitlement-management-reports.md)

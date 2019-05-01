---
title: Azure AD 授权管理 （预览版）-Azure Active Directory 故障排除
description: 了解有关的某些项应检查以帮助您解决 Azure Active Directory 权利管理 （预览版）。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/25/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ca7955186d6c6fb1dff2acd6a2d5badd1cedaef
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541505"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>排查 Azure AD 授权管理 （预览版）

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 授权管理当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本指南介绍了一些应检查以帮助您解决 Azure Active Directory (Azure AD) 授权管理的项。

## <a name="checklist-for-entitlement-management-administration"></a>授权管理管理的清单

* 如果收到访问被拒绝的消息时配置授权管理，并且你是全局管理员，请确保你的目录具有[Azure AD Premium P2 （或 EMS E5） 许可证](entitlement-management-overview.md#prerequisites)。  
* 如果收到访问被拒绝消息时创建或查看访问包，并且你是目录的创建者组的成员，则必须创建之前创建第一个访问包目录。

## <a name="checklist-for-adding-a-resource"></a>用于添加资源的清单

* 如果有已分配给你想要具有访问包管理的资源的用户，请确保将用户分配到相应的策略具有访问包。 例如，你可能想要在组中已有的用户访问包中包含一个组。 如果用户组需要持续访问权限，它们必须具有访问包的相应策略，以便他们看不到组失去访问权限。 通过要求请求包含该资源的访问包的用户，或者直接将它们分配给访问包，可以分配访问包。 有关详细信息，请参阅[编辑和管理现有访问包](entitlement-management-access-package-edit.md)。

## <a name="checklist-for-request-issues"></a>请求问题的清单

* 当用户想要请求访问包的访问权限时，请务必使用它们**我访问门户链接**访问包。 有关详细信息，请参阅[复制我访问门户链接](entitlement-management-access-package-edit.md#copy-my-access-portal-link)。

* 当用户登录到我访问门户来请求访问包时，请确保它们使用其组织帐户身份验证。 组织帐户可以是任一帐户中的资源目录，或包含在其中一个访问包的策略的目录中。 如果用户的帐户不是组织帐户，或该目录不包含在策略中，用户将不会看到访问包。 有关详细信息，请参阅[请求的访问权限访问包](entitlement-management-request-access.md)。

* 如果将阻止用户在登录到的资源目录，它们将不能请求我访问门户中的访问权限。 用户可以请求访问权限之前，必须从用户的配置文件中删除登录块。 若要删除的登录块中，在 Azure 门户中，单击**Azure Active Directory**，单击**用户**，单击用户，然后单击**配置文件**。 编辑**设置**部分，并更改**阻止登录**到**否**。 有关详细信息，请参阅[添加或更新使用 Azure Active Directory 用户的个人资料信息](../fundamentals/active-directory-users-profile-azure-portal.md)

* 在我访问门户中，如果用户是请求者和审批者，他们将看不访问包其请求上**审批**页。 此行为是有意而为-用户不能批准他们自己的请求。 请确保已配置的策略的其他审批者发送请求访问包。 有关详细信息，请参阅[编辑现有策略](entitlement-management-access-package-edit.md#edit-an-existing-policy)。

* 如果新的外部用户，不以前签名在目录中，收到访问程序包，包括 SharePoint Online 站点，如之前在 SharePoint Online 中预配其帐户未完全传递，将显示其访问包。

## <a name="next-steps"></a>后续步骤

- [查看报表的用户如何获取的访问权利管理中](entitlement-management-reports.md)

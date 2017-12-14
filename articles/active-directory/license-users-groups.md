---
title: "在 Azure Active Directory 中向用户发放许可 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 中向自己及用户发放许可。"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: mtillman
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.openlocfilehash: bc210b83a9eeb947a15b60548e43096bd9e11c45
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-license-users-in-azure-active-directory"></a>快速入门：在 Azure Active Directory 中向用户发放许可
基于许可证的 Azure AD 服务的工作原理是在 Azure AD 租户中激活 Azure Active Directory (Azure AD) 订阅。 激活订阅之后，服务功能由 Azure AD 管理员管理，并由获得许可的用户使用。 当购买企业移动性 + 安全性、Azure AD Premium 或 Azure AD Basic 时，租户会随着订阅更新，包括其有效期和预付许可证。 可以在 Azure 门户中的 Azure Active Directory 下打开“许可证”磁贴查看订阅信息，包括已分配或可用的许可证数目。 “许可证”边栏选项卡也是管理许可证分配的最佳场所。

只要获取了订阅，就能配置付费功能，但是，要使用 Azure AD 付费功能，仍需向用户分发许可证。 必须向应有权访问 Azure AD 付费功能或者通过 Azure AD 付费功能管理的用户分配许可证。 许可证分配是用户与购买的服务（例如 Azure AD Premium、Basic 或企业移动性 + 安全性）之间的映射。

可使用[基于组的许可证分配](active-directory-licensing-whatis-azure-portal.md)设置规则，如下所示：
* 目录中的所有用户会自动获取许可证
* 具有相应职务的每个人都会获得许可证
* 可通过使用[自助服务组](active-directory-accessmanagement-self-service-group-management.md)，将决策权委派给组织中的其他管理者

> [!TIP]
> 有关将许可证分配到组的详细介绍（包括高级方案和 Office 365 许可方案），请参阅[在 Azure Active Directory 中按组成员身份为用户分配许可证](active-directory-licensing-group-assignment-azure-portal.md)。

## <a name="assign-licenses-to-users-and-groups"></a>向用户和组分配许可证
要使用激活的订阅，首先应向自己分配许可证，并刷新浏览器，确保订阅中包含所期望的所有功能。 下一步是向需要访问 Azure AD 付费功能的用户分配许可证。 分配许可证的简单办法是将许可证分配给用户组，而不是个人。 将许可证分配给组时，将向所有组成员分配许可证。 如果在组中添加或从组中删除用户，则会相应地自动为其分配许可证，或删除其许可证。 

> [!NOTE]
> 某些 Microsoft 服务不能在所有位置使用。 在将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。 可在 Azure 门户中的“用户”&gt;“配置文件”&gt;下的“设置”中设置此属性。 使用组许可证分配时，未指定使用位置的任何用户将继承该目录的位置。

要分配许可证，可在“Azure Active Directory”&gt;“许可证”&gt;“所有产品”下，选择一个或多个产品，然后选择命令栏上的“分配”。

![选择一个要分配的许可证](media/license-users-groups/select-license-to-assign.png)

可使用“用户和组”边栏选项卡选择多个用户或组，或在产品中禁用服务计划。 使用顶部的搜索栏搜索用户和组名称。

![选择用于许可证分配的用户或组](media/license-users-groups/select-user-for-license-assignment.png)

将许可证分配给组时，可能需要一些时间等待所有用户继承许可证，具体等待时间取决于组的大小。 可在“许可证”磁贴下的“组”边栏选项卡上，查看处理状态。

![许可证分配状态](media/license-users-groups/license-assignment-status.png)

在 Azure AD 许可证分配期间，可能会发生分配错误，但在管理 Azure AD 和企业移动性 + 安全性产品时，这种情况相对很少见。 潜在的分配错误仅限于：
- 分配冲突：以前为用户分配的许可证与当前许可证不兼容。 在此情况下，若要分配新的许可证，必须先删除当前许可证。
- 超过可用许可证数目：分配组中的用户数目超过可用的许可证数目时，用户的分配状态将反映因缺少许可证而发生分配失败。

### <a name="azure-ad-b2b-collaboration-licensing"></a>Azure AD B2B 协作授权

通过 B2B 协作，可将来宾用户邀请至自己的 Azure AD 租户，以提供 Azure AD 服务及任何设为可用资源的 Azure 资源的访问权限。  

邀请 B2B 用户并将其分配到 Azure AD 中的应用程序不会产生费用。 B2B 协作用户还可免费使用最多 10 个应用（每个来宾用户）和 3 个基本报表。 如果来宾用户在合作伙伴的 Azure AD 租户中具有相应的许可证，那么他们也将在你的租户中获得相应许可。

这不是必需的，但如果要提供 Azure AD 付费功能的访问权限，这些 B2B 来宾用户必须使用相应的 Azure AD 许可证来获得许可。 一个具有 Azure AD 付费许可证的邀请方租户可向其他五个受邀加入租户的来宾用户分配 B2B 协作用户权限。 如需相关方案和信息，请参阅 [B2B 协作许可指南](active-directory-b2b-licensing.md)。

## <a name="view-assigned-licenses"></a>查看已分配的许可证

已分配和可用的许可证摘要视图显示在“Azure Active Directory”&gt;“许可证”&gt;“所有产品”下。

![查看许可证摘要](media/license-users-groups/view-license-summary.png)

选择特定产品时，会提供已分配的用户和组的详细列表。 “许可用户”列表显示所有当前正在使用许可证的用户，并显示出直接向其分配许可证的用户以及从组继承许可证的用户。

![查看许可证详细信息](media/license-users-groups/view-license-detail.png)

同样，“许可组”列表显示所有已向其分配许可证的组。 选择一个用户或组以打开“许可证”边栏选项卡，该选项卡显示分配给该对象的所有许可证。

## <a name="remove-a-license"></a>删除许可证

若要删除许可证，请转到用户或组，打开“许可证”磁贴。 选择许可证，然后单击“删除”。

![删除许可证](media/license-users-groups/remove-license.png)

无法直接删除用户从组继承的许可证。 需要从继承许可证的组删除该用户。


## <a name="next-steps"></a>后续步骤
在本快速入门教程中，你已了解如何在 Azure AD 目录中将许可证分配给用户和组。 

可使用以下链接在 Azure 门户的 Azure AD 中配置订阅许可证分配。

> [!div class="nextstepaction"]
> [分配 Azure AD 许可证](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 
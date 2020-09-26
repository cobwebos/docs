---
title: 分配给云组的角色疑难解答常见问题解答- Azure Active Directory | Microsoft Docs
description: 了解在 Azure Active Directory 中将角色分配给组的一些常见问题和故障排除提示。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7acbb9aa443cde8df7016d3f2a38d58002b98dcd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317389"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>分配给云组的角色疑难解答

以下是一些常见问题和疑难解答提示，用于在 Azure Active Directory (Azure AD) 中将角色分配给组。

**问：** 我是组管理员，但看不 **到可以分配给组交换机的 Azure AD 角色** 。

**答：** 只有特权角色管理员或全局管理员才能创建适合角色分配的组。 只有这些角色中的用户才会看到此控制。

**问：** 谁可以修改分配给 Azure AD 角色的组的成员资格？

**答:** 默认情况下，只有特权角色管理员和全局管理员管理可分配角色组的成员资格，但你可以通过添加组所有者来委派对可分配角色的组的管理。

**问**：我是组织中的帮助台管理员，但无法更新目录读取器用户的密码。 为何发生这种情况？

**答**：用户可能通过可分配角色的组获取目录读取器。 可分配角色的组的所有成员和所有者都受到保护。 只有具有特权身份验证管理员或全局管理员角色的用户才能重置受保护用户的凭据。

**问：** 无法更新用户的密码。 它们未分配任何更高权限的特权角色。 为何会发生这种情况？

**答:** 用户可以是可分配角色的组的所有者。 我们保护可分配角色的组的所有者，以避免特权提升。 例如，如果将组 Contoso_Security_Admins 分配给安全管理员角色，其中 Bob 是组所有者，Alice 是组织中的密码管理员，则可能会出现这种情况。 如果没有这种保护措施，Alice 可以重置 Bob 的凭据并接管其身份。 之后，Alice 可以将自己或任何人添加到 Contoso_Security_Admins 组，成为组织中的安全管理员。 若要查明用户是否为组所有者，请获取该用户拥有的对象列表，并查看是否有组将 isAssignableToRole 设置为 true。 如果是，则用户是受保护的，并且该行为是设计的行为。 请参阅以下文档，了解如何获取拥有的对象：

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject?view=azureadps-2.0)  
- [列出 ownedObjects](/graph/api/user-list-ownedobjects?tabs=http&view=graph-rest-1.0)

**问：** 是否可以对可分配给 Azure AD 角色的组（特别是 isAssignableToRole 属性设置为 true 的组）创建访问评审？  

**答:** 可以。 如果你使用最新版访问评审，则审阅者默认进入“我的访问权限”，且仅全局管理员可以对可分配角色的组创建访问评审。 但是，如果你使用旧版本的访问评审，则审阅者默认进入“访问面板”，且全局管理员和用户管理员均可以对可分配角色的组创建访问评审。 新版体验将于 2020 年 7 月 28 日向所有客户推出。如果你想尽快升级，请在 [Azure AD 访问评审 - 我的访问权限新审阅者体验登记表中](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)提出申请。

**问：** 是否可以创建访问包并将可以分配给 Azure AD 角色的组添加到其中？

**答:** 可以。 全局管理员和用户管理员有权将任何组放入访问包中。 全局管理员没有任何更改，但用户管理员角色权限稍有更改。 若要将可分配角色的组放入访问包中，你必须是用户管理员，同时也是可分配角色的组的所有者。 下面是显示了哪些用户可以在“企业许可证管理”中创建访问包的完整表：

Azure AD 目录角色 | 权利管理角色 | 可以添加安全组\* | 可以添加 Microsoft 365 组\* | 可以添加应用 | 可以添加 SharePoint Online 站点
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
全局管理员 | 不适用 | ✔️ | ✔️ | ✔️  | ✔️
用户管理员  | 不适用  | ✔️  | ✔️  | ✔️
Intune 管理员 | 目录所有者 | ✔️  | ✔️  | &nbsp;  | &nbsp;
Exchange 管理员  | 目录所有者  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Teams 服务管理员 | 目录所有者  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
SharePoint 管理员 | 目录所有者 | &nbsp; | ✔️  | &nbsp;  | ✔️ 
应用程序管理员 | 目录所有者  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
云应用程序管理员 | 目录所有者  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
用户 | 目录所有者 | 仅限组所有者 | 仅限组所有者 | 仅限应用所有者  | &nbsp;

\*组不可分配角色；也就是说，isAssignableToRole = false。 如果组可以分配角色，则创建访问包的人员也必须是可分配角色的组的所有者。

**问：** 在“分配的角色”中找不到“删除分配”选项。 如何删除分配给用户的角色？

**答:** 此答案仅适用于 Azure AD Premium P1 组织。

1. 登录到 [Azure 门户](https://portal.azure.com)，然后打开“Azure Active Directory”。
1. 选择“用户”并打开用户配置文件。
1. 选择“分配的角色”。
1. 选择齿轮图标。 此时会打开一个窗格，会显示此信息。 直接分配旁边有一个“删除”按钮。 若要删除间接角色分配，请从已分配角色的组中删除该用户。

**问：** 如何查看所有可分配角色的组？

**答:** 执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后打开“Azure Active Directory”。
1. 选择“组” > “所有组” 。
1. 选择“添加筛选器”。
1. 筛选到“角色可分配”。

**问：** 如何实现知道哪个角色直接和间接分配到了某个主体？

**答:** 执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后打开“Azure Active Directory”。
1. 选择“用户”并打开用户配置文件。
1. 选择“分配的角色”，然后：

    - 在 Azure AD Premium P1 许可组织中：选择齿轮图标。 此时会打开一个窗格，会显示此信息。
    - 在 Azure AD Premium P2 许可组织中：你可以在“成员资格”列中找到直接和继承的许可证信息。

**问：** 我们为何要强制创建新的云组来将其分配给角色？  

**答:** 如果将现有组分配给角色，则现有组所有者可以向该组添加其他成员，而新成员不会意识到他们将拥有该角色。 因为可分配角色的组功能强大，因此我们设置了很多限制来保护它们。 你不会希望发生会让管理组的人员感到意外的更改。

## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](roles-groups-concept.md)
- [创建可分配角色的组](roles-groups-create-eligible.md)
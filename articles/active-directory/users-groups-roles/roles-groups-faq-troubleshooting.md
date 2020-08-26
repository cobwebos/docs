---
title: 分配给云组的角色疑难解答常见问题解答-Azure Active Directory |Microsoft Docs
description: 将 Azure AD 角色分配给 Azure 门户、PowerShell 或图形 API 中可分配角色的组。
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
ms.openlocfilehash: a3f2a23da5baa3a5d1955b10d18411fcedc3acd1
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798289"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>分配给云组的角色疑难解答

下面是一些常见问题和疑难解答技巧，用于将角色分配到 Azure Active Directory (Azure AD) 中的组。

**问：** 我是组管理员，但看不 **到可以分配给组交换机的 Azure AD 角色** 。

**答：** 只有特权角色管理员或全局管理员才能创建适合角色分配的组。 只有这些角色中的用户才会看到此控制。

**问：** 谁可以修改分配给 Azure AD 角色的组的成员身份？

**答：** 默认情况下，只有特权角色管理员和全局管理员可以管理角色可分配的组的成员身份，但你可以通过添加组所有者来委派角色可分配的组的管理。

**问**：我是组织中的支持人员管理员，但无法更新作为目录读者的用户的密码。 为什么会发生这种情况？

**答**：用户可能已通过角色可分配的组获得目录读取器。 可分配角色的组的所有成员和所有者都受到保护。 只有特权身份验证管理员或全局管理员角色中的用户可以重置受保护用户的凭据。

**问：** 无法更新用户的密码。 它们未分配任何更高的特权角色。 为什么会发生这种情况？

**答：** 用户可以是可分配角色的组的所有者。 我们保护可分配角色的组的所有者，以避免权限提升。 例如，如果将组 Contoso_Security_Admins 分配到安全管理员角色，其中 Bob 是组所有者，而 Alice 是组织中的密码管理员，则可能会出现这种情况。 如果此保护不存在，Alice 可能会重置 Bob 的凭据并接管其身份。 之后，Alice 可以向组中添加用户或任何人，Contoso_Security_Admins 组成为组织中的安全管理员。 若要找出用户是否是组所有者，请获取该用户拥有的对象的列表，并查看是否有任何组的 isAssignableToRole 设置为 true。 如果是，则保护该用户，并且该行为是设计的行为。 请参阅以下文档获取拥有的对象：

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject?view=azureadps-2.0)  
- [列出 ownedObjects](/graph/api/user-list-ownedobjects?tabs=http&view=graph-rest-1.0)

**问：** 能否对可分配给 Azure AD 角色的组创建访问评审 (具体而言，将 isAssignableToRole 属性设置为 true) 的组？  

**答：** 是的，你可以。 如果你使用的是最新版本的访问评审，则默认情况下，你的审阅者会定向到 "我的访问"，只有全局管理员才能在可分配角色的组上创建访问评审。 但是，如果你使用的是较旧版本的访问评审，则默认情况下，你的审阅者会定向到访问面板，全局管理员和用户管理员可以在可分配角色的组上创建访问评审。 新体验将于7月 28 2020 日推出给所有客户，但是，如果你想要更早升级，请 [在访问注册中发出 Azure AD 访问评审-已更新的审阅者体验](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)的请求。

**问：** 是否可以创建访问包并将可以分配给 Azure AD 角色的组添加到其中？

**答：** 是的，你可以。 全局管理员和用户管理员可以将任何组放在访问包中。 全局管理员没有任何更改，但用户管理员角色权限略有变化。 若要将角色分配的组放入访问包，你必须是用户管理员，并且还必须是角色可分配的组的所有者。 下面是显示了哪些用户可以在企业许可证管理中创建访问包的完整表：

Azure AD 目录角色 | 权利管理角色 | 可以添加安全组\* | 可以添加 Microsoft 365 组\* | 可以添加应用 | 可以添加 SharePoint Online 站点
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
全局管理员 | 不适用 | ✔️ | ✔️ | ✔️  | ✔️
用户管理员  | 不适用  | ✔️  | ✔️  | ✔️
Intune 管理员 | 目录所有者 | ✔️  | ✔️  | &nbsp;  | &nbsp;
Exchange 管理员  | 目录所有者  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
团队服务管理员 | 目录所有者  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
SharePoint 管理员 | 目录所有者 | &nbsp; | ✔️  | &nbsp;  | ✔️ 
应用程序管理员 | 目录所有者  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
云应用程序管理员 | 目录所有者  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
用户 | 目录所有者 | 仅限组所有者 | 仅限组所有者 | 仅限应用所有者  | &nbsp;

\*组不是可分配的;也就是说，isAssignableToRole = false。 如果组可以分配角色，则创建访问包的人员也必须是可分配角色的组的所有者。

**问：** 在 "分配的角色" 中找不到 "删除分配" 选项。 如何实现删除用户的角色分配吗？

**答：** 此答案仅适用于 Azure AD Premium P1 组织。

1. 登录到 [Azure 门户](https://portal.azure.com) 并打开 **Azure Active Directory**。
1. 选择 "用户" 并打开一个用户配置文件。
1. 选择 " **分配的角色**"。
1. 选择齿轮图标。 此时会打开一个窗格，可给出此信息。 直接分配旁边有 "删除" 按钮。 若要删除间接角色分配，请从已分配角色的组中删除用户。

**问：** 如何实现查看所有可分配角色的组？

**答：** 请按照以下步骤操作：

1. 登录到 [Azure 门户](https://portal.azure.com) 并打开 **Azure Active Directory**。
1. 选择 "**组**" "  >  **所有组**"。
1. 选择 " **添加筛选器**"。
1. 筛选为可 **分配的角色**。

**问：** 如何实现知道哪个角色直接和间接分配到了某个主体？

**答：** 请按照以下步骤操作：

1. 登录到 [Azure 门户](https://portal.azure.com) 并打开 **Azure Active Directory**。
1. 选择 "用户" 并打开一个用户配置文件。
1. 选择 " **分配的角色**"，然后：

    - 在 Azure AD Premium P1 许可组织：选择齿轮图标。 此时会打开一个窗格，可给出此信息。
    - 在 Azure AD Premium P2 授权组织中：你将在 " **成员身份** " 列中找到直接和继承的许可证信息。

**问：** 我们为何强制创建新的云组来将其分配给角色？  

**答：** 如果将现有组分配给角色，则现有组所有者可以将其他成员添加到此组，而不会有新成员认识到它们将具有角色。 由于角色可分配的组功能强大，因此我们将对其进行保护。 你不想对组的更改对管理组的人员感到吃惊。

## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](roles-groups-concept.md)
- [创建可分配角色的组](roles-groups-create-eligible.md)
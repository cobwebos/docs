---
title: 排查 Azure AD 我的应用登录应用程序时遇到的问题
description: 排查 Azure AD 我的应用登录应用程序时遇到的问题
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9dd407ce2727582039089c0abe31b68bfe5d0f30
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070320"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>排查 Azure AD 我的应用登录应用程序时遇到的问题

"我的应用" 是一种基于 web 的门户，在 Azure Active Directory (Azure AD) 中使用工作或学校帐户的用户可以查看和启动 Azure AD 管理员已授予他们访问权限的基于云的应用程序。 

若要了解有关使用 Azure AD 作为应用的标识提供者的详细信息，请参阅 [什么是 Azure AD 中的应用程序管理](what-is-application-management.md)。 若要快速加快速度，请 [在应用程序管理上查看快速入门系列](view-applications-portal.md)。

这些应用程序代表用户在 Azure AD 门户中进行配置。 必须正确配置应用程序，并将其分配给用户或用户所属的组，才能在 "我的应用" 中看到该应用程序。 

用户看到的应用类型分为以下几类：
-   Office 365 应用程序
-   使用基于联合的 SSO 配置的 Microsoft 应用程序与第三方应用程序
-   基于密码的 SSO 应用程序
-   使用现有 SSO 解决方案的应用程序

## <a name="general-issues-to-check-first"></a>首先要检查的常规问题

-   请确保 web 浏览器满足要求，请参阅 [我的应用支持的浏览器](../user-help/my-apps-portal-end-user-access.md)。
-   确保用户的浏览器已将应用程序的 URL 添加到其**受信任的站点**。
-   务必确认应用程序**配置**正确。
-   确保用户的帐户**已启用**登录功能。
-   确保用户的帐户**未被锁定**。
-   确保用户的**密码未过期或遗忘**。
-   确保**多重身份验证**未阻止用户访问。
-   确保**条件访问策略**或**标识保护**策略不会阻止用户访问。
-   确保用户的**身份验证联系信息**是最新的以允许强制执行多重身份验证或条件访问策略。
-   同时还务必尝试清除浏览器 Cookie，并尝试重新登录。

## <a name="problems-with-the-users-account"></a>用户帐户问题
由于用户帐户出现问题，因此无法访问我的应用。 下面是可以用来排除和解决用户及其帐户设置存在的问题的一些方法：
-   [检查在 Azure Active Directory 中是否存在用户帐户](#check-if-a-user-account-exists-in-azure-active-directory)
-   [检查用户帐户的状态](#check-a-users-account-status)
-   [重置用户的密码](#reset-a-users-password)
-   [启用自助服务密码重置](#enable-self-service-password-reset)
-   [检查用户的多重身份验证状态](#check-a-users-multi-factor-authentication-status)
-   [检查用户的身份验证联系信息](#check-a-users-authentication-contact-info)
-   [检查用户的组成员身份](#check-a-users-group-memberships)
-   [检查用户的已分配许可证](#check-a-users-assigned-licenses)
-   [为用户分配许可证](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>检查在 Azure Active Directory 中是否存在用户帐户
若要检查是否存在某个用户帐户，请执行以下步骤：
1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2.  通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4.  在导航菜单中选择 " **用户和组** "。
5.  选择“所有用户”。
6.  **搜索** 感兴趣的用户，并选择要选择 **的行** 。
7.  检查用户对象的属性，确保它们看上去与预期一致并且未丢失任何数据。

### <a name="check-a-users-account-status"></a>检查用户帐户的状态
若要检查用户帐户的状态，请执行以下步骤：
1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2.  通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4.  在导航菜单中选择 " **用户和组** "。
5.  选择“所有用户”。
6.  **搜索** 感兴趣的用户，并选择要选择 **的行** 。
7.  选择 " **配置文件**"。
8.  在“设置”**** 下，确保“阻止登录”**** 设置为“否”****。

### <a name="reset-a-users-password"></a>重置用户的密码
若要重置用户的密码，请执行以下步骤：
1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2.  通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4.  在导航菜单中选择 " **用户和组** "。
5.  选择“所有用户”。
6.  **搜索** 感兴趣的用户，并选择要选择 **的行** 。
7.  选择用户窗格顶部的 " **重置密码** " 按钮。
8.  在出现的 "**重置密码**" 窗格上，选择 "**重置密码**" 按钮。
9.  为用户复制**临时密码**或**输入新密码**。
10. 告知用户此新密码，在他们下一次登录到 Azure Active Directory 时，需要更改此密码。

### <a name="enable-self-service-password-reset"></a>启用自助式密码重置
若要启用自助服务密码重置，请执行以下部署步骤：
-   [使用户能够重置其 Azure Active Directory 密码](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)
-   [使用户能够重置或更改其 Active Directory 本地密码](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>检查用户的多重身份验证状态
若要检查用户的多重身份验证状态，请执行以下步骤：
1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2. 通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4. 在导航菜单中选择 " **用户和组** "。
5. 选择“所有用户”。
6. 选择窗格顶部的 " **多重身份验证** " 按钮。
7. 在 **多重身份验证管理门户** 加载后，确保位于 " **用户** " 选项卡上。
8. 通过搜索、筛选或排序在用户列表中找到用户。
9. 从用户列表中选择用户，并根据需要**启用**、**禁用**或**强制实施**多重身份验证。
   >[!NOTE]
   >如果用户处于**已强制实施**状态，可暂时将其设置为**已禁用**以允许用户重新登录到其帐户。 一旦他们重新登录到其帐户，便可以再次将其状态更改为**已启用**来要求他们在下次登录期间重新注册其联系信息。 此外，也可以按照[检查用户的身份验证联系信息](#check-a-users-authentication-contact-info)中的步骤为其验证或设置此数据。

### <a name="check-a-users-authentication-contact-info"></a>检查用户的身份验证联系信息
若要检查用于多重身份验证、条件访问、标识保护和密码重置的用户身份验证联系信息，请执行以下步骤：
1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2.  通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4.  在导航菜单中选择 " **用户和组** "。
5.  选择“所有用户”。
6.  **搜索** 感兴趣的用户，并选择要选择 **的行** 。
7.  选择 " **配置文件**"。
8.  向下滚动到“身份验证联系信息”****。
9.  **查看**为用户注册的数据并根据需要进行更新。

### <a name="check-a-users-group-memberships"></a>检查用户的组成员身份
若要检查用户的组成员身份，请执行以下步骤：
1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2.  通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4.  在导航菜单中选择 " **用户和组** "。
5.  选择“所有用户”。
6.  **搜索** 感兴趣的用户，并选择要选择 **的行** 。
7.  选择 **组** 以查看用户所属的组。

### <a name="check-a-users-assigned-licenses"></a>检查用户的已分配许可证
若要检查用户的已分配许可证，请执行以下步骤：
1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2.  通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4.  在导航菜单中选择 " **用户和组** "。
5.  选择“所有用户”。
6.  **搜索** 感兴趣的用户，并选择要选择 **的行** 。
7.  选择 " **许可证** " 以查看用户当前已分配的许可证。

### <a name="assign-a-user-a-license"></a>为用户分配许可证 
若要将许可证分配给用户，请执行以下步骤：
1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2.  通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4.  在导航菜单中选择 " **用户和组** "。
5.  选择“所有用户”。
6.  **搜索** 感兴趣的用户，并选择要选择 **的行** 。
7.  选择 " **许可证** " 以查看用户当前已分配的许可证。
8.  选择 " **分配** " 按钮。
9.  从可用产品列表中选择**一个或多个产品**。
10. **可选** 选择 " **分配选项** " 项以按粒度分配产品。 选择“确定”  。
11. 选择 " **分配** " 按钮，将这些许可证分配给此用户。

## <a name="troubleshooting-deep-links"></a>深层链接疑难解答
深层链接或用户访问 URL 是用户可用来直接从其浏览器 URL 栏访问其密码 - SSO 应用程序的链接。 通过导航到此链接，用户将自动登录到应用程序，而无需先转到 "我的应用"。 此链接与用户从 Office 365 应用程序启动器访问这些应用程序时使用的链接相同。

### <a name="checking-the-deep-link"></a>检查深层链接

若要检查是否具有正确的深层链接，请执行以下步骤：
1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。 
2. 通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4. 从 Azure Active Directory 左侧导航菜单中选择 " **企业应用程序** "。
5. 选择“所有应用程序”，查看所有应用程序的列表。
   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。
6. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。 
7. 通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
8. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
9. 从 Azure Active Directory 左侧导航菜单中选择 " **企业应用程序** "。
10. 选择“所有应用程序”，查看所有应用程序的列表。
    * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。
11. 选择要检查其深层链接的应用程序。
12. 找到“用户访问 URL”**** 标签。 深层链接应与此 URL 匹配。

## <a name="contact-support"></a>联系支持人员
使用以下信息（如果有）打开支持票证：
-   相关错误 ID
-   UPN（用户电子邮件地址）
-   TenantID
-   浏览器类型
-   错误发生的时区和时间/时间段
-   Fiddler 跟踪

## <a name="next-steps"></a>后续步骤
- [应用程序管理的快速入门系列](view-applications-portal.md)

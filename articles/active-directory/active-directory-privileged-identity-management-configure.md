<properties
	pageTitle="Azure AD 特权标识管理"
	description="本主题介绍什么是 Azure AD 特权标识管理，以及如何对它进行配置。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="01/21/2016"
	wacn.date=""/>

# Azure AD 特权标识管理

利用 Azure Active Directory (AD) Privileged Identity Management，可以管理、控制和监视特权标识及其对 Azure AD 中和 Office 365 或 Microsoft Intune 等其他 Microsoft Online Services 中资源的访问权限。

为了允许用户执行特权操作，组织通常需要为许多用户授予 Azure AD、Azure 或 Office 365 资源或者其他 SaaS 应用的永久访问特权。对于许多客户而言，这会给云中托管的资源不断增大安全风险，因为他们无法充分监视这些用户正在使用管理特权执行哪些操作。此外，如果有访问特权的用户帐户被泄露，则可能会影响客户的总体云安全性。Azure AD 特权标识管理可帮助解决这一风险。

利用 Azure AD Privileged Identity Management，你可以：

- 发现哪些用户是 Azure AD 管理员
- 启用对访问目录的按需“实时”管理权限
- 获取有关管理员访问历史记录以及管理员分配更改的报告
- 获取有关访问特权角色的警报

Azure AD Privileged Identity Management 可以管理内置的 Azure Active Directory 组织角色：

- 全局管理员角色
- 计费管理员
- 服务管理员  
- 用户管理员
- 密码管理员

## 实时管理员访问

以前，你可以通过 Azure 管理门户或 Windows PowerShell 向管理员角色分配用户。因此，该用户将成为**永久管理员**，始终以他或她的分配角色工作。此预览版增加了**临时管理员**支持，该用户需要完成激活过程才能获得分配的角色。激活过程会 Azure AD 中的用户角色分配从活动更改为非活动。

## 为目录启用特权标识管理

可以通过访问 [Azure 门户](https://portal.azure.cn/)开始使用 Azure AD Privileged Identity Management。目前，Azure AD Privileged Identity Management 只会出现在 Azure 门户中。它不会出现在经典门户中。你必须是全局管理员才能为目录启用 Azure AD 特权标识管理。

![Azure 门户 — 搜索特权标识 — 屏幕截图][1]

[初始化此扩展](active-directory-privileged-identity-management-getting-started.md)后，你将自动成为目录的第一个**安全管理员**。安全管理员可以访问此扩展来管理其他管理员的访问权限。

在初始化期间，Azure AD Privileged Identity Management 的磁贴将添加到 Azure 门户的开始面板。

## 特权标识管理仪表板

Azure AD Privileged Identity Manager 提供一个仪表板，其中显示了许多重要信息，例如：

- 分配给每个特权角色的用户数  
- 临时管理员和永久管理员的数目
- 管理员的访问历史记录

![PIM 仪表板 — 屏幕截图][2]

## 特权角色管理

使用 Azure AD 特权标识管理，你可以通过添加或删除每个角色的永久或临时管理员，来管理管理员。

![PIM 添加/删除管理员 — 屏幕截图][3]

## 配置角色激活设置

使用角色激活设置可以配置临时角色激活属性，包括：

- 角色激活期限的持续时间
- 角色激活通知
- 用户在角色激活过程中需要提供的信息  

![PIM 设置 — 管理员激活 — 屏幕截图][4]

## 角色激活  

若要激活角色，临时管理员需要请求该角色的时间限定“激活”。可以使用 Azure AD 特权标识管理中“激活我的角色”选项请求激活。

想要激活某个角色的管理员需要在 Azure 门户中初始化 Azure AD Privileged Identity Management。

任何类型的管理员都可以使用 Azure AD 特权标识管理激活其角色。

角色激活有时间限制。在“角色激活”设置中，你可以配置激活时限，以及管理员为了激活角色而需要提供的信息。

![PIM 管理员请求角色激活 — 屏幕截图][5]

## 角色激活历史记录

使用 Azure AD Privileged Identity Management 还可以跟踪特权角色分配的变化以及角色激活历史记录。这可以使用审核日志选项来实现：

![PIM 激活历史记录 — 屏幕截图][6]

## 后续步骤
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png


<!---HONumber=Mooncake_0516_2016-->
<properties
	pageTitle="Azure AD Privileged Identity Management | Azure"
	description="本主题介绍什么是 Azure AD Privileged Identity Management，以及如何使用 PIM 来提升云安全性。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="04/15/2016"
	wacn.date=""/>

# Azure AD 特权标识管理

利用 Azure Active Directory (AD) Privileged Identity Management，可以管理、控制和监视特权标识以及对 Azure AD 中和 Office 365 或 Microsoft Intune 等其他 Microsoft Online Services 中资源的访问权限。

用户有时候需要在 Azure 或 Office 365 资源或者其他 SaaS 应用中执行特权操作。这通常意味着，组织必须授予他们永久的 Azure AD 访问特权。这会给云中托管的资源不断增大安全风险，因为组织无法充分监视这些用户正在使用管理特权执行哪些操作。此外，如果有访问特权的用户帐户被泄露，这个缺口可能会影响其总体云安全性。Azure AD 特权标识管理可帮助解决这一风险。

利用 Azure AD Privileged Identity Management，你可以：

- 查看哪些用户是 Azure AD 管理员
- 按需启用对 Office 365 和 Intune 等 Microsoft Online Services 的“实时”管理访问权限
- 获取有关管理员访问历史记录以及管理员分配更改的报告
- 获取有关访问特权角色的警报

Azure AD Privileged Identity Management 可以管理内置的 Azure Active Directory 组织角色：

- 全局管理员角色
- 计费管理员
- 服务管理员  
- 用户管理员
- 密码管理员

## 实时管理员访问

以前，你可以通过早期的 Azure 管理门户或 Windows PowerShell 向管理员角色分配用户。因此，该用户将成为该角色的**永久管理员**，始终以他或她的分配角色工作。Azure AD Privileged Identity Management 引入了**临时管理员**角色的概念，这是需要完成分配角色激活过程的用户。激活过程会在指定的时段内（如 8 小时），将 Azure AD 中的用户角色分配从非活动更改为活动。

## 为目录启用 Privileged Identity Management

可以通过访问 [Azure 门户](https://portal.azure.com/)开始使用 Azure AD Privileged Identity Management。早期的经典门户不显示 Azure AD Privileged Identity Management。

>[AZURE.NOTE] 你必须是拥有组织帐户而非 Microsoft 帐户的全局管理员，才能为目录启用 Azure AD Privileged Identity Management。

1. 以目录的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 如果你的组织有多个目录，请单击 Azure 门户右上角的用户名，选择你要使用 Azure AD Privileged Identity Management 的目录。
3. 单击左侧导航区域中的“新建”图标。
4. 选择“安全性 + 标识”。
5. 选择“Azure AD Privileged Identity Management”。
6. 选中“固定到仪表板”选项，然后单击“创建”按钮。“Privileged Identity Management”仪表板随即打开。

如果你是在目录中使用 Azure AD Privileged Identity Management 的第一个人，则[安全性向导](active-directory-privileged-identity-management-security-wizard.md)会引导你完成初始分配体验。之后，你将自动成为目录的第一个**安全管理员**。

只有安全管理员才能使用 PIM 应用来管理其他管理员的访问权限。你可以[在 PIM 中为其他用户提供管理能力](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)。

## 特权标识管理仪表板

Azure AD Privileged Identity Manager 提供一个仪表板，其中显示了许多重要信息，例如：

- 分配给每个特权角色的用户数  
- 临时管理员和永久管理员的数目
- 每个管理员的访问历史记录

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

角色激活有时间限制。在“角色激活”设置中，你可以设置激活时限，以及管理员为了激活角色而需要提供的信息。

![PIM 管理员请求角色激活 — 屏幕截图][5]

## 角色激活历史记录

使用 Azure AD Privileged Identity Management 还可以跟踪特权角色分配的变化以及角色激活历史记录。这可以使用审核日志选项来实现：

![PIM 激活历史记录 — 屏幕截图][6]

## 后续步骤
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png


<!---HONumber=Mooncake_0620_2016-->
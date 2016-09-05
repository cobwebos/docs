<properties
	pageTitle="Azure AD Privileged Identity Management | Azure"
	description="本主题介绍什么是 Azure AD Privileged Identity Management，以及如何使用 PIM 来提升云安全性。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="08/11/2016"
	wacn.date=""/>  


# Azure AD 特权标识管理

使用 Azure Active Directory (AD) Privileged Identity Management，可以管理、控制和监视组织内的访问。这包括访问 Azure AD 和其他 Microsoft Online Services（如 Office 365 或 Microsoft Intune）中的资源。

组织希望尽量减少拥有访问权限的人员以保护信息或资源安全，因为这样做可以减小恶意用户获得相应访问权限的机会。但是，用户仍需在 Azure、Office 365 或 SaaS 应用中执行特权操作。最终必须完成工作，因此组织会授予用户在 Azure AD 中的特许访问权限，而不会监视这些用户使用其管理员特权执行哪些操作。Azure AD 特权标识管理可帮助解决这一风险。

Azure AD Privileged Identity Management 可帮助你实现以下操作：

- 查看哪些用户是 Azure AD 管理员
- 按需启用对 Office 365 和 Intune 等 Microsoft Online Services 的“实时”管理访问权限
- 获取有关管理员访问历史记录以及管理员分配更改的报告
- 获取有关访问特权角色的警报

Azure AD Privileged Identity Management 可以管理内置的 Azure AD 组织角色，包括：

- 全局管理员角色
- 计费管理员
- 服务管理员
- 用户管理员
- 密码管理员

## 实时管理员访问

以前，你可以通过 Azure 经典门户或 Windows PowerShell 向管理员角色分配用户。因此，该用户将成为**永久管理员**，始终以分配的角色工作。Azure AD Privileged Identity Management 引入了**有资格管理员**的概念 。有资格的管理员应是不时（但不是每天）需要特权访问的用户。该角色处于非活动状态，直到用户需要访问权限，然后他们完成激活过程，并在预定的时间内成为活动管理员。

## 为目录启用 Privileged Identity Management

可以在 [Azure 门户](https://portal.azure.cn/)中开始使用 Azure AD Privileged Identity Management。

>[AZURE.NOTE] 你必须是拥有组织帐户（例如，@yourdomain.com）而非 Microsoft 帐户（例如，@outlook.com）的全局管理员，才能为目录启用 Azure AD Privileged Identity Management。

1. 以目录的全局管理员身份登录 [Azure 门户](https://portal.azure.cn/)。
2. 如果你的组织有多个目录，请在 Azure 门户的右上角选择你的用户名。选择你将在其中使用 Azure AD Privileged Identity Management 的目录。
3. 选择“新建”>“安全性 + 标识”>“Azure AD Privileged Identity Management”。

	![在门户中启用 PIM][1]  


4. 选中“固定到仪表板”选项，然后单击“创建”按钮。此时“Privileged Identity Management”仪表板将打开。

如果你是在目录中使用 Azure AD Privileged Identity Management 的第一个人，则[安全性向导](/documentation/articles/active-directory-privileged-identity-management-security-wizard/)会引导你完成初始分配体验。之后，你将会自动成为该目录的第一个**安全管理员**和**特权角色管理员**。

只有特权角色管理员才能管理其他管理员的访问权限。你可以[在 PIM 中为其他用户提供管理能力](/documentation/articles/active-directory-privileged-identity-management-how-to-give-access-to-pim/)。

## 特权标识管理仪表板

Azure AD Privileged Identity Manager 提供一个仪表板，其中显示了许多重要信息，例如：

- 指出提升安全性的机会的警报
- 分配给每个特权角色的用户数
- 有资格的管理员和永久管理员的数目
- 查看进行中的访问

![PIM 仪表板 — 屏幕截图][2]  


## 特权角色管理

使用 Azure AD 特权标识管理，你可以通过添加或删除每个角色的永久或有资格的管理员，来管理管理员。

![PIM 添加/删除管理员 — 屏幕截图][3]  


## 配置角色激活设置

使用角色激活设置可以配置有资格的角色激活属性，包括：

- 角色激活期限的持续时间
- 角色激活通知
- 用户在角色激活过程中需要提供的信息

![PIM 设置 — 管理员激活 — 屏幕截图][4]  


请注意，图中“多重身份验证”的按钮处于禁用状态。对于某些特权级别高的角色，我们需要 MFA 以便加强保护。

## 角色激活  

若要激活角色，有资格的管理员需要请求该角色的时间限定“激活”。可以使用 Azure AD 特权标识管理中“激活我的角色”选项请求激活。

想要激活某个角色的管理员需要在 Azure 门户中初始化 Azure AD Privileged Identity Management。

任何类型的管理员都可以使用 Azure AD Privileged Identity Management 激活角色。

角色激活可自定义。在“PIM”设置中，你可以确定激活时限，以及管理员为了激活角色而需要提供的信息。

![PIM 管理员请求角色激活 — 屏幕截图][5]  


## 角色激活历史记录

使用 Azure AD Privileged Identity Management 还可以跟踪特权角色分配的变化以及角色激活历史记录。这可以使用审核日志选项来实现：

![PIM 激活历史记录 — 屏幕截图][6]  


## 后续步骤
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->


[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=Mooncake_0829_2016-->
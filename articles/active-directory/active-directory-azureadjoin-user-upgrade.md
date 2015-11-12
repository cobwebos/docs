<properties 
	pageTitle="通过“设置”使用 Azure AD 设置 Windows 10 设备 | Microsoft Azure" 
	description="本主题介绍用户如何通过设置菜单加入到 Azure AD。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="08/02/2015" 
	wacn.date=""/>

# 通过“设置”使用 Azure AD 设置 Windows 10 设备
如果你以前使用的是 Windows 7 或 8，现在计算机已升级到 Windows 10，则你可以通过“设置”菜单加入到 Azure AD。

从设置菜单加入到 Azure AD
-----------------------------------------------------------------------------------------------

1. 在“开始”菜单中，单击“设置”超级按钮。
2. 单击“设置”->“系统”->“关于”->“加入 Azure AD”
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. 在“Azure AD 加入”消息窗口中单击“继续”。
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. 提供你的登录凭据。此登录体验将会提供完成身份验证所需的全部步骤。如果你是联合租户的成员，你的管理员将为你提供组织托管的联合身份验证体验。
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. 如果你的组织已配置为使用 Multi-Factor Authenticaion 来加入 Azure AD，则你需要提供第二因素才能继续下一步。
6. 在“允许管理此设备”屏幕中单击“接受”。
7. 你应会看到消息“你的设备现已加入 Azure AD 中你的组织”。


## 其他信息
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade)
* [了解 Azure AD Join 的使用方案和部署注意事项](active-directory-azureadjoin-deployment-aadjoindirect)
* [设置 Azure AD Join](active-directory-azureadjoin-setup)

<!---HONumber=79-->
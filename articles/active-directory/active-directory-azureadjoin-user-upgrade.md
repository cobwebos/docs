<properties
	pageTitle="通过“设置”使用 Azure AD 设置 Windows 10 设备 | Microsoft Azure"
	description="说明用户如何通过“设置”菜单加入 Azure AD。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.date="02/26/2016"
	wacn.date=""/>

# 通过“设置”使用 Azure AD 设置 Windows 10 设备
如果你已经在使用 Windows 7 或 Windows 8，且你的计算机或设备已升级为 Windows 10，则可以通过“设置”菜单加入 Azure Active Directory (Azure AD)。

## 通过“设置”菜单加入 Azure AD 的步骤


1. 在“开始”菜单中，单击“设置”超级按钮。
2. 从“设置”中选择“系统”->“关于”->“加入 Azure AD”。
<center>
![通过“设置”菜单加入 Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. 在“Azure AD 加入”消息窗口中单击“继续”。
<center>
![“加入 Azure AD”消息窗口](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. 提供你的登录凭据。此登录体验将会提供完成身份验证所需的全部步骤。如果你是联合租户的成员，你的管理员将为你提供组织托管的联合身份验证体验。
<center>
![提供登录凭据](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. 如果你的组织已配置为使用 Azure Multi-Factor Authentication 来加入 Azure AD，则需要提供第二个因素才能继续下一步操作。
6. 在“允许管理此设备”屏幕中单击“接受”。
7. 你应会看到消息“你的设备现已加入你在 Azure AD 中的组织”。


## 其他信息
* [了解 Azure AD Join 的使用方案](/documentation/articles/active-directory-azureadjoin-deployment-aadjoindirect)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](/documentation/articles/active-directory-azureadjoin-devices-group-policy)
* [设置 Azure AD Join](/documentation/articles/active-directory-azureadjoin-setup)
* [通过 Microsoft Passport 在不使用密码的情况下进行身份验证](/documentation/articles/active-directory-azureadjoin-passport)

<!---HONumber=Mooncake_0613_2016-->
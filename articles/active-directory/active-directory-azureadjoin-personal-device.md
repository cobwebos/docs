<properties
	pageTitle="将个人设备加入组织 | Microsoft Azure"
	description="说明用户如何向其企业网络注册其个人的 Windows 10 设备，并提供 BYOD 方案的部署步骤。"
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

# 将个人设备加入组织

## 将 Windows 10 设备加入组织

1.	从“开始”菜单中，选择“设置”。
2.	选择“帐户”，然后单击**你的帐户**。
3.	单击“添加工作帐户或学校帐户”，然后键入你的组织帐户。
4.	在你组织的登录页面上，输入你的用户名和密码，然后单击“确定”。
5.	系统将提示你完成 Multi-Factor Authentication 质询。（该质询可由 IT 管理员配置。）
6.	Azure Active Directory (Azure AD) 会检查设备是否需要注册移动设备管理。
7.	Windows 会在 Azure AD 的组织目录中注册设备，并将其注册到移动设备管理中（如果适用）。
8.	如果你是托管用户，Windows 会通过自动登录将你转到桌面。
9.	如果你是联合用户，则会转到 Windows 登录屏幕以输入凭据。

## 其他信息
* [面向企业的 Windows 10：在工作中使用设备的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [通过 Microsoft Passport 在不使用密码的情况下进行身份验证](active-directory-azureadjoin-passport.md)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](active-directory-azureadjoin-devices-group-policy.md)
* [设置 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Mooncake_0509_2016-->
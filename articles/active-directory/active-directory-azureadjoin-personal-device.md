<properties
	pageTitle="将个人设备加入组织 | Microsoft Azure"
	description="本主题介绍用户如何将其个人 Windows 10 计算机注册到企业网络。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="08/02/2015"
	wacn.date=""/>

# 将个人设备加入组织

将 Windows 10 设备加入组织
--------------------------------------------------------------------------------------------
1.	从“开始”菜单中，选择“设置”。
2.	选择“帐户”，然后单击**你的帐户**。
3.	单击“添加工作帐户或学校帐户”，然后键入你的组织帐户。
4.	然后，你将会转到组织的登录页。输入你的用户名和密码，然后单击“确定”。
5.	然后，系统会提示你完成 Multi-Factor Authentication 质询。IT 部门可以配置质询。
6.	然后，Azure AD 检查将此用户/设备是否需要移动设备管理 (MDM) 注册。
7.	然后，Windows 将在 Azure AD 上的组织目录中注册该设备，并在 MDM 中进行注册。
8.	完成此操作后，如果你是托管用户，Windows 将完成安装程序，并通过自动登录屏幕将用户定向到桌面。
9.	如果你是联合用户，将会转到 Windows 登录屏幕，你必须输入凭据才能登录。

## 其他信息
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade)
* [了解 Azure AD Join 的使用方案和部署注意事项](active-directory-azureadjoin-deployment-aadjoindirect)
* [设置 Azure AD Join](active-directory-azureadjoin-setup)

<!---HONumber=79-->
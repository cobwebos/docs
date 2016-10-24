<properties
	pageTitle="Azure Multi-Factor Authentication 服务器和 Active Directory 联合身份验证服务入门"
	description="这是与 Azure Multi-Factor Authentication 相关的页面，介绍如何将 Azure MFA 与 AD FS 配合使用。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na" ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication 服务器和 Active Directory 联合身份验证服务入门



<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

如果你的组织已使用 AD FS 将本地 Active Directory 与 Azure Active Directory 联合，则有下述 2 个使用 Azure Multi-Factor Authentication 的选项。

- 使用 Azure Multi-Factor Authentication 或 Active Directory 联合身份验证服务保护 Azure Active Directory 资源
- 使用 Azure Multi-Factor Authentication 服务器保护云和本地资源

下表总结了使用 Azure Multi-Factor Authentication 和 AD FS 保护资源的身份验证体验

|身份验证体验 - 基于浏览器的应用|身份验证体验 - 基于非浏览器的应用
:------------- | :------------- | :------------- |
使用 Azure Multi-Factor Authentication 保护 Azure AD 资源 |<li>第一重身份验证使用 AD FS 在本地执行。</li> <li>第二因素身份验证是使用云身份验证实施的基于手机的方法。</li>|最终用户可以使用应用密码登录。
使用 Active Directory 联合身份验证服务保护 Azure AD 资源 |<li>第一重身份验证使用 AD FS 在本地执行。</li><li>第二重身份验证遵循声明在本地执行。</li>|最终用户可以使用应用密码登录。

有关联合用户的应用密码注意事项：

- 应用密码使用云身份验证进行验证，因此绕过了联合。仅在设置应用密码时，才会主动使用联合。
- 应用密码不遵循“本地客户端访问控制”设置。
- 使用应用密码时，无法使用本地身份验证日志记录功能。
- 帐户禁用/删除可能需要长达 3 小时才能实现目录同步，从而延迟了云标识中应用密码的禁用/删除。

有关设置 Azure Multi-Factor Authentication 或具有 AD FS 的 Azure Multi-Factor Authentication 服务器的信息，请参阅以下主题：

- [使用 Azure Multi-Factor Authentication 和 Azure AD 保护云资源](multi-factor-authentication-get-started-adfs-cloud.md)
- [将 Azure Multi-Factor Authentication 服务器与 Windows Server 2012 R2 AD FS 配合使用来保护云和本地资源](multi-factor-authentication-get-started-adfs-w2k12.md)
- [将 Azure Multi-Factor Authentication 服务器与 AD FS 2.0 配合使用来保护云和本地资源](multi-factor-authentication-get-started-adfs-adfs2.md)

<!---HONumber=AcomDC_0921_2016-->
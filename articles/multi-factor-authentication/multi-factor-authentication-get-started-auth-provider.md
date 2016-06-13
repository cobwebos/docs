<properties 
	pageTitle="Microsoft Azure Multi-Factor Auth 提供程序入门" 
	description="了解如何创建 Azure Multi-Factor Auth 提供程序。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="05/12/2016" 
	wacn.date=""/>



# Azure Multi-Factor Auth 提供程序入门
默认情况，具有 Azure Active Directory 和 Office 365 用户的全局管理员可以使用 Multi-Factor Authentication。但是，如果你想要充分利用[高级功能](multi-factor-authentication-whats-next.md)，则必须购买完整版的 Azure MFA。

> [AZURE.NOTE]  Azure Multi-Factor Auth 提供程序用于利用 Azure MFA 完整版提供的功能。它适用于**没有通过 Azure MFA、Azure AD Premium 或 EMS 获得许可证**的用户。默认情况下，Azure MFA、Azure AD Premium 和 EMS 包括 Azure MFA 完整版。如果你有许可证，则无需 Azure Multi-Factor Auth 提供程序。
 
使用以下步骤创建 Azure Multi-Factor Auth 提供程序。

## 创建 Multi-Factor Auth 提供程序
--------------------------------------------------------------------------------

1. 以管理员身份登录到 **Azure 经典门户**。
2. 在左侧选择“Active Directory”。
3. 在“Active Directory”页的顶部，选择“Multi-Factor Authentication 提供程序”。
![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. 在底部单击“新建”。
![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. 在“应用程序服务”下选择“Multi-Factor Auth 提供程序”。
![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. 选择“快速创建”。
![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. 填写以下字段，然后选择“创建”。
	1. **名称** - Multi-Factor Auth 提供程序的名称。
	2. **使用模型** - Multi-Factor Authentication 提供程序的使用模型。
		- 按身份验证 - 购买按身份验证收费的模型。通常用于在面向使用者的应用程序中使用 Azure Multi-Factor Authentication 的方案。
		- 按启用的用户 - 购买按每个启用的用户收费的模型。通常用于员工访问 Office 365 等应用程序的方案。
	2. **目录** - 与 Multi-Factor Authentication 提供程序关联的 Azure Active Directory 租户。请注意以下事项：
		- 无需 Azure AD 目录即可创建 Multi-Factor Auth 提供程序。如果计划仅使用 Azure Multi-Factor Authentication 服务器或 SDK，则可将此框留空。
		- Multi-Factor Auth 提供程序必须与 Azure AD 目录相关联才能利用高级功能。
		- 仅当要将本地 Active Directory 环境与 Azure AD 目录同步时，才需要 Azure AD Connect、AAD Sync 或 DirSync。如果你只使用未同步的 Azure AD 目录，则
![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)	
5. 单击“创建”后，将创建 Multi-Factor Authentication 提供程序，此时你会看到以下消息：“已成功创建 Multi-Factor Authentication 提供程序”。单击“确定”。
![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)	

<!---HONumber=Mooncake_0606_2016-->
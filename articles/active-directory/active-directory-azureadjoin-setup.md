<properties 
	pageTitle="为用户设置 Azure AD Join | Microsoft Azure" 
	description="本主题说明管理员如何为其最终用户（员工、学生或其他用户）设置 Azure AD Join。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="08/02/2015" 
	wacn.date=""/>

# 在组织中设置 Azure AD Join

在设置 Azure AD Join 之前，需要将用户的本地目录同步到云，或者在 Azure AD 中手动创建托管帐户。

有关将本地用户同步到 Azure AD 的详细说明，请参阅[将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)。


若要在 Azure AD 中手动创建和管理用户，请参阅 [Azure AD 中的用户管理](https://msdn.microsoft.com/library/azure/hh967609.aspx)。

## 设置设备注册 
1. 以管理员身份登录到 Azure 门户。
2. 在左窗格中选择“Active Directory”。
3. 在“目录”选项卡中选择你的目录。
4. 选择“配置”选项卡。
5. 滚动到名为“设备”的部分。
6. 在“设备”选项卡上，设置以下项：  
   * **每个用户的设备数上限**：选择用户可在 Azure AD 中拥有的设备数上限。如果用户达到此配额，则在删除一个或多个现有设备之前，他们无法添加其他设备。
   * **需要 Multi-factor Auth 才能加入设备**：当用户应提供第二个身份验证因素才能将其设备添加 Azure AD 时启用。有关 Multi-factor Auth 的详细信息，请参阅[云中的 Azure Multi-Factor Authentication 入门](multi-factor-authentication-get-started-cloud/)。
   * **用户可将设备加入 Azure AD**：选择可将设备加入 Azure AD 的用户和组。
   * **已加入 Azure AD 的设备上的其他管理员**：使用 Azure AD Premium 或 Enterprise Mobility Suite (EMS)，可以选择要将设备的本地管理员权限授予哪些用户。默认情况下，会将本地管理员权限授予全局管理员和设备所有者。

<center>![](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>
为用户设置 Azure AD Join 后，他们可以通过其企业设备或个人设备连接到 Azure AD。

下面是如何使用户能够设置 Azure AD Join 的三种方案：

- 用户可将公司拥有的设备直接加入 Azure AD
- 用户可将公司拥有的设备加入本地 Active Directory 域，并扩展到 Azure AD
- 用户可在个人设备上将工作帐户添加到 Windows 

## 其他信息
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect)
* [设置 Azure AD Join](active-directory-azureadjoin-setup)

<!---HONumber=79-->
<properties
	pageTitle="保护对 Office 365 和其他连接到 Azure Active Directory 的应用的访问 | Azure"  
    description="借助条件性访问控制，Azure Active Directory 会在验证用户身份时先检查你选取的特定条件，然后才允许访问应用程序。一旦符合这些条件，用户就会通过身份验证并获权访问应用程序。"  
    services="active-directory" 
	keywords="对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略" 
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="03/23/2016"
	wacn.date="" />

# 保护对 Office 365 和其他连接到 Azure Active Directory 的应用的访问  
  
保护对公司资源的访问对每个组织来说都非常重要。随着云服务和移动设备的出现，用户访问公司资源的方式发生了显著变化。这会导致需要更改如何保护公司资源的相关策略。
  
## 为何使用条件性访问？  
 Azure Active Directory 中的条件性访问控制功能为公司提供一些简单的方式，可同时保护他们在云中和本地的资源。无论你的需求是像“防止使用窃取的密码来访问我的资源”还是“必须使用正常的受管理设备才能访问我的企业内容”，Azure Active Directory 都能满足你的需求。

## 如何强制实施条件性访问控制？  
 借助条件性访问控制，Azure Active Directory 会在验证用户身份时先检查你选择的特定条件，然后才允许访问应用程序。一旦符合这些条件，用户就会通过身份验证并获权访问应用程序。
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## 基于用户的资源访问
  
- **用户属性**：在用户属性级别，你可以应用策略以确保只有授权用户可以访问公司资源。   
- **用户的组成员资格**：你也可以根据用户的组成员资格，控制提供给用户的访问权限级别。   
- **Multi Factor Authentication (MFA)**：此外，你也可以强制实施一些策略，以要求用户使用 Multi Factor Authentication 系统来验证其身份。例如，你可以强制用户在个人移动电话上确认 PIN 码，以确保一层额外的安全性。对于已获权访问有效用户的用户名和密码的未授权用户，MFA 身份验证可防止其访问你的资源。 

## 基于设备的条件性访问 

- **已注册的设备**：在设备级别，你可以设置一些策略来强制要求只有已注册或已知的设备才能进行访问。此外，还可以使用移动设备管理 (MDM) 解决方案（例如 Microsoft Intune）来确保只有受管理设备能够访问你的资源。设备级别的条件性访问可确保只有符合你策略（例如在设备上强制使用 PIN）的设备才能进行访问。此外，通过使用 MDM 解决方案，你还可以确保能够远程擦除丢失/遭窃设备上的公司数据。  
- **设备策略**：你也可以设置让策略只根据单个应用程序来限制访问。此外，也可以根据设备的物理位置（即，请求是来自已知的公司网络还是外部）来设置访问级别。  

可使用这些策略来设置的访问级别可以应用于云中或本地的资源。云中的资源可能是 Office 365 和第三方 SaaS 应用之类的应用。此外，这些也可能是你已托管于云中的业务线应用。
  
## 条件性访问 — 内容映射  
以下内容映射列出了进一步了解在当前部署中启用条件性访问所需参考的文档

| 方案 | 文章 |
|------------------------------------------------------|----------|
| 保护公司资源免受网络钓鱼攻击 |[开始使用 MFA 和 Extranet 进行 AAD SaaS 应用的条件性访问](/documentation/articles/active-directory-conditional-access-azuread-connected-apps)<br><br>[Azure AD 应用的条件性访问](/documentation/articles/active-directory-conditional-access-technical-reference)<br><br>[如何配置 MFA](/documentation/articles/multi-factor-authentication-get-started-cloud)<br><br>[将 Azure Multi-Factor Authentication 与 AD FS 配合使用来保护云资源](https://technet.microsoft.com/library/dn758113.aspx)<br><br>[每用户 MFA 注意事项](/documentation/articles/multi-factor-authentication-end-user-manage-settings)<br><br>[来自 Extranet 的 MFA](/documentation/articles/multi-factor-authentication-get-started-adfs-cloud)|
| 保护丢失/遭窃设备上的公司数据 |[设备注册服务](/documentation/articles/active-directory-conditional-access-device-registration-overview)<br><br> [注册已加入域的设备](/documentation/articles/active-directory-azureadjoin-setup)<br><br> [使用 Azure AD 中注册的设备进行本地条件性访问](/documentation/articles/active-directory-conditional-access-on-premises-setup) <br><br>[为加入域的 Windows 7 设备配置自动设备注册](/documentation/articles/active-directory-conditional-access-automatic-device-registration-windows7) <br><br>[为加入域的 Windows 8.1 设备配置自动设备注册](/documentation/articles/active-directory-conditional-access-automatic-device-registration-windows8_1) <br><br>[Office 365 服务的条件性访问设备策略](/documentation/articles/active-directory-conditional-access-device-policies)|
| 其他信息 |[条件性访问常见问题解答](/documentation/articles/active-directory-conditional-faqs)|


<!---HONumber=Mooncake_0613_2016-->
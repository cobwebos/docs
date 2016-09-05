<properties
	pageTitle="Azure Active Directory 条件访问 | Azure"  
	description="借助条件性访问控制，Azure Active Directory 会在验证用户身份时先检查你选取的特定条件，然后才允许访问应用程序。一旦符合这些条件，用户就会通过身份验证并获权访问应用程序。"  
	services="active-directory" 
	keywords="对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略" 
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>  


<tags 
	ms.service="active-directory" 
	ms.date="08/08/2016"
	wacn.date="" />  


# Azure Active Directory 条件访问   
  
保护对公司资源的访问对每个组织来说都非常重要。随着云服务和移动设备的出现，用户访问公司资源的方式发生了显著变化。这要求安全的新方法。
  
## 为何使用条件性访问？  

Azure Active Directory 中的条件访问控制功能为公司提供一些简单的方式，可保护云中和本地的资源。条件访问策略要求多重身份验证，使用它可帮助防止凭据被盗和被仿冒的风险，另外它要求 Intune 托管的设备授权访问敏感的服务，有助于确保公司数据安全。



## 许可要求

条件访问是 [Azure AD Premium](/documentation/services/identity) 的一项功能。访问应用了条件访问策略的应用程序的所有用户都必须拥有 Azure AD Premium 许可证。你可以使用[未经授权的用户报告](https://aka.ms/utc5ix)，了解有关用法的更多信息。





## 如何强制实施条件性访问控制？  
 借助条件性访问控制，Azure Active Directory 会在验证用户身份时先检查你选择的特定条件，然后才允许访问应用程序。一旦符合这些条件，用户就会通过身份验证并获权访问应用程序。
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)  


## 条件
  
- **组成员身份**：你也可以根据用户的组成员身份，控制提供用户的访问级别。

- **位置**：当用户不在受信任的网络中时，可以使用用户位置触发 MFA 并阻止控制。

- **设备平台**：可以使用设备平台类型（如 iOS、Android、Windows Mobile 和 Windows）作为应用策略的条件。

- **已启用设备**：在评估设备策略过程中会验证设备启用/禁用状态。禁用目录中丢失或被盗的设备后，该设备不再可用于满足策略要求。

- **登录和用户风险**：条件访问风险策略可与 Azure AD Identity Protection 一起使用，基于风险事件和异常登录活动提供高级保护。


## 控制
   
- **多重身份验证 (MFA)**：你可以要求使用 MFA 进行强身份验证。可以由 Azure MFA 或本地 MFA 提供程序使用 ADFS 提供 MFA。对于已获权访问有效用户的用户名和密码的未授权用户，MFA 身份验证可帮助防止其访问你的资源。

- **阻止**：可以根据用户位置等的条件应用访问权限。例如，当用户不在受信任的网络中时，阻止访问。

- **兼容设备**：在设备级别，你可以设置策略来强制实施只有已加入域的计算机或已在移动设备管理 (MDM) 中注册并满足合规性的移动设备可以访问。Microsoft Intune 用于在应用程序访问过程中检查设备的合规性并向 Azure Active Directory 返回报告，以便实施。
 

## 应用程序

- 可使用这些策略设置的访问级别可以应用于云中或本地的应用程序和服务。策略将直接应用于网站或服务。然后，对浏览器访问以及访问服务的应用程序强制实施此策略。可将策略应用于的服务的列表可在此处找到。


## 基于设备的条件访问

可以从注册到 Azure AD 中的满足特定条件的设备限制对应用程序的访问。这对于防止有效用户从以下位置访问组织资源非常有用：

- 未知/非托管设备
- 不符合你组织定义的安全策略的设备。

可以基于以下要求设置策略：

- **已加入域的设备** - 你可以设置策略来限制对已加入本地 Active Directory 域并已注册到 Azure AD 中的设备的访问。此策略适用于属于本地 Active Directory 域并已注册到 Azure AD 中的 Windows 台式机、笔记本电脑或企业平板电脑。
有关如何使用 Azure AD 设置已加入域的设备的自动注册的详细信息，请参阅 [How to setup automatic Registration of Windows domain joined devices with Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)（如何使用 Azure Active Directory 设置已加入 Windows 域的设备的自动注册）。

- **兼容设备** - 你可以设置策略来限制对已由管理系统标记为“兼容”的设备的访问。此策略可确保只有符合安全策略（例如，强制对设备实施文件加密）的设备可以访问。此策略可用于限制从以下设备访问：

    - 由混合配置中部署的 System Center Configuration Manager 2016 托管的**已加入 Windows 域的设备**。

    - 由 Microsoft Intune 或受支持的第三方移动设备管理 (MDM) 系统托管的 **Windows 10 移动工作或个人设备**。

    - 由 Microsoft Intune 托管的 **iOS 和 Android 设备**。


访问受基于设备的 CA 策略保护的应用程序的用户需要从符合此策略的设备执行此操作。如果从不符合此策略的设备进行操作，访问将被拒绝。

有关如何在 Azure AD 中配置基于设备的 CA 策略的信息，请参阅 [How to configure Device-based Conditional Access policy for access control to Azure Active Directory connected applications](/documentation/articles/active-directory-conditional-access-policy-connected-applications/)（如何配置基于设备的条件访问策略以实现对 Azure Active Directory 连接的应用程序的访问控制）。

## Azure Active Directory 条件访问的文章索引
  
以下内容映射列出了进一步了解在当前部署中启用条件性访问所需参考的文档


### MFA 和位置策略

- [Getting started with conditional access to Azure AD connected apps based on group, location, and MFA policies](/documentation/articles/active-directory-conditional-access-azuread-connected-apps/)（基于组、位置和 MFA 策略实现对 Azure AD 连接的应用的条件访问入门）

- [What kind of applications are supported](/documentation/articles/active-directory-conditional-access-supported-apps/)（支持哪种应用程序）


### 设备策略

- [How to set Device-based Conditional Access policy for access control to Azure Active Directory connected applications](active-directory-conditional-access-policy-connected-applications.md)（如何设置基于设备的条件访问策略以实现对 Azure Active Directory 连接的应用程序的访问控制）

- [How to setup automatic registration of Windows domain joined devices with Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)（如何使用 Azure Active Directory 设置已加入 Windows 域的设备的自动注册）

- [帮助保护数据（需要 Microsoft Intune）](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### 基于登录风险保护资源

[Azure AD Identity Protection](/documentation/articles/active-directory-identityprotection/)

### 其他信息

- [条件性访问常见问题解答](/documentation/articles/active-directory-conditional-faqs/)
- [技术参考](/documentation/articles/active-directory-conditional-access-technical-reference/)

<!---HONumber=Mooncake_0829_2016-->
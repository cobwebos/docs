<properties
	pageTitle="Azure AD Join 的使用方案和部署注意事项 | Azure"
	description="说明管理员如何为其最终用户（员工、学生或其他用户）设置 Azure AD Join。它还讨论了使用 Azure AD Join 的不同实际方案。"
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

# Azure AD Join 的使用方案和部署注意事项 

## Azure AD Join 的使用方案
### 方案 1：业务主要在云中进行

如果你当前在云中操作和管理企业的标识，或者即将迁移到云，则你可以从 Azure Active Directory Join (Azure AD Join) 获益。你可以使用在 Azure AD 中创建的帐户来登录 Windows 10。通过[首次运行体验 (FRX) 过程](/documentation/articles/active-directory-azureadjoin-user-frx)或通过从[设置菜单](/documentation/articles/active-directory-azureadjoin-user-upgrade)加入 Azure AD，用户可将其计算机加入 Azure AD。用户还可以在浏览器或 Office 应用程序中通过单一登录 (SSO) 访问云资源（例如 Office 365）。

### 方案 2：教育机构

教育机构通常有两种用户类型：教职员和学生。教职员被视为组织中较长期的成员，因此需要为他们创建本地帐户。但学生是组织中期限较短的成员，可在 Azure AD 中管理其帐户。这意味着，可以将目录范围推送到云而不是存储在本地。此外，这还意味着学生可以使用其 Azure AD 帐户登录 Windows，并在 Office 应用程序中获取 Office 365 资源的访问权限。

### 方案 3：零售业

零售业拥有季节性工作者和长期员工。对于较长期的全职员工，通常可以创建本地帐户，并使用已加入域的计算机。但季节性工作者是组织中期限较短的成员，因而需要在可更轻松转移用户许可证的位置管理其帐户。在具有 Office 365 许可证的云中创建这些用户帐户时，这些用户能够从使用 Azure AD 帐户登录 Windows 和 Office 应用程序中获益，同时在他们离开公司之后对于他们的许可证保持更大的灵活性。

### 方案 4：其他方案

除了上述方案以外，你还可以通过让用户将其设备加入 Azure AD，通过简化的加入体验、高效的设备管理、自动化移动设备管理注册以及单一登录 Azure AD 和本地资源来获益。


## Azure AD Join 的部署注意事项

### 允许用户将公司拥有的设备直接加入 Azure AD


企业可以为合作伙伴公司和组织提供仅限云的帐户。然后，这些合作伙伴可以使用单一登录轻松访问公司应用和资源。此方案适用于主要在云中访问依赖于 Azure AD 进行身份验证的资源（例如 Office 365 或 SaaS 应用）的用户。

### 先决条件
**在企业级别（管理员）**

*	Azure 订阅配合 Azure Active Directory  

**在用户级别**

*	Windows 10（专业版和企业版）

### 管理员任务
* [设置设备注册](/documentation/articles/active-directory-azureadjoin-setup)

### 用户任务
* [安装期间使用 Azure AD 设置新的 Windows 10 设备](/documentation/articles/active-directory-azureadjoin-user-frx)
* [通过设置菜单使用 Azure AD 设置 Windows 10 设备](/documentation/articles/active-directory-azureadjoin-user-upgrade)
* [将个人 Windows 10 设备加入你的组织](/documentation/articles/active-directory-azureadjoin-personal-device)



## 在组织中针对 Windows 10 启用 BYOD
你可以将用户和员工设置为使用其个人 Windows 设备 (BYOD) 来访问公司应用与资源。你的用户可将其 Azure AD 帐户（工作或学校帐户）添加到个人 Windows 设备，以安全且合规的方式来访问资源。

### 先决条件
**在企业级别（管理员）**

*	Azure AD 订阅

**在用户级别**

*	Windows 10（专业版和企业版）


### 管理员任务

* [设置设备注册](/documentation/articles/active-directory-azureadjoin-setup)

### 用户任务
* [将个人 Windows 10 设备加入你的组织](/documentation/articles/active-directory-azureadjoin-personal-device)


## 其他信息
* [面向企业的 Windows 10：在工作中使用设备的方式](/documentation/articles/active-directory-azureadjoin-windows10-devices-overview)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](/documentation/articles/active-directory-azureadjoin-user-upgrade)
* [通过 Microsoft Passport 在不使用密码的情况下进行身份验证](/documentation/articles/active-directory-azureadjoin-passport)
* [了解 Azure AD Join 的使用方案](/documentation/articles/active-directory-azureadjoin-deployment-aadjoindirect)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](/documentation/articles/active-directory-azureadjoin-devices-group-policy)
* [设置 Azure AD Join](/documentation/articles/active-directory-azureadjoin-setup)

<!---HONumber=Mooncake_0613_2016-->
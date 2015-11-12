<properties 
	pageTitle="Azure AD Join 的使用方案和部署注意事项 | Microsoft Azure" 
	description="本主题说明管理员如何为其最终用户（员工、学生或其他用户）设置 Azure AD Join。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="08/02/2015" 
	wacn=""/>

# Azure AD Join 的使用方案和部署注意事项 

## Azure AD Join 的使用方案
方案 1：业务主要在云中进行
--------------------------------------------------------
如果你当前在云中操作和管理企业的标识，或者即将迁移到云，则你可以从 Azure AD Join 获益。你可以使用在 Azure AD 中创建的帐户来登录 Windows 10。通过[首次运行体验 (FRX) 过程](active-directory-azureadjoin-user-frx.md)或通过[设置体验](active-directory-azureadjoin-user-upgrade.md)添加 Azure AD，用户可将其计算机加入 Azure AD。用户现在可以在浏览器或 Office 应用程序中通过 SSO 访问其云资源（例如 Office 365）。

方案 2：教育机构
---------------------------------------------------------------------------------- 
教育机构通常有两种用户类型：教职员和学生。教职员被视为组织中较长期的成员，因此需要为他们创建本地帐户。学生是组织中期限较短的成员，因而可在 Azure AD 中进行管理，这样，就能将目录范围推送到云而不是本地。这些学生现在可以使用其 Azure AD 帐户登录 Windows，并在 Office 应用程序中获取 Office 365 资源的访问权限。

方案 3：零售业
---------------------------------------------------------------------------------------
零售业拥有季节性工作者和长期员工。可以将较长期的全职员工创建为本地帐户，而他们通常使用已加入域的计算机。但季节性工作者是组织中期限较短的成员，因而需要在可更轻松转移用户许可证的位置进行管理。在具有 Office 365 许可证的云中创建这些用户，让这些用户能够由于使用 Azure AD 帐户登录 Windows 和 Office 应用程序而获益，同时在他们离开公司之后对于他们的许可证保持更大的机动性。

方案 4：其他方案
------------------------------------------------------------------------------------------
除了上述方案以外，你还可以让用户添加已加入 Azure AD 的设备，通过简化的加入体验、Azure AD 中的设备管理、自动 MDM 注册以及单一登录到 Azure AD 和本地资源来获益。


## Azure AD Join 的部署注意事项

允许用户将公司拥有的设备直接加入 Azure AD
-----------------------------------------------------------------------------------------

企业可以为合作伙伴公司和组织提供仅限云的帐户。然后，这些合作伙伴可以轻松访问并单一登录到其公司应用与资源。此方案适用于主要使用设备访问云中依赖于 Azure AD 进行身份验证的资源（例如 Office 365 或 SaaS 应用）的用户。

### 先决条件
**在企业级别（管理员）**

*	Azure 订阅配合 Azure Active Directory  

**在用户级别**

*	Windows 10（Professional 和 Enterprise SKU）

### 管理员任务
* [设置设备注册和 MFA](active-directory-azureadjoin-setup.md)

### 用户任务
* [安装期间使用 Azure AD 设置新的 Windows 10 设备](active-directory-azureadjoin-user-frx.md)
* [通过“设置”使用 Azure AD 设置 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [将个人 Windows 10 设备加入你的组织](active-directory-azureadjoin-personal-device.md)
  


## 在组织中针对 Windows 10 启用 BYOD
你可以将用户和员工设置为使用其个人 Windows 设备来访问公司应用与资源。你的用户可通过将其 Azure AD 帐户（工作帐户）添加到个人 Windows 设备，以安全且合规的方式来访问工作资源。

### 先决条件
**在企业级别（管理员）**

*	Azure AD 订阅

**在用户级别**

*	Windows 10（Professional 和 Enterprise SKU）


### 管理员任务

* [设置设备注册和 MFA](active-directory-azureadjoin-setup)

### 用户任务
* [将个人 Windows 10 设备加入你的组织](active-directory-azureadjoin-personal-device)


## 其他信息
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade)
* [设置 Azure AD Join](active-directory-azureadjoin-setup)

<!---HONumber=79-->
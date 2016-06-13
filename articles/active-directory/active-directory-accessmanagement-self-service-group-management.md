<properties
	pageTitle="为自助服务应用程序访问管理设置 Azure Active Directory | Azure"
	description="用户通过自助组管理可以在 Azure Active Directory 中创建和管理安全组或 Office 365 组，还可以请求安全组或 Office 365 组成员身份。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags 
	ms.service="active-directory" 
	ms.date="03/17/2016"
	wacn.date=""/>

# 为自助组管理设置 Azure Active Directory

用户通过自助组管理可以在 Azure Active Directory (Azure AD) 中创建和管理安全组或 Office 365 组，还可以请求安全组或 Office 365 组成员身份，随后组所有者将批准或拒绝该请求。通过使用自助组管理功能，可以将组成员身份的日常控制委派给了解该成员身份的业务上下文的人员。自助组管理功能仅适用于安全组和 Office 365 组，而不适用于已启用邮件的安全组或通讯组列表。

自助组管理当前由两个基本方案组成：委派组管理和自助组管理。


- **委派组管理** - 我们以管理对公司所用 SaaS 应用程序的访问权限的管理员为例。由于有许多加入者和离开者，管理这些访问权限变得越来越繁琐，因此该管理员要求业务所有者创建一个新组。现在，该管理员为业务所有者刚创建的新组分配对该应用程序的访问权限，并将当前有权访问该应用程序的所有人都放入该组。然后，业务所有者可以添加更多用户，这些用户稍后将自动设置到该应用程序中。业务所有者无需等待管理员完成此工作，而是可以自己管理其用户的访问权限。该管理员可以为不同业务组的行政助理做同样的事情，现在业务所有者和该行政助理都可以管理其用户的访问权限，但无法接触或查看对方的用户。该管理员仍然可以看到有权访问该应用程序的所有用户，并可根据需要阻止访问权限。


- **自助组管理** - 以下是该方案的一个示例：两个用户都拥有独立设置的 SharePoint Online 站点，但他们都想为对方的团队提供访问权限。要实现此目的，他们在 Azure AD 中创建了一个组，然后每人在 SharePoint Online 中选取同一个组为他们的站点提供访问权限。当有人想要访问时，他们从访问面板发出请求，获得批准后便可自动访问这两个 SharePoint Online 站点。后来，他们中的一人决定，允许访问其站点的所有人也访问特定的 SaaS 应用程序。他要求该 SaaS 应用程序的管理员将该应用程序的访问权限添加到其站点。从那以后，他批准的任何请求将提供对这两个 SharePoint Online 站点以及该 SaaS 应用程序的访问权限。

## 为最终用户启用自助组管理功能

在 [Azure 经典门户](https://manage.windowsazure.cn)的“配置”选项卡上，将“委派组管理”设为“已启用”，然后将“用户可以创建安全组”或“用户可以创建 Office 组”设为“已启用”。

当启用了“用户可以创建安全组”选项时，将允许目录中的所有用户创建新的安全组并在这些组中添加成员。这些新组还会在访问面板中向所有其他用户显示，并且这些用户可以创建加入这些组的请求（如果组的策略设置允许创建这种请求）。如果已禁用“用户可以创建安全组”，则用户将无法创建组，并且无法更改他们拥有的现有组，但仍可管理这些组的成员身份，以及审批来自其他用户的加入这些组的请求。

你还可以使用“可以对安全组使用自助服务的用户”选项对用户的自助组管理功能实现更精细的访问控制。当启用了“用户可以创建组”选项时，将允许目录中的所有用户创建新的组并在这些组中添加成员。你还可以将“可以对安全组使用自助服务的用户”设为“部分”，仅允许有限的组用户使用组管理。当此开关设为“部分”时，将在你的目录中创建一个名为 SSGMSecurityGroupsUsers 的组，然后只有那些你已设为此组的成员的用户可以在你的目录中创建新安全组以及向这些组添加成员。你可以将“可以对安全组使用自助服务的用户”设为“全部”，允许你的目录中的所有用户创建新组。

你还可以使用“可以对安全组使用自助服务的组”框（默认设置为“SSGMSecurityGroupsUsers”）为某个组指定你自己的自定义名称，该组包含的所有用户均将有权使用自助服务，并可以在你的目录中创建新组。

## 其他信息

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)

* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)

<!---HONumber=Mooncake_0606_2016-->
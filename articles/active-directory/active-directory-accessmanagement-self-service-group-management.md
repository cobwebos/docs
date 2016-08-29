<properties
	pageTitle="为自助服务应用程序访问管理设置 Azure Active Directory | Azure"
	description="用户通过自助组管理可以在 Azure Active Directory 中创建和管理安全组或 Office 365 组，还可以请求安全组或 Office 365 组成员身份。"
	services="active-directory"
	documentationCenter=""
  	authors="curtand"
	manager="femila"
	editor=""
	/>

<tags 
	ms.service="active-directory" 
	ms.date="08/10/2016"
	wacn.date=""/>  


# 为自助组管理设置 Azure Active Directory

自助组管理可让用户在 Azure Active Directory (Azure AD) 中创建和管理安全组或 Office 365 组。用户也可以请求安全组或 Office 365 组成员身份，然后组所有者可以批准或拒绝成员身份。如此一来，可以将组成员身份的日常控制委托给了解该成员身份的业务上下文的人员。自助组管理功能仅适用于安全组和 Office 365 组，而不适用于已启用邮件的安全组或通讯组列表。

自助组管理当前由两个基本方案组成：委派组管理和自助组管理。

- **委托组管理** — 以管理对公司所用 SaaS 应用程序的访问权限的管理员为例。由于有许多加入者和离开者，管理这些访问权限变得越来越繁琐，因此该管理员要求业务所有者创建一个新组。管理员将该应用程序的访问权限分配给新组，并添加到所有人员已访问该应用程序的组。然后，业务所有者可以添加更多用户，而这些用户将自动预配到该应用程序中。业务所有者无需等待管理员管理用户的访问权限。如果管理员将相同的权限授予不同业务组中的管理员，则该人员也可以管理自己的用户的访问权限。业务所有者和管理员都无法查看或管理彼此的用户。该管理员仍然可以看到有权访问该应用程序的所有用户，并可根据需要阻止访问权限。

- **自助组管理** — 以下是该方案的一个示例：两个用户都拥有独立设置的 SharePoint Online 站点。他们都想为对方的团队提供对其站点的访问权限。若要实现此目的，他们可以在 Azure AD 中创建一个组，然后各自在 SharePoint Online 中选择该组并为该组提供对其站点的访问权限。当有人想要访问时，他们从访问面板发出请求，获得批准后便可自动访问这两个 SharePoint Online 站点。后来，他们中的一人决定，允许访问其站点的所有人也访问特定的 SaaS 应用程序。SaaS 应用程序的管理员可以将此应用程序的访问权限添加到 SharePoint Online 站点。从那以后，他批准的任何请求都将提供对这两个 SharePoint Online 站点以及该 SaaS 应用程序的访问权限。

## 为最终用户启用自助组管理功能

1. 在 [Azure 经典门户](https://manage.windowsazure.cn)中，打开 Azure AD 目录。

2. 在“配置”选项卡上，将“委托组管理”设置为“已启用”。

3. 将“用户可以创建安全组”或“用户可以创建 Office 组”设置为“已启用”。

启用“用户可以创建安全组”后，将允许目录中的所有用户创建新的安全组并在这些组中添加成员。这些新组也会显示在其他所有用户的“访问面板”中。如果组的策略设置允许，其他用户可以创建加入这些组的请求。如果“用户可以创建安全组”被禁用，用户将无法创建组，也无法更改其拥有的现有组。不过，他们仍然可以管理这些组的成员身份，并审批其他用户加入其组的请求。

你还可以使用“可对安全组使用自助服务的用户”对用户的自助组管理功能实现更精细的访问控制。启用“用户可以创建组”后，将允许目录中的所有用户创建新组并在这些组中添加成员。你还可以将“可对安全组使用自助服务的用户”设为“部分”，仅允许一组有限的用户使用组管理。将此开关设为“部分”后，必须先将用户添加到 SSGMSecurityGroupsUsers 组，他们才能创建新组并在这些组中添加成员。你可以将“可对安全组使用自助服务的用户”设为“全部”，允许目录中的所有用户创建新组。

也可以使用“可对安全组使用自助服务的组”框，为其成员可以使用自助服务的组指定自定义名称。

## 其他信息

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups/)

* [有关 Azure Active Directory 中应用程序管理的文章索引](/documentation/articles/active-directory-apps-index/)
* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis/)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect/)

<!---HONumber=Mooncake_0822_2016-->
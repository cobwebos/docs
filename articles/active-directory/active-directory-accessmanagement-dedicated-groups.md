<properties 
	pageTitle="Azure Active Directory 中的专用组 | Microsoft Azure" 
	description="概述专用组在 Azure Active Directory 中的工作原理及其创建方法。"
	services="active-directory" 
	documentationCenter="" 
	authors="curtand"
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.date="02/09/2016"
	wacn.date=""/>

# Azure Active Directory 中的专用组

在 Azure Active Directory 中，专用组和专用组的组成员身份都是自动创建的。无法通过 Azure 门户、Windows PowerShell cmdlet 或以编程方式在专用组中添加或删除成员。若要启用专用组，请在 Azure 门户中的“配置”选项卡上，将“启用专用组”开关设置为“是”。

一旦将“启用专用组”开关设置为“是”，你就可以通过将“启用‘所有用户’组”开关设置为“是”，来进一步使目录自动创建“所有用户”专用组。然后，你还可以编辑此专用组的名称，方法是在“‘所有用户’组的显示名称”字段中键入该名称。

如果你要向目录中的所有用户分配相同的权限，“所有用户”专用组可能十分有用。例如，可以通过向“所有用户”专用组分配对某个 SaaS 应用程序的访问权限，来授予目录中所有用户对该应用程序的访问权限。

请注意，“所有用户”专用组包含目录中的所有用户，当中有来宾和外部用户。如果需要排除外部用户的组，可以通过以下方法实现：使用类似下面的动态规则来创建组

(user.userPrincipalName -notContains "#EXT#@")

如果需要排除所有来宾的组，请使用类似下面的规则

(user.userType -ne "Guest")

本文将详细说明如何创建规则，以管理 Azure Active Directory 中组的成员：

* [创建简单规则以配置组的动态成员身份](/documentation/articles/active-directory-accessmanagement-simplerulegroup)


这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)

<!---HONumber=Mooncake_0411_2016-->
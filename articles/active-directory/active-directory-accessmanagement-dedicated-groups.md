<properties 
	pageTitle="Azure Active Directory 中的专用组 | Microsoft Azure" 
	description="概述 Azure AD 中的专用组及其创建方式。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.date="10/09/2015" 
	wacn.date=""/>

# Azure Active Directory 中的专用组

在 Azure Active Directory 中，专用组和专用组的组成员身份都是自动创建的。无法通过 Azure 管理门户、Windows PowerShell cmdlet 或以编程方式在专用组中添加或删除成员。若要启用专用组，请在 Azure 管理门户中的“配置”选项卡上，将“启用专用组”开关设置为“是”。

在当前的公共预览版中，一旦将“启用专用组”开关设置为“是”，你就可以通过将“启用‘所有用户’组”开关设置为“是”，来进一步使目录自动创建“所有用户”专用组。然后，你还可以编辑此专用组的名称，方法是在“‘所有用户’组的显示名称”字段中键入该名称。

如果你要向目录中的所有用户分配相同的权限，“所有用户”专用组可能十分有用。例如，可以通过向“所有用户”专用组分配对某个 SaaS 应用程序的访问权限，来授予目录中所有用户对该应用程序的访问权限。

下面这些主题提供了有关 Azure Active Directory 的其他一些信息

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)

* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)

<!---HONumber=79-->
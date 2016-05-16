
<properties
	pageTitle="使用组来管理对 SaaS 应用程序的访问 | Microsoft Azure"
	description="如何在 Azure Active Directory Premium 或 Basic 中使用组来分配与 Azure Active Directory 集成的 SaaS 应用程序的访问权限。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="03/17/2016"
	wacn.date=""/>


# 使用组来管理对 SaaS 应用程序的访问

通过 Azure AD Premium 或 Azure AD Basic 许可证使用 Azure Active Directory (Azure AD) 时，你可以使用组来分配与 Azure AD 集成的 SaaS 应用程序的访问权限。例如，如果要为营销部分配使用五个不同 SaaS 应用程序所需的访问权限，则可以创建一个包含销售部用户的组，然后将该组分配给营销部所需的这五个 SaaS 应用程序。这样就可以在一个位置管理营销部的成员身份，从而节省了时间。之后，当将用户添加为营销组的成员时，这些用户将分配到应用程序；当将用户从营销组中删除时，也会从应用程序中删除为其分配的权限。

此功能适用于可从 Azure AD 应用程序库中添加的数百个应用程序。

**为组分配对 SaaS 应用程序的访问权限**

1. 在 [Azure 经典门户](https://manage.windowsazure.cn)中，在左侧导航栏上选择“Active Directory”。

2. 选择“目录”选项卡，然后打开要在其中为组分配对 Saas 应用程序的访问权限的目录。

3. 选择“应用程序”选项卡。选择从应用程序库中添加的应用程序，然后单击“用户和组”选项卡。

4. 在“用户和组”选项卡的“开头为”字段中，输入要为其分配访问权限的组的名称，然后单击右上角的复选标记。你只需键入组名称的开头部分。

5. 选择组，然后选择“分配访问权限”按钮。当看见确认消息时选择“是”。目前应用程序的基于组的分配不支持嵌套的组成员身份。

6. 你还可以查看哪些用户分配到了该应用程序，无论这些用户是直接分配还是按组成员身份分配。为此，请将“显示”下拉列表从“组”更改为“所有用户”。此列表显示目录中的用户，以及每个用户是否已分配到该应用程序。此列表还显示分配的用户是直接分配到该应用程序（分配类型显示为“直接”），还是按组成员身份分配（分配类型显示为“继承”）。


> [AZURE.NOTE]
仅当启用了 Azure AD Premium 或 Azure AD Basic 后，才会看到“用户和组”选项卡。

##相关文章

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)

* [有关 Azure Active Directory 中应用程序管理的文章索引](/documentation/articles/active-directory-apps-index)

* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)
<!---HONumber=Mooncake_0509_2016-->
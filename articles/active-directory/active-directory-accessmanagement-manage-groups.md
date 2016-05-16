<properties

	pageTitle="在 Azure Active Directory 中管理安全组 | Microsoft Azure"
	description="如何创建和管理安全组以使用 Azure Active Directory 管理 Azure 资源访问。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.date="03/18/2016"
	wacn.date=""/>


#在 Azure Active Directory 中管理安全组

Azure Active Directory (Azure AD) 的主要功能之一是管理对资源的访问权限。这些资源可以是目录中的对象，或者是目录外部的资源，例如 SaaS 应用程序、Azure 服务、SharePoint 站点，又或者是本地资源。此外，资源所有者可以将资源的访问权限分配给 Azure AD 组。这样可以授予组成员访问资源的权限。然后，组所有者可以管理组的成员身份。实际上，资源所有者是将其资源的用户访问权限委派给了组的所有者。

## 如何创建安全组？

**创建组**

此任务可以使用 Office 365 帐户门户、Windows Intune 帐户门户或 Azure 门户完成，具体取决于你的组织订阅了哪些服务。有关使用门户管理 Azure Active Directory 的详细信息，请参阅[管理 Azure AD 目录](active-directory-administer.md)。

1. 在 [Azure 经典门户](https://manage.windowsazure.cn)中，选择“Active Directory”，然后选择你的组织的目录名称。

2. 选择“组”选项卡。

3. 选择“添加组”。

4. 在“添加组”窗口中，指定组的名称和说明。


## 如何在安全组中添加或删除单个用户？

**将单个用户添加到组**

1. 在 [Azure 经典门户](https://manage.windowsazure.cn)中，选择“Active Directory”，然后选择你的组织的目录名称。

2. 选择“组”选项卡。

3. 打开你要添加成员的组。默认情况下，这将显示所选组的“成员”选项卡。

4. 选择“添加成员”。

5. 在“添加成员”页上，选择要添加为该组成员的用户或组的名称，并确保该名称已添加到“选定”窗格中。


**从组中删除单个用户**

1. 在 [Azure 经典门户](https://manage.windowsazure.cn)中，选择“Active Directory”，然后选择你的组织的目录名称。

2. 选择“组”选项卡。

3. 打开你要从中删除成员的组。

4. 选择“成员”选项卡，选择要从该组中删除的成员的名称，然后单击“删除”。

6. 在要从组中删除此成员的提示中确认。


## 如何动态管理组的成员身份？

在 Azure AD 中，你可以非常轻松地设置一个简单的规则（该规则只有一个比较条件）来确定哪些用户将成为组的成员。例如，如果将一个组分配给 SaaS 应用程序，并且设置了一个规则用于添加职务为“Sales Rep”的用户，那么 Azure AD 目录中具有该职务的所有用户都有权访问此 SaaS 应用程序。

> [AZURE.NOTE] 你可以为安全组或 Office 365 组中的动态成员身份设置规则。目前应用程序的基于组的分配不支持嵌套的组成员身份。组的动态成员身份要求向以下人员分配 Azure AD Premium 许可证：
>- 管理组规则的管理员，
>- 以及由规则选定为组成员的所有用户。

**启用组的动态成员身份**

1. 在 [Azure 经典门户](https://manage.windowsazure.cn)中，选择“Active Directory”，然后选择你的组织的目录名称。

2. 选择“组”选项卡，然后打开要编辑的组。

3. 选择“配置”选项卡，然后将“启用动态成员身份”设置为“是”。

4. 为该组设置一个简单的规则，以控制该组的动态成员身份的运作方式。请确保已选中“添加符合以下条件的用户”选项，然后从列表中选择用户属性（例如部门、职务等），

5. 接下来选择条件（“不等于”、“等于”、“开头不为”、“开头为”、“不包含”、“包含”、“不匹配”、“匹配”），最后指定所选用户属性的值。

要了解如何为动态组成员身份创建高级规则（包含多个比较条件的规则），请参阅[使用属性创建高级规则](active-directory-accessmanagement-groups-with-advanced-rules.md)。

## 其他信息

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)

* [有关 Azure Active Directory 中应用程序管理的文章索引](/documentation/articles/active-directory-apps-index)

* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)
<!---HONumber=Mooncake_0509_2016-->
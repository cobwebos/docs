<properties
	pageTitle="创建简单规则以配置组的动态成员身份 | Microsoft Azure"
	description="说明如何创建简单规则以配置组的动态成员身份。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.date="02/09/2016"
	wacn.date=""/>


# 创建简单规则以配置组的动态成员身份

若要针对特定组启用动态成员身份，请执行以下步骤：

1. 在 Azure 门户中的“组”选项卡下，选择要编辑的组，然后在此组的“配置”选项卡中，将“启用动态成员身份”开关设置为“是”。


2. 现在，你可以为该组设置一个简单的规则，以控制动态成员身份如何对该组起作用。请确保已选中“添加符合以下条件的用户”单选按钮，然后从下拉菜单中选择用户属性（例如，department、jobTitle 等），

3. 接下来选择条件（“不等于”、“等于”、“开头不为”、“开头为”、“不包含”、“包含”、“不匹配”、“匹配”），最后指定所选用户属性的值。例如，如果为 SaaS 应用程序分配了一个组并通过设置一条规则（其中“添加符合以下条件的用户”设置为 jobTitle 等于 (-eq) “销售代表”）为该组启用动态成员身份，则 Azure AD 目录中职务设置为“销售代表”的所有用户都将有权访问该 SaaS 应用程序。

4. 请注意，你可以为安全组或 Office 组中的动态成员身份设置规则。组的动态成员身份要求向以下人员分配 Azure AD Premium 许可证：管理组规则的管理员，以及由规则选定为组成员的所有用户。

下面你将详细了解动态组成员身份的复杂规则：

* [使用属性创建高级规则](/documentation/articles/active-directory-accessmanagement-groups-with-advanced-rules)

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)
* [有关 Azure Active Directory 中应用程序管理的文章索引](/documentation/articles/active-directory-apps-index)
* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)


<!---HONumber=Mooncake_0411_2016-->
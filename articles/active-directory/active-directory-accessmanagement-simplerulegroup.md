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
	ms.date="10/09/2015" 
	wacn.date=""/>


# 创建简单规则以配置组的动态成员身份

**若要针对特定组启用动态成员身份，请执行以下步骤：**

1. 在 Azure 管理门户中的“组”选项卡下，选择要编辑的组，然后在此组的“配置”选项卡中，将“启用动态成员身份”开关设置为“是”。


2. 现在，你可以为该组设置一个简单的规则，以控制动态成员身份如何对该组起作用。请确保已选中“添加符合以下条件的用户”单选按钮，然后从下拉菜单中选择用户属性（例如，department、jobTitle 等），

3. 接下来选择条件（“不等于”、“等于”、“开头不为”、“开头为”、“不包含”、“包含”、“不匹配”、“匹配”），最后指定所选用户属性的值。例如，如果为 SaaS 应用程序分配了一个组并通过设置一条规则（其中“添加符合以下条件的用户”设置为 jobTitle 等于 (-eq) “销售代表”）为该组启用动态成员身份，则 Azure AD 目录中职务设置为“销售代表”的所有用户都将有权访问该 SaaS 应用程序。

下面这些主题提供了有关 Azure Active Directory 的其他一些信息

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)

* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)

<!---HONumber=79-->
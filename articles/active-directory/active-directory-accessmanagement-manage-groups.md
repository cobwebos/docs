<properties
	pageTitle="在 Azure Active Directory 中管理安全组 | Microsoft Azure"
	description="介绍如何注册 Azure，以及尝试使用 Azure AD 之前所要完成的最先几个步骤。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.date="07/13/2015" 
	wacn.date=""/>


# 在 Azure Active Directory 中管理安全组


## 如何创建和管理安全组

**通过 Azure 管理门户创建组**

1. 在管理门户中，单击“Active Directory”，然后单击你所在组织的目录的名称。
2. 单击“组”选项卡。
3. 在“组”页上，单击“添加组”。
4. 在“添加组”窗口中，指定组的名称和说明。
5. 此任务可以使用 Office 365 帐户门户、Windows Intune 帐户门户或 Microsoft Azure 管理门户完成，具体取决于你的组织已订阅了哪些服务。有关使用门户管理 Azure Active Directory 的详细信息，请参阅“管理 Azure AD 目录”。

## 如何在安全组中分配或删除用户

**通过 Azure 管理门户向组中添加成员**

1. 在管理门户中，单击“Active Directory”，然后单击你所在组织的目录的名称。
2. 单击“组”选项卡。
3. 在“组”页上，单击要将成员添加到的组的名称。默认情况下，这将显示所选组的“成员”选项卡。
4. 在该组的页上，单击“添加成员”。
5. 在“添加成员”页上，单击要添加为该组成员的用户或组的名称，并确保该名称已添加到“选定”窗格中。


**通过 Azure 管理门户删除组中的成员**

1. 在管理门户中，单击“Active Directory”，然后单击你所在组织的目录的名称。
2. 单击“组”选项卡。
3. 在“组”页上，单击要从中删除成员的组的名称。
4. 在该组的页上，单击“成员”选项卡。
5. 在该组的页上，单击要从该组中删除的成员的名称，然后单击“删除”。
6. 通过单击“是”来回答操作验证问题，确认你要从该组中删除此成员。


## 如何使用规则动态管理安全组的成员
**若要针对特定组启用动态成员身份，请执行以下步骤：**

1. 在 Azure 管理门户中的“组”选项卡下，选择要编辑的组，然后在此组的“配置”选项卡中，将“启用动态成员身份”开关设置为“是”。
2. 现在，你可以为该组设置一个简单的规则，以控制动态成员身份如何对该组起作用。请确保已选中“添加符合以下条件的用户”单选按钮，然后从下拉菜单中选择用户属性（例如，department、jobTitle 等）， 
3. 接下来选择条件（“不等于”、“等于”、“开头不为”、“开头为”、“不包含”、“包含”、“不匹配”、“匹配”），最后指定所选用户属性的值。
4. 例如，如果为 SaaS 应用程序分配了一个组（有关详细信息，请参阅“在 Azure AD 中为组分配对 SaaS 应用程序的访问权限”）并通过设置一条规则（其中“添加符合以下条件的用户”设置为 jobTitle 等于 (-eq) “销售代表”）为该组启用动态成员身份，则 Azure AD 目录中职务设置为“销售代表”的所有用户都将有权访问该 SaaS 应用程序。

下面这些主题提供了有关 Azure Active Directory 的其他一些信息

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)

* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)

<!---HONumber=79-->
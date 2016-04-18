
<properties 
	pageTitle="组的动态成员身份故障排除 | Microsoft Azure" 
	description="本主题列出了对 Azure AD 中组的动态成员成份进行故障排除的提示。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="02/09/2016"
	wacn.date=""/>


# 组的动态成员身份疑难解答

| 症状 | 操作 |
|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 我在组上配置了一个规则，但该组中的成员身份未更新 | 在“目录配置”选项卡中，检查“启用委派组管理”设置是否设为“是”。仅当你已使用分配有 Azure Active Directory Premium 许可证的用户登录时，才能看到此设置。检查规则中的用户属性值：是否有用户满足该规则？ |
| 我配置了一条规则，但现在却删除了该规则的现有成员 | 这是预期的行为。在启用或更改某个规则时，会删除组中的现有成员。评估规则后返回的用户将作为成员添加到组中。 |
| 我在添加或更改规则后未立即看到成员身份变化，这是为什么？ | 专用成员身份评估定期在异步后台进程中执行。该过程要花费多长时间取决于目录中的用户数，以及应用规则后创建的组的大小。通常，用户数较少的目录在几分钟内就能看到组成员身份变化。而具有大量用户的目录可能需要 30 分钟或更长时间才能填充信息。 |

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)



<!---HONumber=Mooncake_0411_2016-->
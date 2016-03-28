<properties
	pageTitle="删除 Azure Active Directory 中的自定义域 | Microsoft Azure"
	description="如何删除 Azure Active Directory 中的自定义域。"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"

	ms.date="02/05/2016"
	wacn.date=""/>


# 删除自定义域名

你可以删除不再需要配合 Azure AD 目录使用的自定义域名；例如，如果你有新的公司名称、你使用不同的 Azure AD 目录，等等。你也可以删除未验证的域，例如，如果你在添加之后发现名称输入错误，或者未根据域是否要联合来正确设置值。

## 开始之前

在删除域名之前，我们建议你阅读以下信息：

- 无法删除当你注册时为目录提供的原始 contoso.onmicrosoft.com 域名。
- 对于具有关联子域的任何顶级域，只有在删除这些子域后，才能删除这些顶级域。例如，如果 corp.adatum.com 或其他子域使用了顶级域名 adatum.com，则你无法删除该域名。如需详细信息，请参阅支持文章[尝试从 Office 365 中删除域时发生“域有关联的子域”或“无法删除具有子域的域”错误。](https://support.microsoft.com/kb/2787792/)
- 是否已激活目录同步？ 如果已激活，则会将类似于下面的域自动添加到你的帐户：contoso.mail.onmicrosoft.com。此域名不可删除。
- 在删除某个域名之前，必须先从与该域关联的所有用户或电子邮件帐户中删除该域名。你可以删除所有帐户，或者批量编辑用户帐户以更改其域名信息和电子邮件地址。有关详细信息，请参阅[在 Azure AD 中创建或编辑用户](active-directory-create-users.md)。请记得删除：

	-   用户名或电子邮件地址中有域的任何用户

	-   电子邮件地址中有域的任何已启用邮件的组

	-   域属于其回复 URL 一部分的任何应用程序

- 如果你在正用于 SharePoint Online 网站集的域名中托管 SharePoint Online 站点，则必须先删除网站集，然后才能删除该域名。

## 删除域

1.  使用具有该目录的全局管理员权限的帐户登录到 Azure 经典门户。

2.  打开你的目录，然后选择“域”。

3.  选择域，然后单击“删除”。

## 后续步骤

- [使用自定义域名来简化用户登录体验](active-directory-add-domain.md)
- [向“登录”和“访问面板”页添加公司品牌](active-directory-add-company-branding.md)
- [在 Azure Active Directory 中添加和验证自定义域名](active-directory-add-domain-add-verify-general.md)
- [将用户分配到自定义域](active-directory-add-domain-add-users.md)
- [更改自定义域名的 DNS 注册机构](active-directory-add-domain-change-registrar.md)

<!---HONumber=Mooncake_0321_2016-->
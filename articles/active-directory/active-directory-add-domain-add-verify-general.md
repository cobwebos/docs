<properties
	pageTitle="在 Azure Active Directory 中添加和验证自定义域名 | Microsoft Azure"
	description="在 Azure AD 入门过程中，学习如何将现有域添加到 Azure Active Directory。设置你的自定义域，以便与本地标识基础结构同步用户帐户信息。"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"

	ms.date="02/05/2016"
	wacn.date=""/>

# 在 Azure Active Directory 中添加和验证自定义域名

若要添加自定义域名并验证它是否可用于 Azure Active Directory，你必须完成以下步骤。

1.  执行下列操作之一：

    -   [添加要与本地 Active Directory 联合的自定义域名](#add-a-custom-domain-name-that-will-be-federated)

    -   [添加不与本地 Active Directory 联合的自定义域名](#add-a-custom-domain-name-that-will-not-be-federated)

2.  验证自定义域名。

    -   添加 DNS 条目，Azure AD 将使用这些条目在域的域名注册机构中验证你的组织是否拥有该自定义域名。

    -   在 Azure AD 中验证域。

## 添加要联合的自定义域名

有关使用 Azure AD Connect 进行本地目录集成的详细信息，请参阅

**将自定义域名添加到 Azure AD 目录**

1.  在 Azure AD 中，使用具有全局管理员权限的帐户登录到 [Azure 经典门户](https://manage.windowsazure.com/)。

2.  选择“Active Directory”。

3.  打开你的目录。

4.  选择“域”选项卡。

5.  在命令栏上，选择“添加”。

6.  输入自定义域名。请务必包含 .com 扩展名。

7.  选中“我计划配置此域为使用本地 Active Directory 进行单点登录”复选框。

8.  单击**“添加”**。

## 添加不会联合的自定义域名

大多数自定义域名是二级域“contoso.com”

**将自定义域名添加到 Azure AD 目录**

1.  在 Azure AD 中，使用具有全局管理员权限的帐户登录到 [Azure 经典门户](https://manage.windowsazure.com/)。

2.  选择“Active Directory”。

3.  打开你的目录。

4.  选择“域”选项卡。

5.  在命令栏上，选择“添加”。

6.  输入自定义域名。请务必包含 .com 扩展名。

7.  确保清除“我计划配置此域为使用本地 Active Directory 进行单点登录”复选框。

8.  选择“添加”。

9.  查看域是否已添加到目录。

## 在任一域名注册机构验证域

若要验证你的域，请在域名注册机构或 DNS 托管商那里创建一条 DNS 记录，然后，Azure AD 将使用该记录来确认你拥有该域。[有关在常见 DNS 注册机构中添加 DNS 条目的说明](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

如果你已向域名注册机构注册了域，则所需的 DNS 记录已存在。

如果已添加自定义域但尚未对其进行验证，该域的状态显示为“未验证”。

## 验证不会与本地目录联合的自定义域名
在域注册机构成功通过 DNS 系统添加针对域创建的任何记录之后，请执行以下操作：

1.  在 [Azure 经典门户](https://manage.windowsazure.com/)上的“Azure Active Directory”中，单击“域”。

2.  在“域”列表中，找到你要验证的域，然后根据所用的门户，单击“单击以验证域”或“验证”。

3.  遵照提供的说明完成验证过程。

    -   如果域验证成功，系统会通知你已将该域添加到你的帐户。

    -   如果域验证失败，可能需要更多的时间来传播你在域注册机构所做的任何更改。取消验证，然后重试。

如果对域做出更改的时间超过了 72 个小时，请登录域注册机构网站并验证输入的别名信息是否正确。如果输入的信息不正确，则必须删除不正确的 DNS 记录，然后使用正确的信息创建一条新记录。

## 验证自定义域是否已与本地目录联合

1.  下载并运行 Azure AD Connect。Azure AD Connect 工具将[提示你添加提供给你的 DNS 条目](active-directory-aadconnect-get-started-custom.md#select-the-azure-ad-domain-that-you-wish-to-federate)。

## 三级域名

你可以将三级域（例如“europe.contoso.com”）用于 Azure AD。添加和使用三级域：

1.  添加并验证二级域“contoso.com”

2.  将所有子域（例如“europe.contoso.com”）添加到 Azure AD。当你添加已验证的二级域的子域时，三级域将自动由 Azure AD 验证。无需添加任何其他 DNS 条目。

也可以使用 PowerShell 和 Graph 来完成这些步骤。

在验证域后，可将你的域配置为用于你的帐户。

## 后续步骤

- [使用自定义域名来简化用户登录体验](active-directory-add-domain.md)
- [向“登录”和“访问面板”页添加公司品牌](active-directory-add-company-branding.md)
- [将用户分配到自定义域](active-directory-add-domain-add-users.md)
- [更改自定义域名的 DNS 注册机构](active-directory-add-domain-change-registrar.md)
- [在 Azure Active Directory 中删除自定义域名](active-directory-add-domain-delete-domain.md)

<!---HONumber=Mooncake_0321_2016-->
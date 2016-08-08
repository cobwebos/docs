<properties
	pageTitle="无法登录管理 Azure 订阅 | Azure"
	description="说明某些常见 Azure 订阅登录问题的疑难解答信息"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor="na"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="genli"/>

# 无法登录管理 Azure 订阅

> [AZURE.NOTE] 如果在本文中有任何需要协助的地方，你可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。或者，你也可以在 [Azure 支持站点](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)上提出 Azure 支持事件。有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。

本文将探讨引发登录问题的部分常见原因。

## 你试图访问哪个门户？

帐户管理员只能访问[帐户中心](https://account.windowsazure.com/)，而服务管理员 (SA) 和共同管理员 (CA) 则只能访问 [Azure 门户](https://portal.azure.com)或 [Azure 经典门户](https://manage.windowsazure.com/)。

有关 Azure 管理员角色的详细信息，请参阅[如何添加或更改 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md)。

## 你的订阅是否与 Microsoft 帐户或组织帐户相关联？

你的 Microsoft 帐户是你使用的电子邮件地址，可以与密码一起用于登录任何 Windows Live 程序或服务，例如 Outlook、Hotmail、MSN 或 OneDrive。你可以使用属于你的任何电子邮件地址（包括公司电子邮件）来设置 Microsoft 帐户。有关更多详细信息，请参阅 [www.microsoft.com/account](http://www.microsoft.com/account)。

如果你的帐户与组织帐户相关联，则请选择正确的登录选项，如下所示。有关组织帐户使用方面的详细信息，请参阅[以组织身份注册 Azure](./active-directory/sign-up-organization.md)：

![登录页](./media/billing-cannot-login-subscription/signin.png)

## 共同管理员：你是否使用正确的帐户类型来管理其他帐户？

- 如果使用 Microsoft 帐户登录，则只能添加其他 Microsoft 帐户作为共同管理员。这是一项安全要求，可以防止非组织帐户发现某些帐户（例如 janedoe@contoso.com）是否有效。
- 如果你使用组织帐户登录，则可以添加你组织中的其他组织帐户作为共同管理员。例如，abby@contoso.com 可以添加 bob@contoso.com 作为服务管理员或共同管理员，但不能添加 john@notcontoso.com。使用组织帐户登录的用户还可以添加 Microsoft 帐户用户作为服务管理员或共同管理员。

可以使用组织帐户登录 Azure 以后，请注意对服务管理员 (SA) 和共同管理员 (CA) 帐户要求方面的下述更改：

| 登录方法| 添加 Microsoft 帐户作为共同管理员或服务管理员？ |添加相同组织中的组织帐户作为共同管理员或服务管理员？ |添加不同组织中的组织帐户作为共同管理员或服务管理员？
| ------------- | ------------- |---------------|---------------|
|Microsoft 帐户 |是|否|否|
|组织帐户|是|是|否|

## Internet 浏览器是否有问题？

尝试删除高速缓存/Cookie，使用 IE InPrivate 浏览模式，或者使用其他浏览器。

<!---HONumber=Mooncake_0801_2016-->
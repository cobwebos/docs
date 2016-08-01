<properties
	pageTitle="Azure AD .NET 协议概述 | Azure"
	description="如何使用 Azure AD，通过 HTTP 消息来授权访问租户中的 Web 应用程序和 Web API。"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="01/21/2016"
	wacn.date=""/>

<!--TODO: Introduction -->

## 将应用程序注册到 AD 租户

首先，你需要将应用程序注册到 Active Directory 租户。这将会使你获得应用程序的客户端 ID，并且可通过启用此 ID 来接收令牌。

- 登录到 Azure 管理门户。

- 在左侧的导航窗格中，单击“Active Directory”。

- 选择要在其中注册应用程序的租户。

- 单击“应用程序”选项卡，然后在底部抽屉中单击“添加”。

- 根据提示创建新的应用程序。本教程简要介绍了 Web 应用程序和本机应用程序的操作步骤，如果想要查看 Web 应用程序或本机应用程序的具体示例，请参阅[此处](/documentation/articles/active-directory-developers-guide/)的快速入门。

- 对于 Web 应用程序，请在用户登录页面（如 `http://localhost:12345`）提供“登录 URL”，即应用的基 URL。“应用程序 ID URI”是应用程序的唯一标识符。约定是使用 `https://<tenant-domain>/<app-name>`，例如 `https://contoso.onmicrosoft.com/my-first-aad-app`

- 对于本机应用程序，请提供“重定向 URI”，Azure AD 将用其返回令牌响应。输入特定于应用程序的值，例如 `http://MyFirstAADApp`

- 完成注册后，AAD 将为应用程序分配唯一的客户端标识符。在后面的部分中将会用到此值，因此，请在应用程序的“配置”选项卡中复制此值。

<!---HONumber=Mooncake_0725_2016-->
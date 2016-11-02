<properties
	pageTitle="Azure Active Directory B2C︰应用程序注册 | Microsoft Azure"
	description="如何将应用程序注册到 Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/30/2016"
	ms.author="swkrish"/>


# Azure Active Directory B2C：注册应用程序

## 先决条件

若要构建可接受使用者注册和登录的应用程序，首先需要使用 Azure Active Directory B2C 租户注册应用程序。通过使用[创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)中所述的步骤获取自己的租户。按照该文章中的所有步骤执行操作后，将具有固定到启动板的 B2C 功能边栏选项卡。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 导航到 B2C 功能边栏选项卡

如果你有固定到启动板的 B2C 功能边栏选项卡，则只要以 B2C 租户的全局管理员身份登录到 [Azure 门户](https://portal.azure.com/)，便可看到此边栏选项卡。

此外，可以通过依次单击 [Azure 门户](https://portal.azure.com/)左侧导航窗格中的“**浏览**”和“**Azure AD B2C**”访问此边栏选项卡。

> [AZURE.IMPORTANT] 你需要是能够访问 B2C 功能边栏选项卡的 B2C 租户全局管理员。任何其他租户的全局管理员或任何租户的用户均不能访问它。你可以通过使用 Azure 门户右上角中的租户切换器切换到 B2C 租户。

## 注册应用程序

1. 在 Azure 门户的 B2C 功能边栏选项卡上，单击“**应用程序**”。
2. 单击边栏选项卡顶部的“**+添加**”。
3. 输入应用程序的“**名称**”，用于向使用者描述你的应用程序。例如，你可以输入“Contoso B2C app”。
4. 如果你正在编写基于 Web 的应用程序，则将“**包括 Web 应用 / Web API**”开关切换到“**是**”。**回复 URL** 是 Azure AD B2C 将在其中返回应用程序请求的任何令牌的终结点。例如，输入 `https://localhost:44321/`。如果 Web 应用程序还将调用一些由 Azure AD B2C 保护的 Web API，则还将需要通过单击“**生成密钥**”按钮来创建“**应用程序密钥**”。

    > [AZURE.NOTE] **应用程序密钥**是一个重要的安全凭据，应进行适当地保护。

5. 如果你正在编写移动应用程序，则将“**包括本机客户端**”开关切换到“**是**”。复制系统自动为你创建的默认“**重定向 URI**”。
6. 单击“**创建**”以注册你的应用程序。
7. 单击刚刚创建的应用程序，并复制稍后将在代码中使用的全局唯一“**应用程序客户端 ID**”。

> [AZURE.IMPORTANT] 在 B2C 功能边栏选项卡中创建的应用程序必须在同一位置中进行管理。如果使用 PowerShell 或另一个门户编辑 B2C 应用程序，则此应用程序将不受支持且可能不适用于 Azure AD B2C。

## 构建快速启动应用程序

现在，你已将应用程序注册到 Azure AD B2C，接下来可以完成快速启动教程之一以启动并运行。以下是一些建议：

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0921_2016-->
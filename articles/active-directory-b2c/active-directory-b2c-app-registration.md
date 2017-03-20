---
title: "Azure Active Directory B2C：应用程序注册 | Microsoft Docs"
description: "如何将应用程序注册到 Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: bryanla
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/13/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 541849501335fb25d96cffa81b8119adc158cdd7
ms.lasthandoff: 03/14/2017


---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C：注册应用程序

> [!IMPORTANT]
> 从 Azure 门户中的 Azure AD B2C 边栏选项卡创建的应用程序必须从同一位置进行管理。 如果使用 PowerShell 或另一个门户编辑 B2C 应用程序，则此应用程序将不受支持且可能不适用于 Azure AD B2C。
> 
> 

## <a name="prerequisite"></a>先决条件
若要构建可接受使用者注册和登录的应用程序，首先需要使用 Azure Active Directory B2C 租户注册应用程序。 通过使用 [创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)中所述的步骤获取自己的租户。 按照该文章中的所有步骤执行操作后，将具有固定到启动板的 B2C 功能边栏选项卡。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>导航到 B2C 功能边栏选项卡
如果你有固定到启动板的 B2C 功能边栏选项卡，则只要以 B2C 租户的全局管理员身份登录到 [Azure 门户](https://portal.azure.com/) ，便可看到此边栏选项卡。

此外，还可以通过单击“更多服务”，然后搜索 [Azure 门户](https://portal.azure.com/)左侧导航窗格中的“Azure AD B2C”访问此边栏选项卡。

> [!IMPORTANT]
> 你需要是能够访问 B2C 功能边栏选项卡的 B2C 租户全局管理员。 任何其他租户的全局管理员或任何租户的用户均不能访问它。  可以通过 Azure 门户右上角的租户切换器切换到 B2C 租户。
> 
> 

## <a name="register-a-web-application"></a>注册 Web 应用程序
1. 在 Azure 门户的 B2C 功能边栏选项卡上，单击“ **应用程序**”。
2. 单击边栏选项卡顶部的“ **+添加** ”。
3. 输入应用程序的“ **名称** ”，用于向使用者描述你的应用程序。 例如，你可以输入“Contoso B2C app”。
4. 将“包括 Web 应用/Web API”开关切换到“是”。 **回复 URL** 是 Azure AD B2C 将在其中返回应用程序请求的任何令牌的终结点。 例如，输入 `https://localhost:44316/`。
5. 单击“保存”注册应用程序。
6. 单击刚刚创建的应用程序，并复制稍后将在代码中使用的全局唯一“ **应用程序客户端 ID** ”。


## <a name="register-a-web-api"></a>注册 Web API
1. 在 Azure 门户的 B2C 功能边栏选项卡上，单击“ **应用程序**”。
2. 单击边栏选项卡顶部的“ **+添加** ”。
3. 输入应用程序的“ **名称** ”，用于向使用者描述你的应用程序。 例如，可以输入“Contoso B2C api”。
4. 将“包括 Web 应用/Web API”开关切换到“是”。 **回复 URL** 是 Azure AD B2C 将在其中返回应用程序请求的任何令牌的终结点。 例如，输入 `https://localhost:44316/`。
5. 单击“保存”注册应用程序。
6. 单击刚刚创建的应用程序，并复制稍后将在代码中使用的全局唯一“ **应用程序客户端 ID** ”。


## <a name="register-a-mobilenative-application"></a>注册移动/本机应用程序
1. 在 Azure 门户的 B2C 功能边栏选项卡上，单击“ **应用程序**”。
2. 单击边栏选项卡顶部的“ **+添加** ”。
3. 输入应用程序的“ **名称** ”，用于向使用者描述你的应用程序。 例如，你可以输入“Contoso B2C app”。
4. 将“包括本机客户端”开关切换到“是”。
5. 输入使用自定义方案的**重定向 URI**。 例如，com.onmicrosoft.contoso.appname://redirect/path。 确保选择[适当的重定向 URI](#choosing-a-redirect-uri)。
6. 单击“保存”注册应用程序。
7. 单击刚刚创建的应用程序，并复制稍后将在代码中使用的全局唯一“ **应用程序客户端 ID** ”。

### <a name="choosing-a-redirect-uri"></a>选择重定向 URI
为移动/本机应用程序选择重定向 URI 时，有两个重要的考虑事项： 
* **唯一**：每个应用程序的重定向 URI 的方案应是唯一的。 在我们的示例 (com.onmicrosoft.contoso.appname://redirect/path) 中，我们使用 com.onmicrosoft.contoso.appname 作为方案。 建议遵循此模式。 如果两个应用程序共享同一方案，用户会看到“选择应用”对话框。 如果用户的选择不正确，登录将失败。 
* **完整**：重定向 URI 必须同时包含方案和路径。 路径必须在域后包含至少一个正斜杠（例如，//contoso/ 可以使用，//contoso 不能使用）。 

## <a name="build-a-quick-start-application"></a>构建快速启动应用程序
现在，你已将应用程序注册到 Azure AD B2C，接下来可以完成快速启动教程之一以启动并运行。 以下是一些建议：

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]



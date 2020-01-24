---
title: Microsoft 标识平台 & SAML 持有者断言流 |Microsoft
description: 了解如何从 Microsoft Graph 获取数据，而无需使用 SAML 持有者断言流提示用户提供凭据。
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 16f30473ded5f1de5dc94c1cff9da96165b1a01c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700203"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft 标识平台和 OAuth 2.0 SAML 持有者断言流
当客户端需要使用现有的信任关系时，可通过 OAuth 2.0 SAML 持有者断言流请求使用 SAML 断言的 OAuth 访问令牌。 应用于 SAML 断言的签名提供授权应用的身份验证。 SAML 断言是由标识提供程序颁发并由服务提供商使用的 XML 安全令牌。 服务提供程序依赖于其内容来标识断言的主题以了解安全相关的目的。

SAML 断言将发布到 OAuth 令牌终结点。  终结点根据应用的事先批准处理断言并颁发访问令牌。 客户端不需要具有或存储刷新令牌，也不需要将客户端密码传递给令牌终结点。

从 Microsoft Graph Api （仅支持委托权限）中提取数据时，SAML 持有者断言流非常有用，无需提示用户输入凭据。 在此方案中，客户端凭据 grant （这是后台进程的首选）不起作用。

对于执行基于浏览器的交互式登录以获取 SAML 断言并随后要添加对 OAuth 保护 API （如 Microsoft Graph）的访问权限的应用程序，可以发出 OAuth 请求，以获取 API 的访问令牌。 将浏览器重定向到 Azure AD 对用户进行身份验证时，浏览器将从 SAML 登录获取会话，并且用户无需输入其凭据。

对于与标识提供者（如与 Azure Active Directory 联合的 Active Directory 联合身份验证服务（ADFS））进行身份验证的用户，也支持 OAuth SAML 持有者断言流。  从 ADFS 获取的 SAML 断言可在 OAuth 流中用于对用户进行身份验证。

![OAuth 流](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>使用 SAML 持有者断言调用关系图
现在，让我们了解如何以编程方式获取 SAML 断言。 此方法通过 ADFS 进行测试。 但是，这适用于支持以编程方式返回 SAML 断言的任何标识提供者。 基本过程是：获取 SAML 断言，获取访问令牌，并 Microsoft Graph 访问。

### <a name="prerequisites"></a>必备组件

在授权服务器/环境（Microsoft 365）与标识提供程序之间建立信任关系，或 SAML 2.0 持有者断言（ADFS）的颁发者。 若要将 ADFS 配置为单一登录和标识提供者，可参阅[此文](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/)。

在[门户](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中注册应用程序：
1. 登录到门户的 "[应用注册" 边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)（请注意，我们使用的是 v2.0 终结点进行图形 API，因此需要在此门户中注册该应用程序。 否则，可以使用 Azure active directory 中的注册。 
1. 选择“新注册”。
1. “注册应用程序”页出现后，请输入应用程序的注册信息： 
    1. **名称**：输入一个会显示给应用用户的有意义的应用程序名称。
    1. **支持的帐户类型** - 选择希望应用程序支持的帐户。
    1. **重定向 URI （可选）** -选择你构建的应用类型、Web 或公用客户端（移动 & 桌面），然后输入应用程序的重定向 URI （或回复 URL）。
    1. 完成后，选择“注册”。
1. 记下应用程序（客户端） ID。
1. 在左侧窗格中，选择 "**证书" & "机密**"。 单击 "**客户端**密钥" 部分中的 "**新建客户端密码**"。 复制新的客户端密钥，在离开边栏选项卡时，你将无法进行检索。
1. 在左窗格中，选择 " **API 权限**"，然后单击 "**添加权限**"。 选择 " **Microsoft Graph**"，然后选择 "**委派权限**"，然后选择 "**任务"。请阅读**，因为我们打算使用 Outlook 图形 API。 

安装[Postman](https://www.getpostman.com/)，它是测试示例请求所需的工具。  稍后，你可以将请求转换为代码。

### <a name="get-the-saml-assertion-from-adfs"></a>从 ADFS 获取 SAML 断言
使用 SOAP 信封创建向 ADFS 终结点发出的 POST 请求，以便提取 SAML 断言：

![获取 SAML 断言](./media/v2-saml-bearer-assertion/2.png)

标头值：

![标头值](./media/v2-saml-bearer-assertion/3.png)

ADFS 请求正文：

![ADFS 请求正文](./media/v2-saml-bearer-assertion/4.png)

成功发布此请求后，你应该会收到来自 ADFS 的 SAML 断言。 只有**SAML： Assertion**标记数据是必需的，否则将其转换为 base64 编码以用于进一步的请求。

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>使用 SAML 断言获取 OAuth2 标记 
在此步骤中，使用 ADFS 断言响应提取 OAuth2 标记。

1. 按照以下标头值创建 POST 请求：

    ![POST 请求](./media/v2-saml-bearer-assertion/5.png)
1. 在请求的正文中，将替换**client_id**、 **client_secret**和**断言**（base64 编码的 SAML 断言获取了上一步）：

    ![请求正文](./media/v2-saml-bearer-assertion/6.png)
1. 成功请求后，你将从 Azure active directory 收到一个访问令牌。

### <a name="get-the-data-with-the-oauth-token"></a>获取包含 Oauth 令牌的数据

收到访问令牌后，调用图形 Api （在本示例中为 Outlook 任务）。 

1. 使用上一步骤中获取的访问令牌创建 GET 请求：

    ![GET 请求](./media/v2-saml-bearer-assertion/7.png)

1. 成功请求后，将收到 JSON 响应。

## <a name="next-steps"></a>后续步骤

了解不同的[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)。
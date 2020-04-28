---
title: Microsoft 标识平台 & SAML 持有者断言流 |Microsoft
description: 了解如何在不提示用户输入凭据的情况下，使用 SAML 持有者断言流从 Microsoft Graph 提取数据。
services: active-directory
author: umeshbarapatre
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1cd79b1f9e4cd3afadee250da0c184c0c5b8ac07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886171"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft 标识平台和 OAuth 2.0 SAML 持有者断言流
当客户端需要使用现有的信任关系时，你可以通过 OAuth 2.0 SAML 持有者断言流使用 SAML 断言请求 OAuth 访问令牌。 应用到 SAML 断言的签名提供已授权应用的身份验证。 SAML 断言是标识提供者颁发的 XML 安全令牌，由服务提供者使用。 出于安全相关的目的，该服务提供者依赖于其内容来识别断言的使用者。

SAML 断言将发布到 OAuth 令牌终结点。  该终结点根据应用的先前审批状态处理断言并颁发访问令牌。 客户端不需要使用或存储刷新令牌；不需要将客户端机密传递给令牌终结点。

可以使用 SAML 持有者断言流在不提示用户输入凭据的情况下，从 Microsoft Graph API（仅支持委托的权限）提取数据。 在此方案中，客户端凭据授予（最适合后台进程）不起作用。

对于在浏览器中以交互方式登录以获取 SAML 断言，然后需要添加对 OAuth 所保护 API（例如 Microsoft Graph）的访问权限的应用程序，可以发出 OAuth 请求来获取 API 的访问令牌。 将浏览器重定向到 Azure AD 以便对用户进行身份验证时，浏览器将从 SAML 登录中拾取会话，而用户无需输入其凭据。

对于使用已联合到 Active Directory 的标识提供者（例如 Azure Active Directory 联合身份验证服务 (ADFS)）进行身份验证的用户，也支持 OAuth SAML 持有者断言流。  可在 OAuth 流中使用从 ADFS 获取的 SAML 断言对用户进行身份验证。

![OAuth 流](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>使用 SAML 持有者断言的调用关系图
现在，让我们了解如何以编程方式实际提取 SAML 断言。 此方法已使用 ADFS 进行测试。 不过，此方法也适用于支持以编程方式返回 SAML 断言的任何标识提供者。 基本过程是：获取 SAML 断言，获取访问令牌，然后访问 Microsoft Graph。

### <a name="prerequisites"></a>必备条件

在授权服务器/环境 (Microsoft 365) 与标识提供者或 SAML 2.0 持有者断言 (ADFS) 颁发者之间建立信任关系。 若要将 ADFS 配置为单一登录和标识提供者，可参阅[此文](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/)。

在[门户](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中注册应用程序：
1. 登录到[门户的应用注册边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)（请注意，我们将对图形 API 使用 v2.0 终结点，因此需要在此门户中注册应用程序。 否则可以使用 Azure Active Directory 中的注册）。 
1. 选择“新注册”。 
1. “注册应用程序”页出现后，请输入应用程序的注册信息：  
    1. **名称**：输入一个会显示给应用用户的有意义的应用程序名称。
    1. **支持的帐户类型** - 选择希望应用程序支持的帐户。
    1. **重定向 URI （可选）** -选择你构建的应用类型、Web 或公用客户端（移动 & 桌面），然后输入应用程序的重定向 URI （或回复 URL）。
    1. 完成后，选择“注册”  。
1. 记下应用程序（客户端）ID。
1. 在左窗格中，选择“证书和机密”****。 在“客户端机密”部分单击“新建客户端机密”。******** 复制新客户端机密，因为在退出该边栏选项卡后不再可以检索它。
1. 在左窗格中，依次选择“API 权限”、“添加权限”。******** 依次选择“Microsoft Graph”、“委托的权限”、“Tasks.read”，因为我们打算使用 Outlook 图形 API。************ 

安装 [Postman](https://www.getpostman.com/)（用于测试示例请求的工具）。  稍后可将请求转换为代码。

### <a name="get-the-saml-assertion-from-adfs"></a>从 ADFS 获取 SAML 断言
使用 SOAP 信封创建向 ADFS 终结点的 POST 请求，以提取 SAML 断言：

![获取 SAML 断言](./media/v2-saml-bearer-assertion/2.png)

标头值：

![标头值](./media/v2-saml-bearer-assertion/3.png)

ADFS 请求正文：

![ADFS 请求正文](./media/v2-saml-bearer-assertion/4.png)

成功发布此请求后，你应会收到来自 ADFS 的 SAML 断言。 只需提供 **SAML:Assertion** 标记数据，请将其转换为 base64 编码，以便在后续请求中使用。

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>使用 SAML 断言获取 OAuth2 令牌 
此步骤使用 ADFS 断言响应提取 OAuth2 令牌。

1. 按如下所示创建包含标头值的 POST 请求：

    ![POST 请求](./media/v2-saml-bearer-assertion/5.png)
1. 在请求正文中，替换 **client_id**、**client_secret** 和 **assertion**（在上一步骤中获取的 base64 编码 SAML 断言）：

    ![请求正文](./media/v2-saml-bearer-assertion/6.png)
1. 成功发出请求后，你将收到来自 Azure Active Directory 的访问令牌。

### <a name="get-the-data-with-the-oauth-token"></a>使用 OAuth 令牌获取数据

收到访问令牌后，调用图形 API（在本示例中为 Outlook 任务）。 

1. 使用上一步骤中提取的访问令牌创建 GET 请求：

    ![GET 请求](./media/v2-saml-bearer-assertion/7.png)

1. 成功发出请求后，你将收到 JSON 响应。

## <a name="next-steps"></a>后续步骤

了解不同的[身份验证流和应用场景](authentication-flows-app-scenarios.md)。
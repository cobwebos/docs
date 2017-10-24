---
title: "Azure Active Directory B2C：使用客户端证书保护 RESTful 服务"
description: "演示如何使用客户端证书保护 Azure AD B2C 中自定义 REST API 声明交换的示例"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: a5cfe0e3f40b929e969e0a118939caa1ccb33cc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-client-certificates"></a>Azure Active Directory B2C：使用客户端证书保护 RESTful 服务
[创建 RESTful 服务](active-directory-b2c-custom-rest-api-netfw.md)用来与 Azure AD B2C 交互之后，我们将演示如何使用客户端证书限制对 Azure Web 应用 (RESTful API) 的访问。 此机制称为 TLS 相互身份验证或**客户端证书身份验证**。  只有包含适当证书的服务（例如 Azure AD B2C）能够访问你的服务。

> [!NOTE]
>
>也可以[使用 HTTP 基本身份验证](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)保护 RESTful 服务。 但应该知道，HTTP 基本身份验证的安全性被认为比客户端证书要低。 我们建议按本文中所述，使用客户端证书身份验证保护 RESTful 服务。

本文详细介绍如何：
1. 将 Web 应用设置为使用客户端证书身份验证
1. 将证书上传到 Azure AD B2C 策略密钥
1. 将自定义策略配置为使用客户端证书

## <a name="prerequisites"></a>先决条件
* 完成前一文章[集成 REST API 声明交换](active-directory-b2c-custom-rest-api-netfw.md)中所述的步骤
* 应准备好有效的证书（包含私钥的 .pfx 文件）

## <a name="step-1-configure-web-app-for-client-certificate-authentication"></a>步骤 1：将 Web 应用配置为使用客户端证书身份验证
若要将 **Azure 应用服务**设置为要求提供客户端证书，Web 应用的 `clientCertEnabled` 站点设置需为 true。 目前无法通过 Azure 门户中的管理体验进行此项设置，需要使用 REST API 来完成此项更改。

> [!NOTE]
>
>确保 Azure 应用服务计划使用标准或更高的层。 有关详细信息，请参阅 [Azure 应用服务计划深入概述](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)


使用 [Azure 资源浏览器（预览版）](https://resources.azure.com)将 clientCertEnabled 属性切换为 true。 以下屏幕截图演示如何通过 Azure 资源浏览器设置 clientCertEnabled 值![通过 Azure 资源浏览器设置 clientCertEnabled](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

> [!NOTE]
>
>有关如何设置 clientCertEnabled 属性的详细信息，请参阅：[如何为 Web 应用配置 TLS 相互身份验证](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)


> [!TIP]
>
>或者，可以使用 [ARMClient](https://github.com/projectkudu/ARMClient) 工具轻松创建 REST API 调用。

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>步骤 2：将证书上传到 Azure AD B2C 策略密钥
将 `clientCertEnabled` 设置为 `true` 之后，与 RESTful API 之间的通信现在需要客户端证书。 为此，需要在 Azure AD B2C 租户中上传并存储客户端证书。   
1.  转到 Azure AD B2C 租户，然后选择“B2C 设置” > “标识体验框架”
2.  选择“策略密钥”，查看租户中的可用密钥。
3.  单击“+添加”。
4.  对于“选项”，使用“上传”。
5.  使用 `B2cRestClientCertificate` 作为“名称”。  
    前缀 `B2C_1A_` 会自动添加。
6.  在“文件上传”中，选择包含私钥的证书 .pfx 文件。 提供证书的**密码**。

    ![上传策略密钥](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7.  单击“创建” 
8.  若要确认是否已创建密钥 `B2C_1A_B2cRestClientCertificate`，请选择“策略密钥”查看租户中的可用密钥。

## <a name="step-3-change-the-technicalprofile-to-support-client-certificate-authentication-in-your-extension-policy"></a>步骤 3：更改 `TechnicalProfile` 以支持扩展策略中的客户端证书身份验证
1.  从工作目录打开扩展策略文件 (TrustFrameworkExtensions.xml)。
2.  找到包含 `Id="REST-API-SignUp"` 的 `<TechnicalProfile>` 节点
3.  找到 `<Metadata>` 元素
4.  将 `AuthenticationType` 更改为 `ClientCertificate`

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5.  紧靠在 `<Metadata>` 元素右括号的后面添加以下 XML 片段：  

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

添加 XML 片段后，`TechnicalProfile` 应如下所示：

![设置 ClientCertificate 身份验证 XML 元素](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>步骤 4：将策略上传到租户

1.  在 [Azure 门户](https://portal.azure.com)中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，单击“Azure AD B2C”。
2.  选择“标识体验框架”。
3.  单击“所有策略”。
4.  选择“上传策略”
5.  选中“覆盖策略(若存在)”框。
6.  上传 TrustFrameworkExtensions.xml，并确保它能够通过验证

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>步骤 5：使用“立即运行”测试自定义策略
1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。

    >[!NOTE]
    >
    >    “立即运行”需要在租户中至少预先注册一个应用程序。 
    >    在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。

2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_signup_signin”。 选择“立即运行”。

3.  尝试在“名”字段中输入“Test”，B2C 会在页面顶部显示错误消息

    ![测试标识 API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  尝试在“名”字段中输入名称（不要输入“test”。 B2C 会注册该用户，然后将 loyaltyNumber 发送到应用程序。 请注意本示例中此 JWT 中的编号。

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

   > [!NOTE]
   >如果收到上述错误消息，表示 Azure AD B2C 在提供客户端证书时已成功调用 RESTful 服务。 下一步是验证证书。

## <a name="step-6-adding-certificate-validation"></a>步骤 6：添加证书验证
由 Azure AD B2C 发送到 RESTful 服务的客户端证书不会在 Azure Web 应用平台中经过任何验证（只会检查该证书是否存在）。 验证该证书是 Web 应用的责任。 下面是为了进行身份验证而验证证书属性的示例 ASP.NET 代码。

> [!NOTE]
>有关如何为 Azure 服务应用配置客户端证书身份验证的详细信息，请参阅：[如何为 Web 应用配置 TLS 相互身份验证](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)

### <a name="61-add-application-settings-to-projects-webconfig-file"></a>6.1：将应用程序设置添加到项目的 web.config 文件
打开前面创建的 Visual Studio 项目，将以下应用程序设置添加到 web.config 文件中的 `appSettings` 元素下面

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

替换证书的值：
* 使用者名称
* 颁发者名称
* 证书指纹

### <a name="62-add-isvalidclientcertificate-function"></a>6.2：添加 IsValidClientCertificate 函数
打开 Controllers\IdentityController.cs，将以下函数添加到 `Identity` 控制器类。 

```C#
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check time validity of certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check subject name of certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check issuer name of certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check thumprint of certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

在前面的**示例**中，仅当满足以下所有条件时，才认为该证书有效：
1. 在服务器上的当前时间，证书未过期且处于活动状态。
2. 证书的 `Subject` 名称具有与 `ClientCertificate:Subject` 应用程序设置值相等的公用名
3. 证书的 `Issuer` 名称具有与 `ClientCertificate:Issuer` 应用程序设置值相等的公用名
4. 证书的 `thumbprint` 等于 `ClientCertificate:Thumbprint` 应用程序设置值

> [!IMPORTANT]
>
>根据服务的敏感性，可能需要添加更多验证。 例如：测试证书是否链接到受信任根证书颁发机构、颁发者组织名称验证，等等。

### <a name="63-add-isvalidclientcertificate-function"></a>6.3：添加 IsValidClientCertificate 函数
打开 Controllers\IdentityController.cs，在 `SignUp()` 函数的开头添加以下代码行。 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

添加代码片段后，`Identity` 控制器应如下所示：

![添加证书验证代码](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-to-azure-and-test"></a>步骤 7：发布到 Azure 并测试
1. 在“解决方案资源管理器”中右键单击“Contoso.AADB2C.API”项目，选择“发布”。
2. 重复步骤 6，使用证书验证再次测试自定义策略。 尝试运行该策略，确保添加验证后一切正常
3. 现在，请在 web.config 文件中，将 `ClientCertificate:Subject` 的值更改为**无效**值。 再次运行该策略，应会看到错误消息。
4. 将值改回**有效**值，确保策略可以调用 REST API
5. 通过[使用 Application Insights 收集日志](active-directory-b2c-troubleshoot-custom.md)来排查问题。

## <a name="optional-download-the-complete-policy-files-and-code"></a>[可选] 下载完整的策略文件和代码
* 建议在完成自定义策略入门演练后，使用自己的自定义策略文件而不是这些示例文件来生成方案。  [参考示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)
* 可在此处下载完整代码：[Visual Studio 解决方案参考示例](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)
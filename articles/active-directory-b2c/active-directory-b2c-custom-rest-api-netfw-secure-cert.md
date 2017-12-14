---
title: "Azure Active Directory B2C：使用客户端证书保护 RESTful 服务"
description: "使用客户端证书保护 Azure AD B2C 中的自定义 REST API 声明交换"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 9547ba8c65360a03168ff1b6eba01038554e7fd3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>使用客户端证书保护 RESTful 服务
在相关的文章中，我们已[创建一个 RESTful 服务](active-directory-b2c-custom-rest-api-netfw.md)，该服务可与 Azure Active Directory B2C (Azure AD B2C) 交互。

本文介绍如何使用客户端证书来限制对 Azure Web 应用 (RESTful API) 的访问。 此机制称为 TLS 相互身份验证或客户端证书身份验证。 只有包含适当证书的服务（例如 Azure AD B2C）能够访问你的服务。

>[!NOTE]
>也可以[使用 HTTP 基本身份验证](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)保护 RESTful 服务。 但是，HTTP 基本身份验证的安全性被认为比客户端证书要低。 我们建议按本文中所述，使用客户端证书身份验证保护 RESTful 服务。

本文详细介绍如何：
* 将 Web 应用设置为使用客户端证书身份验证。
* 将证书上传到 Azure AD B2C 策略密钥。
* 将自定义策略配置为使用客户端证书。

## <a name="prerequisites"></a>先决条件
* 完成[集成 REST API 声明交换](active-directory-b2c-custom-rest-api-netfw.md)一文中所述的步骤。
* 获取有效的证书（包含私钥的 .pfx 文件）。

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>步骤 1：将 Web 应用配置为使用客户端证书身份验证
若要将 **Azure 应用服务**设置为要求提供客户端证书，请将 Web 应用的 `clientCertEnabled` 站点设置指定为 *true*。 若要进行此项更改，必须使用 REST API。 可通过 Azure 门户中的管理体验完成此项设置。 若要找到该项设置，请在 RESTful 应用程序的“设置”菜单中的“开发工具”下面，选择“资源浏览器”。

>[!NOTE]
>确保 Azure 应用服务计划使用标准或更高的层。 有关详细信息，请参阅 [Azure 应用服务计划深入概述](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)。


使用 [Azure 资源浏览器（预览版）](https://resources.azure.com)将 **clientCertEnabled** 属性设置为 *true*，如下图所示：

![通过 Azure 资源浏览器设置 clientCertEnabled](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

>[!NOTE]
>有关设置 **clientCertEnabled** 属性的详细信息，请参阅[为 Web 应用配置 TLS 相互身份验证](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth)。

>[!TIP]
>或者，可以使用 [ARMClient](https://github.com/projectkudu/ARMClient) 工具更轻松地编写 REST API 调用。

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>步骤 2：将证书上传到 Azure AD B2C 策略密钥
将 `clientCertEnabled` 设置为 *true* 之后，与 RESTful API 之间的通信需要客户端证书。 若要获取、上传以及在 Azure AD B2C 租户中存储客户端证书，请执行以下操作： 
1. 在 Azure AD B2C 租户中，选择“B2C 设置” > “标识体验框架”。

2. 若要查看租户中的可用密钥，请选择“策略密钥”。

3. 选择“添加”。  
    此时会打开“创建密钥”窗口。

4. 在“选项”框中，选择“上传”。

5. 在“名称”框中，键入 **B2cRestClientCertificate**。  
    前缀 *B2C_1A_* 会自动添加。

6. 在“文件上传”框中，选择包含私钥的证书 .pfx 文件。

7. 在“密码”框中，键入证书的密码。

    ![上传策略密钥](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. 选择“创建” 。

8. 若要查看租户中的可用密钥并确认是否已创建 `B2C_1A_B2cRestClientCertificate` 密钥，请选择“策略密钥”。

## <a name="step-3-change-the-technical-profile"></a>步骤 3：更改技术配置文件
若要支持自定义策略中的客户端证书身份验证，请执行以下操作来更改技术配置文件：

1. 在工作目录中，打开 *TrustFrameworkExtensions.xml* 扩展策略文件。

2. 搜索包含 `Id="REST-API-SignUp"` 的 `<TechnicalProfile>` 节点。

3. 找到 `<Metadata>` 元素。

4. 将 *AuthenticationType* 更改为 *ClientCertificate*，如下所示：

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. 紧靠在 `<Metadata>` 元素右括号的后面添加以下 XML 片段： 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    添加该片段后，技术配置文件应类似于以下 XML 代码：

    ![设置 ClientCertificate 身份验证 XML 元素](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>步骤 4：将策略上传到租户

1. 在 [Azure 门户](https://portal.azure.com)中，切换到[你的 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，然后选择“Azure AD B2C”。

2. 选择“标识体验框架”。

3. 选择“所有策略”。

4. 选择“上传策略”。

5. 选中“覆盖策略(如果存在)”复选框。

6. 上传 *TrustFrameworkExtensions.xml* 文件，并确保它能够通过验证。

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>步骤 5：使用“立即运行”测试自定义策略
1. 打开“Azure AD B2C 设置”，选择“标识体验框架”。

    >[!NOTE]
    >“立即运行”需要在租户中至少预先注册一个应用程序。 在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。

2. 打开已上传的信赖方 (RP) 自定义策略 **B2C_1A_signup_signin**，然后选择“立即运行”。

3. 在“名”框中键入 **Test** 来测试该过程。  
    Azure AD B2C 会在窗口顶部显示一条错误消息。    

    ![测试标识 API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. 在“名”框中键入一个名称（不要键入“Test”）。  
    Azure AD B2C 会注册该用户，然后将会员号发送到应用程序。 请注意此 JWT 示例中的编号：

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

   >[!NOTE]
   >如果收到“名称无效，请提供有效名称”错误消息，表示 Azure AD B2C 在提供客户端证书时已成功调用 RESTful 服务。 下一步是验证证书。

## <a name="step-6-add-certificate-validation"></a>步骤 6：添加证书验证
由 Azure AD B2C 发送到 RESTful 服务的客户端证书不会在 Azure Web 应用平台中经历验证，系统只会检查该证书是否存在。 验证该证书是 Web 应用的责任。 

本部分会添加示例 ASP.NET 代码，用于针对身份验证目的验证证书属性。

> [!NOTE]
>有关为 Azure 应用服务配置客户端证书身份验证的详细信息，请参阅[为 Web 应用配置 TLS 相互身份验证](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth)。

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1：将应用程序设置添加到项目的 web.config 文件
在前面创建的 Visual Studio 项目中，将以下应用程序设置添加到 *web.config* 文件中的 `appSettings` 元素后面：

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

将证书的“使用者名称”、“颁发者名称”和“证书指纹”值替换为自己的证书值。

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2：添加 IsValidClientCertificate 函数
打开 *Controllers\IdentityController.cs* 文件，将以下函数添加到 `Identity` 控制器类： 

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

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
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
    
    // 3. Check the issuer name of the certificate
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

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
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

在前面的示例代码中，仅当满足以下所有条件时，才认为该证书有效：
* 在服务器上的当前时间，证书未过期且处于活动状态。
* 证书的 `Subject` 名称等于 `ClientCertificate:Subject` 应用程序设置值。
* 证书的 `Issuer` 名称等于 `ClientCertificate:Issuer` 应用程序设置值。
* 证书的 `thumbprint` 等于 `ClientCertificate:Thumbprint` 应用程序设置值。

>[!IMPORTANT]
>根据服务的敏感性，可能需要添加更多验证。 例如，可能需要测试证书是否链接到受信任根证书颁发机构、颁发者组织名称验证，等等。

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3：调用 IsValidClientCertificate 函数
打开 *Controllers\IdentityController.cs* 文件，在 `SignUp()` 函数的开头添加以下代码片段： 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

添加该片段后，`Identity` 控制器应类似于以下代码：

![添加证书验证代码](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>步骤 7：将项目发布到 Azure 并对其进行测试
1. 在“解决方案资源管理器”中右键单击“Contoso.AADB2C.API”项目，选择“发布”。

2. 重复“步骤 6”，使用证书验证再次测试自定义策略。 尝试运行该策略，确保添加验证后一切正常。

3. 在 *web.config* 文件中，将 `ClientCertificate:Subject` 的值更改为**无效**值。 再次运行该策略，应会看到错误消息。

4. 将值改回**有效**值，确保策略可以调用 REST API。

如果需要对此步骤进行故障排除，请参阅[使用 Application Insights 收集日志](active-directory-b2c-troubleshoot-custom.md)。

## <a name="optional-download-the-complete-policy-files-and-code"></a>（可选）下载完整的策略文件和代码
* 完成[自定义策略入门](active-directory-b2c-get-started-custom.md)演练后，我们建议你使用自己的自定义策略文件来构建方案。 我们已提供[示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)用于参考。
* 可以从 [Visual Studio 解决方案参考示例](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)下载完整代码。 

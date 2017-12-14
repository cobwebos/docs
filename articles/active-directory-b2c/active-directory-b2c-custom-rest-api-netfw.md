---
title: "Azure Active Directory B2C：在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换"
description: "在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换。"
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
ms.date: 09/30/2017
ms.author: yoelh
ms.openlocfilehash: b7ce383b5297b0973f2999e7310fad94a0abe7dd
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换
使用构成 Azure Active Directory B2C (Azure AD B2C) 基础的标识体验框架，可在用户旅程中与 RESTful API 相集成。 本演练将会介绍 Azure AD B2C 如何与 .NET Framework RESTful 服务 (Web API) 交互。

## <a name="introduction"></a>介绍
使用 Azure AD B2C 可以通过调用 RESTful 服务，将自己的业务逻辑添加到用户旅程中。 标识体验框架在“输入声明”集合中将数据发送到 RESTful 服务，在“输出声明”集合中接收 RESTful 返回的数据。 使用 RESTful 服务集成，可以：

* **验证用户输入数据**：此操作防止将格式不当的数据保存到 Azure AD。 如果用户提供的值无效，RESTful 服务会返回错误消息，指示用户提供有效条目。 例如，可以验证用户提供的电子邮件地址是否在客户数据库中存在。
* **覆盖输入声明**：例如，如果用户使用全小写或全大写字母输入了名字，则你可以设置该名字的格式，只将第一个字母大写。
* **通过进一步与企业业务线应用程序集成来丰富用户数据**：RESTful 服务可以接收用户的电子邮件地址、查询客户的数据库，并向 Azure AD B2C 返回用户的会员号。 返回声明可以存储在用户 Azure AD 帐户中、在后续的业务流程步骤中进行评估，或包含在访问令牌中。
* **运行自定义业务逻辑**：可以发送推送通知、更新企业数据库、运行用户迁移过程、管理权限、审核数据库，以及执行其他操作。

可通过以下方式来设计与 RESTful 服务的集成：

* **验证技术配置文件**：对 RESTful 服务的调用在指定的技术配置文件的验证技术配置文件中发生。 在用户旅程推进之前，验证技术配置文件会验证用户提供的数据。 使用验证技术配置文件，可以：
   * 发送输入声明。
   * 验证输入声明并引发自定义错误消息。
   * 发回输出声明。

* **声明交换**：此设计与验证技术配置文件类似，不过它在业务流程步骤中发生。 此定义仅限于：
   * 发送输入声明。
   * 发回输出声明。

## <a name="restful-walkthrough"></a>RESTful 演练
在本演练中，我们将开发一个可以验证用户输入并提供用户会员号的 .NET Framework Web API。 例如，应用程序可以基于会员号授予“白金权益”访问权限。

概述：
* 开发 RESTful 服务 (.NET Framework Web API)。
* 在用户旅程中使用 RESTful 服务。
* 发送输入声明，并在代码中读取这些声明。
* 验证用户的名字。
* 发回会员号。 
* 将会员号添加到 JSON Web 令牌 (JWT)。

## <a name="prerequisites"></a>先决条件
完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

## <a name="step-1-create-an-aspnet-web-api"></a>步骤 1：创建 ASP.NET Web API

1. 在 Visual Studio 中，选择“文件” > “新建” > “项目”来创建项目。

2. 在“新建项目”窗口中，选择“Visual C#” > “Web” > “ASP.NET Web 应用程序(.NET Framework)”。

3. 在“名称”框中为该应用程序命名（例如 *Contoso.AADB2C.API*），选择“确定”。

    ![创建新的 Visual Studio 项目](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. 在“新建 ASP.NET Web 应用程序”窗口中，选择“Web API”或“Azure API 应用”模板。

    ![选择“Web API”模板](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. 确保身份验证设置为“无身份验证”。

6. 选择“确定”创建该项目。

## <a name="step-2-prepare-the-rest-api-endpoint"></a>步骤 2：准备 REST API 终结点

### <a name="step-21-add-data-models"></a>步骤 2.1：添加数据模型
模型代表 RESTful 服务中的输入声明和输出声明数据。 代码通过将输入声明模型从 JSON 字符串反序列化为 C# 对象（你的模型）来读取输入数据。 ASP.NET Web API 将输出声明模型自动反序列化回到 JSON，然后将序列化的数据写入 HTTP 响应消息的正文。 

执行以下操作，创建一个代表输入声明的模型：

1. 如果解决方案资源管理器尚未打开，请选择“视图” > “解决方案资源管理器”。 
2. 在“解决方案资源管理器”中，右键单击“模型”文件夹，选择“添加”，并选择“类”。

    ![添加模型](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. 将类命名为 `InputClaimsModel`，并将以下属性添加到 `InputClaimsModel` 类：

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. 创建新模型 `OutputClaimsModel`，并将以下属性添加到 `OutputClaimsModel` 类：

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. 再创建一个模型 `B2CResponseContent`，用于引发输入验证错误消息。 将以下属性添加到 `B2CResponseContent` 类，提供缺少的引用，并保存文件：

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>步骤 2.2：添加控制器
在 Web API 中，控制器是处理 HTTP 请求的对象。 该控制器返回输出声明；如果名字无效，该控制器会引发“HTTP 冲突”错误消息。

1. 在“解决方案资源管理器”中，右键单击“控制器”文件夹，选择“添加”，并选择“控制器”。

    ![添加新控制器](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. 在“添加基架”窗口中，选择“Web API 控制器 - 空”，再选择“添加”。

    ![选择“Web API 2 控制器 - 空”](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. 在“添加控制器”窗口中，将控制器命名为 **IdentityController**，选择“添加”。

    ![键入控制器名称](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    基架会在“控制器”文件夹中创建名为 *IdentityController.cs* 的文件。

4. 如果 *IdentityController.cs* 文件尚未打开，请双击它，并将该文件中的代码替换为以下代码：

    ```C#
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>步骤 3：将项目发布到 Azure
1. 在解决方案资源管理器中右键单击“Contoso.AADB2C.API”项目，选择“发布”。

    ![发布到 Microsoft Azure 应用服务](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. 在“发布”窗口中，依次选择“Microsoft Azure 应用服务”、“发布”。

    ![创建新的 Microsoft Azure 应用服务](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    此时会打开“创建应用服务”窗口。 在此窗口中，创建在 Azure 中运行 ASP.NET Web 应用所需的全部 Azure 资源。

    > [!NOTE]
    >有关发布方法的详细信息，请参阅[在 Azure 中创建 ASP.NET Web 应用](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)。

3. 在“Web 应用名称”框中，键入唯一的应用名称（有效字符为 a-z、0-9 和连字符 (-)）。 Web 应用的 URL 为 http://<app_name>.azurewebsites.NET，其中，*app_name* 是 Web 应用的名称。 可以接受自动生成的名称，它是唯一的。

    ![提供应用服务属性](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. 若要开始创建 Azure 资源，请选择“创建”。  
    创建 ASP.NET Web 应用后，向导会将该应用发布到 Azure，然后在默认浏览器中启动该应用。

6. 复制 Web 应用的 URL。

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>步骤 4：将新的 `loyaltyNumber` 声明添加到 TrustFrameworkExtensions.xml 文件的架构
尚未在架构中定义 `loyaltyNumber` 声明。 在 `<BuildingBlocks>` 元素（可在 *TrustFrameworkExtensions.xml* 文件的开头处找到此元素）中添加一个定义。

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>步骤 5：添加声明提供程序 
每个声明提供程序必须包含一个或多个用于确定终结点的技术配置文件，以及与该声明提供程序通信所需的协议。 

一个声明提供程序可出于由于各种原因包含多个技术配置文件。 例如，由于声明提供程序支持多个协议、终结点可以包含不同的功能，或者版本可以包含采用不同保障级别的声明，因此可以定义多个技术配置文件。 可以接受在一个用户旅程中发放敏感声明，但不接受在另一个用户旅程中发放此类声明。 

以下 XML 片段包含具有两个技术配置文件的声明提供程序节点：

* **TechnicalProfile Id="REST-API-SignUp"**：定义 RESTful 服务。 
   * `Proprietary` 描述为基于 RESTful 的提供程序的协议。 
   * `InputClaims` 定义要从 Azure AD B2C 发送到 REST 服务的声明。 

   在此示例中，声明 `givenName` 的内容作为 `firstName` 发送到 REST 服务，声明 `surname` 的内容作为 `lastName` 发送到 REST 服务，`email` 按原样发送。 `OutputClaims` 元素定义要从 RESTful 服务检索回到 Azure AD B2C 的声明。

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"**：将验证技术配置文件添加到现有技术配置文件（在基本策略中定义）。 在执行注册旅程期间，验证技术配置文件调用上述技术配置文件。 如果 RESTful 服务返回 HTTP 错误 409（冲突错误），会向用户显示错误消息。 

找到 `<ClaimsProviders>` 节点，然后在 `<ClaimsProviders>` 节点下添加以下 XML 片段：

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>步骤 6：将 `loyaltyNumber` 声明添加到信赖方策略文件，以便将该声明发送到应用程序
编辑 *SignUpOrSignIn.xml* 信赖方 (RP) 文件并修改 TechnicalProfile Id="PolicyProfile" 元素以添加如下内容：`<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`。

添加新声明后，信赖方代码如下所示：

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>步骤 7：将策略上传到租户

1. 在 [Azure 门户](https://portal.azure.com)中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，打开“Azure AD B2C”。

2. 选择“标识体验框架”。

3. 打开“所有策略”。 

4. 选择“上传策略”。

5. 选中“覆盖策略(如果存在)”复选框。

6. 上传 TrustFrameworkExtensions.xml 文件，并确保它能够通过验证。

7. 对 SignUpOrSignIn.xml 文件重复上述步骤。

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>步骤 8：使用“立即运行”测试自定义策略
1. 选择“Azure AD B2C 设置”并转到“标识体验框架”。

    > [!NOTE]
    > “立即运行”需要在租户中至少预先注册一个应用程序。 在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。

2. 打开已上传的信赖方 (RP) 自定义策略 **B2C_1A_signup_signin**，然后选择“立即运行”。

    ![B2C_1A_signup_signin 窗口](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. 在“名”框中键入 **Test** 来测试该过程。  
    Azure AD B2C 会在窗口顶部显示一条错误消息。

    ![测试策略](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  在“名”框中键入一个名称（不要键入“Test”）。  
    Azure AD B2C 会注册该用户，然后将 loyaltyNumber 发送到应用程序。 请注意此 JWT 中的编号。

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>（可选）下载完整的策略文件和代码
* 完成[自定义策略入门](active-directory-b2c-get-started-custom.md)演练后，我们建议你使用自己的自定义策略文件来构建方案。 我们已提供[示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)用于参考。
* 可以从 [Visual Studio 解决方案参考示例](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/)下载完整代码。
    
## <a name="next-steps"></a>后续步骤
* [使用基本身份验证（用户名和密码）保护 RESTful API](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [使用客户端证书保护 RESTful API](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

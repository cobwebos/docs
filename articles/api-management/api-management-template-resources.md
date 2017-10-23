---
title: "Azure API 管理模板资源 | Microsoft 文档"
description: "了解适用于 Azure API 管理中的开发人员门户模板的资源类型。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 212e7ea7bb2ffea63c7ba210195df0da38aa8f0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-template-resources"></a>Azure API 管理模板资源
Azure API 管理提供以下类型的资源，适用于开发人员门户模板。  
  
-   [字符串资源](#strings)  
  
-   [字形资源](#glyphs)  
  
##  <a name="strings"></a> 字符串资源  
 API 管理提供全面的适用于开发人员门户的字符串资源。 这些资源已本地化为所有受 API 管理支持的语言。 默认的一组模板将这些资源用于页眉、标签以及在开发人员门户中显示的任何常量字符串。 若要在模板中使用字符串资源，请提供资源字符串前缀，后跟字符串名称，如以下示例所示。  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 以下示例来自“产品列表”模板，在页面顶部显示“产品”。  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
 请参阅下面的表，了解适用于开发人员门户模板的字符串资源。 请使用表名作为该表中字符串资源的前缀。  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [文档](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a> ApisStrings  
  
|Name|文本|  
|----------|----------|  
|PageTitleApis|API|  
  
###  <a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Name|文本|  
|----------|----------|  
|WebApplicationsDetailsTitle|应用程序预览版|  
|WebApplicationsRequirementsHeader|要求|  
|WebApplicationsScreenshotAlt|屏幕快照|  
|WebApplicationsScreenshotsHeader|屏幕截图|  
  
###  <a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Name|文本|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|是否确实要删除应用程序?|  
|WebDevelopersAppNotPublished|未发布|  
|WebDevelopersAppNotSubminted|未提交|  
|WebDevelopersAppTableCategoryHeader|类别|  
|WebDevelopersAppTableNameHeader|Name|  
|WebDevelopersAppTableStateHeader|状态|  
|WebDevelopersEditLink|编辑|  
|WebDevelopersRegisterAppLink|注册应用程序|  
|WebDevelopersRemoveLink|删除|  
|WebDevelopersSubmitLink|提交|  
|WebDevelopersYourApplicationsHeader|应用程序|  
  
###  <a name="AppStrings"></a> AppStrings  
  
|Name|文本|  
|----------|----------|  
|WebApplicationsHeader|应用程序|  
  
###  <a name="CommonResources"></a> CommonResources  
  
|Name|文本|  
|----------|----------|  
|NoItemsToDisplay|找不到结果。|  
|GeneralExceptionMessage|出现问题。 可能存在临时故障或 Bug。 请重试。|  
|GeneralJsonExceptionMessage|出现问题。 可能存在临时故障或 Bug。 请重新加载页面，并重试。|  
|ConfirmationMessageUnsavedChanges|某些更改未保存。 是否确实要取消并放弃更改?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http 请求正文过大。|  
  
###  <a name="CommonStrings"></a> CommonStrings  
  
|Name|文本|  
|----------|----------|  
|ButtonLabelCancel|取消|  
|ButtonLabelSave|保存|  
|GeneralExceptionMessage|出现问题。 可能存在临时故障或 Bug。 请重试。|  
|NoItemsToDisplay|没有要显示的项。|  
|PagerButtonLabelFirst|第一个|  
|PagerButtonLabelLast|最后一个|  
|PagerButtonLabelNext|下一步|  
|PagerButtonLabelPrevious|上一步|  
|PagerLabelPageNOfM|第 {0} 页，共 {1} 页|  
|PasswordTooShort|密码过短|  
|EmailAsPassword|请勿使用电子邮件作为密码|  
|PasswordSameAsUserName|密码不能包含用户名|  
|PasswordTwoCharacterClasses|请使用不同的字符类|  
|PasswordTooManyRepetitions|重复过多|  
|PasswordSequenceFound|密码包含序列|  
|PagerLabelPageSize|页面大小|  
|CurtainLabelLoading|正在加载...|  
|TablePlaceholderNothingToDisplay|没有所选期间和范围的数据|  
|ButtonLabelClose|关闭|  
  
###  <a name="Documentation"></a> 文档  
  
|Name|文本|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|标头“{0}”无效|  
|WebDocumentationInvalidRequestErrorMessage|请求 URL 无效|  
|TextboxLabelAccessToken|访问令牌 *|  
|DropdownOptionPrimaryKeyFormat|主-{0}|  
|DropdownOptionSecondaryKeyFormat|辅-{0}|  
|WebDocumentationSubscriptionKeyText|订阅密钥|  
|WebDocumentationTemplatesAddHeaders|添加必需的 HTTP 标头|  
|WebDocumentationTemplatesBasicAuthSample|基本授权示例|  
|WebDocumentationTemplatesCurlForBasicAuth|适用于基本授权: --user {username}:{password}|  
|WebDocumentationTemplatesCurlValuesForPath|指定路径参数的值(显示为 {...})、订阅密钥以及查询参数的值|  
|WebDocumentationTemplatesDeveloperKey|指定订阅密钥|  
|WebDocumentationTemplatesJavaApache|此示例使用 HTTP 组件中的 Apache HTTP 客户端(http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|根据需要指定可选参数的值|  
|WebDocumentationTemplatesPhpPackage|此示例使用 HTTP_Request2 包。 (有关详细信息，请访问: http://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|根据需要指定路径参数的值(显示为 {...})和请求正文|  
|WebDocumentationTemplatesRequestBody|指定请求正文|  
|WebDocumentationTemplatesRequiredParams|指定以下必需参数的值|  
|WebDocumentationTemplatesValuesForPath|指定路径参数的值(显示为 {...})|  
|OAuth2AuthorizationEndpointDescription|授权终结点用于与资源所有者交互以及获取授权。|  
|OAuth2AuthorizationEndpointName|授权终结点|  
|OAuth2TokenEndpointDescription|客户端可以通过提供其授权或刷新令牌，使用令牌终结点获取访问令牌。|  
|OAuth2TokenEndpointName|令牌终结点|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p\> 客户端可以将资源所有者的用户代理定向到授权终结点，从而启动流。  客户端包括客户端标识符、请求的范围、本地状态和重定向 URI。在授予(或拒绝)访问权限以后，即可通过授权服务器将用户代理发送回该 URI。     </p\> <p\> 授权服务器会对资源所有者进行身份验证(通过用户代理)，并确定资源所有者是允许还是拒绝了客户端的访问请求。     </p\> <p\> 假定资源所有者授予了访问权限，授权服务器会使用先前在请求中或客户端注册过程中提供的重定向 URI，将用户代理重定向回客户端。  重定向 URI 包括授权代码以及客户端先前提供的任何本地状态。     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p\> 如果用户拒绝了访问请求或者请求无效，则会使用下述添加到重定向的参数通知客户端: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|授权请求|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p\>         若要启动 OAuth 过程，客户端应用必须将用户发送到授权终结点。          在授权终结点，用户会进行身份验证，并授予或拒绝对应用的访问权限。     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|</p\> 假定资源所有者授予了访问权限，授权服务器会使用先前在请求中或客户端注册过程中提供的重定向 URI，将用户代理重定向回客户端。  重定向 URI 包括授权代码以及客户端先前提供的任何本地状态。 </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p\> 客户端会包括上一步收到的授权代码，从授权服务器的令牌终结点请求访问令牌。  在发出请求时，客户端会通过授权服务器进行身份验证。  客户端会包括重定向 URI，用于获取进行验证的授权代码。 </p\> <p\> 授权服务器会对客户端进行身份验证，对授权代码进行验证，确保收到的重定向 URI 匹配用于在步骤(C)中重定向客户端的 URI。  如果验证有效，授权服务器会使用访问令牌进行响应，以及选择性地使用刷新令牌进行响应。 </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p\> 如果请求客户端身份验证失败或无效，授权服务器会使用“HTTP 400 (请求错误)”状态代码进行响应(除非另有指定)，并在响应中包括以下参数。 </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p\> 客户端向令牌终结点发出请求，方法是: 以 "application/x-www-form-urlencoded" 格式发送以下参数，在 HTTP 请求实体正文中采用 UTF-8 字符编码。 </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p\> 授权服务器颁发访问令牌和可选的刷新令牌，并构造响应，方法是: 将以下参数添加到 HTTP 响应的实体正文，使用的状态代码为“200 (正常)”。 </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p\> 客户端通过授权服务器进行身份验证，并从令牌终结点请求访问令牌。 </p\> <p\> 授权服务器对客户端进行身份验证，并在验证有效的情况下颁发访问令牌。 </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\> 如果请求的客户端身份验证失败或无效，授权服务器会使用“HTTP 400 (请求错误)”状态代码进行响应(除非另有指定)，并会在响应中包括以下参数。 </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p\> 客户端向令牌终结点发出请求，方法是: 以 "application/x-www-form-urlencoded" 格式添加以下参数，在 HTTP 请求实体正文中采用 UTF-8 字符编码。 </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\> 如果访问令牌请求有效且获得授权，则授权服务器会颁发访问令牌和可选的刷新令牌，并构造响应，方法是: 将以下参数添加到 HTTP 响应的实体正文，使用的状态代码为“200 (正常)”。 </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p\> 客户端可以将资源所有者的用户代理定向到授权终结点，从而启动流。  客户端包括客户端标识符、请求的范围、本地状态和重定向 URI。在授予(或拒绝)访问权限以后，即可通过授权服务器将用户代理发送回该 URI。 </p\> <p\> 授权服务器会对资源所有者进行身份验证(通过用户代理)，并确定资源所有者是允许还是拒绝了客户端的访问请求。 </p\> <p\> 假定资源所有者授予了访问权限，授权服务器会使用先前提供的重定向 URI，将用户代理重定向回客户端。  重定向 URI 在 URI 片段中包括访问令牌。 </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p\> 如果资源所有者拒绝了访问请求，或者请求失败，而原因并非重定向 URI 缺失或无效，则授权服务器会通知客户端，方法是: 使用 "application/x-www-form-urlencoded" 格式将以下参数添加到重定向 URI 的片段组件。 </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p\>     若要启动 OAuth 过程，客户端应用必须将用户发送到授权终结点。      在授权终结点，用户会进行身份验证，并授予或拒绝对应用的访问权限。 </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p\> 如果资源所有者允许了访问请求，则授权服务器会颁发访问令牌并将其提供给客户端，方法是: 使用 "application/x-www-form-urlencoded" 格式将以下参数添加到重定向 URI 的片段组件。 </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|对于能够保持其凭据机密性的客户端(例如，使用 PHP、Java、Python、Ruby、ASP.NET 等实现的 Web 服务器应用程序)，将对授权代码流进行优化。|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|授权代码授予|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|客户端凭据流适用于客户端(应用程序)请求访问其所控制的受保护资源的情况。 可以将客户端视为资源所有者，因此不需要最终用户交互。|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|客户端凭据授予|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|对于无法保持其凭据(已知会操作特定的重定向 URI)机密性的客户端，将对隐式流进行优化。 通常会在浏览器中使用脚本语言(例如 JavaScript)实现这些客户端。|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|隐式授权|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|资源所有者密码凭据流适用于资源所有者与客户端(应用程序)存在信任关系的情况，例如，设备操作系统或具有很高特权的应用程序均可充当客户端。 此流适用于能够获取资源所有者凭据(用户名和密码，通常使用交互形式)的客户端。|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|资源所有者密码凭据授予|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p\> 资源所有者为客户端提供用户名和密码。 </p\> <p\> 客户端会包括从资源所有者处收到的凭据，从授权服务器的令牌终结点请求访问令牌。  在发出请求时，客户端会通过授权服务器进行身份验证。 </p\> <p\> 授权服务器对客户端进行身份验证，同时验证资源所有者的凭据，并在验证有效的情况下颁发访问令牌。 </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\> 如果请求的客户端身份验证失败或无效，授权服务器会使用“HTTP 400 (请求错误)”状态代码进行响应(除非另有指定)，并会在响应中包括以下参数。 </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p\> 客户端向令牌终结点发出请求，方法是: 以 "application/x-www-form-urlencoded" 格式添加以下参数，在 HTTP 请求实体正文中采用 UTF-8 字符编码。 </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\> 如果访问令牌请求有效且获得授权，则授权服务器会颁发访问令牌和可选的刷新令牌，并构造响应，方法是: 将以下参数添加到 HTTP 响应的实体正文，使用的状态代码为“200 (正常)”。 </p\>|  
|OAuth2Step_AccessTokenRequest_Name|访问令牌请求|  
|OAuth2Step_AuthorizationRequest_Name|授权请求|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|必需。 由授权服务器颁发的访问令牌。|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|必需。 由授权服务器颁发的访问令牌。|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|必需。 由授权服务器颁发的访问令牌。|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|必需。 由授权服务器颁发的访问令牌。|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|必需。 客户端标识符。|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|如果客户端不使用授权服务器进行身份验证，则此项为必需。|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|必需。 客户端标识符。|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|必需。 授权服务器生成的授权代码。|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|必需。 从授权服务器接收的授权代码。|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|可选。 提供附加信息的用户可读 ASCII 文本。|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|可选。 提供附加信息的用户可读 ASCII 文本。|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|可选。 提供附加信息的用户可读 ASCII 文本。|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|可选。 提供附加信息的用户可读 ASCII 文本。|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|可选。 提供附加信息的用户可读 ASCII 文本。|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|可选。 一个 URI，用于标识提供有关此错误的信息的用户可读网页。|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|可选。 一个 URI，用于标识提供有关此错误的信息的用户可读网页。|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|可选。 一个 URI，用于标识提供有关此错误的信息的用户可读网页。|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|可选。 一个 URI，用于标识提供有关此错误的信息的用户可读网页。|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|可选。 一个 URI，用于标识提供有关此错误的信息的用户可读网页。|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|必需。 下述某个 ASCII 错误代码: invalid_request、unauthorized_client、access_denied、unsupported_response_type、invalid_scope、server_error、temporarily_unavailable。|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|必需。 下述某个 ASCII 错误代码: invalid_request、invalid_client、invalid_grant、unauthorized_client、unsupported_grant_type、invalid_scope。|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|必需。 下述某个 ASCII 错误代码: invalid_request、invalid_client、invalid_grant、unauthorized_client、unsupported_grant_type、invalid_scope。|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|必需。 下述某个 ASCII 错误代码: invalid_request、unauthorized_client、access_denied、unsupported_response_type、invalid_scope、server_error、temporarily_unavailable。|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|必需。 下述某个 ASCII 错误代码: invalid_request、invalid_client、invalid_grant、unauthorized_client、unsupported_grant_type、invalid_scope。|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|推荐。 访问令牌的生存期，以秒为单位。|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|推荐。 访问令牌的生存期，以秒为单位。|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|推荐。 访问令牌的生存期，以秒为单位。|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|推荐。 访问令牌的生存期，以秒为单位。|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|必需。 必须将值设置为 "authorization_code"。|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|必需。 必须将值设置为 "client_credentials"。|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|必需。 必须将值设置为 "password"。|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|必需。 资源所有者密码。|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|可选。 重定向终结点 URI 必须是绝对 URI。|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|如果 "redirect_uri" 参数已包括在授权请求中且其值必须相同，则此项为必需。|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|可选。 重定向终结点 URI 必须是绝对 URI。|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|可选。 刷新令牌，可用于获取新的访问令牌。|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|可选。 刷新令牌，可用于获取新的访问令牌。|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|可选。 刷新令牌，可用于获取新的访问令牌。|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|必需。 必须将值设置为 "code"。|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|必需。 必须将值设置为 "token"。|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|可选。 访问请求的范围。|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|如果与客户端请求的范围相同，则此项为可选；否则此项为必需。|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|可选。 访问请求的范围。|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|如果与客户端请求的范围相同，则此项为可选；否则此项为必需。|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|可选。 访问请求的范围。|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|如果与客户端请求的范围相同，则此项为可选；否则此项为必需。|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|可选。 访问请求的范围。|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|如果与客户端请求的范围相同，则此项为可选；否则此项为必需。|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|如果客户端授权请求中存在 "state" 参数，则此项为必需。  从客户端收到的确切值。|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|推荐。 一个不透明值，可供客户端用来保持请求和回调之间的状态。  在将用户代理重定向回客户端时，授权服务器会包括此值。  应使用此参数防止跨站点请求伪造。|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|如果客户端授权请求中存在 "state" 参数，则此项为必需。  从客户端收到的确切值。|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|如果客户端授权请求中存在 "state" 参数，则此项为必需。  从客户端收到的确切值。|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|推荐。 一个不透明值，可供客户端用来保持请求和回调之间的状态。  在将用户代理重定向回客户端时，授权服务器会包括此值。  应使用此参数防止跨站点请求伪造。|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|如果客户端授权请求中存在 "state" 参数，则此项为必需。  从客户端收到的确切值。|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|必需。 已颁发令牌的类型。|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|必需。 已颁发令牌的类型。|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|必需。 已颁发令牌的类型。|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|必需。 已颁发令牌的类型。|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|必需。 资源所有者用户名。|  
|OAuth2UnsupportedTokenType|不支持令牌类型“{0}”。|  
|OAuth2InvalidState|来自授权服务器的响应无效|  
|OAuth2GrantType_AuthorizationCode|授权代码|  
|OAuth2GrantType_Implicit|隐式|  
|OAuth2GrantType_ClientCredentials|客户端凭据|  
|OAuth2GrantType_ResourceOwnerPassword|资源所有者密码|  
|WebDocumentation302Code|302 已找到|  
|WebDocumentation400Code|400 (请求错误)|  
|OAuth2SendingMethod_AuthHeader|授权标头|  
|OAuth2SendingMethod_QueryParam|查询参数|  
|OAuth2AuthorizationServerGeneralException|通过 {0} 授予访问权限时出错|  
|OAuth2AuthorizationServerCommunicationException|无法建立到授权服务器的 HTTP 连接，或者已意外关闭该连接。|  
|WebDocumentationOAuth2GeneralErrorMessage|发生意外错误。|  
|AuthorizationServerCommunicationException|出现授权服务器通信异常。 请与管理员联系。|  
|TextblockSubscriptionKeyHeaderDescription|订阅密钥，用于访问此 API。 在<a href='/developer'\>配置文件</a\>中查找。|  
|TextblockOAuthHeaderDescription|从 <i\>{0}</i\> 获取的 OAuth 2.0 访问令牌。 支持的授权类型: <i\>{1}</i\>。|  
|TextblockContentTypeHeaderDescription|发送到 API 的正文的媒体类型。|  
|ErrorMessageApiNotAccessible|此时无法访问正在尝试调用的 API。 请在<a href="/issues"\>此处</a\>联系 API 发布者。|  
|ErrorMessageApiTimedout|正在尝试调用的 API 获得响应所花的时间超出正常。 请在<a href="/issues"\>此处</a\>联系 API 发布者。|  
|BadRequestParameterExpected|“应使用“{0}”参数”|  
|TooltipTextDoubleClickToSelectAll|双击全选。|  
|TooltipTextHideRevealSecret|显示/隐藏|  
|ButtonLinkOpenConsole|试用|  
|SectionHeadingRequestBody|请求正文|  
|SectionHeadingRequestParameters|请求参数|  
|SectionHeadingRequestUrl|请求 URL|  
|SectionHeadingResponse|响应|  
|SectionHeadingRequestHeaders|请求标头|  
|FormLabelSubtextOptional|可选|  
|SectionHeadingCodeSamples|代码示例|  
|TextblockOpenidConnectHeaderDescription|从 <i\>{0}</i\> 获得的 OpenID Connect ID 令牌。 支持的授权类型: <i\>{1}</i\>。|  
  
###  <a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Name|文本|  
|----------|----------|  
|LinkLabelBack|返回|  
|LinkLabelHomePage|主页|  
|LinkLabelSendUsEmail|给我们发送一封电子邮件|  
|PageTitleError|抱歉，提供请求的页面时出现问题|  
|TextblockPotentialCauseIntermittentIssue|这可能是已经消失的间歇性数据访问问题。|  
|TextblockPotentialCauseOldLink|单击的链接可能为旧链接，不再指向正确的位置。|  
|TextblockPotentialCauseTechnicalProblem|我们这边可能存在技术问题。|  
|TextblockPotentialSolutionRefresh|请尝试刷新页面。|  
|TextblockPotentialSolutionStartOver|从我们的 {0} 重新开始。|  
|TextblockPotentialSolutionTryAgain|转到 {0}，并重试执行的操作。|  
|TextReportProblem|{0}，描述已发生的错误，我们会尽快进行查看。|  
|TitlePotentialCause|可能原因|  
|TitlePotentialSolution|这可能只是暂时性问题，可以进行多项尝试|  
  
###  <a name="IssuesStrings"></a> IssuesStrings  
  
|Name|文本|  
|----------|----------|  
|WebIssuesIndexTitle|问题|  
|WebIssuesNoActiveSubscriptions|没有活动的订阅。 需要先订阅产品，才能报告问题。|  
|WebIssuesNotSignin|没有登录。 请{0}，以便报告问题或发表评论。|  
|WebIssuesReportIssueButton|报告问题|  
|WebIssuesSignIn|登录|  
|WebIssuesStatusReportedBy|状态: {0} &#124; 由 {1} 报告|  
  
###  <a name="NotFoundStrings"></a> NotFoundStrings  
  
|Name|文本|  
|----------|----------|  
|LinkLabelHomePage|主页|  
|LinkLabelSendUsEmail|给我们发送一封电子邮件|  
|PageTitleNotFound|对不起，找不到要查找的页面|  
|TextblockPotentialCauseMisspelledUrl|可能拼错了 URL (如果 URL 是键入的)。|  
|TextblockPotentialCauseOldLink|单击的链接可能为旧链接，不再指向正确的位置。|  
|TextblockPotentialSolutionRetype|尝试重新键入 URL。|  
|TextblockPotentialSolutionStartOver|从我们的 {0} 重新开始。|  
|TextReportProblem|{0}，描述已发生的错误，我们会尽快进行查看。|  
|TitlePotentialCause|可能原因|  
|TitlePotentialSolution|可能的解决方案|  
  
###  <a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Name|文本|  
|----------|----------|  
|WebProductsAgreement|订阅 {0} 产品即表明我同意`<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`。|  
|WebProductsLegalTermsLink|使用条款|  
|WebProductsSubscribeButton|订阅|  
|WebProductsUsageLimitsHeader|使用限制|  
|WebProductsYouAreNotSubscribed|已订阅此产品。|  
|WebProductsYouRequestedSubscription|已请求订阅此产品。|  
|ErrorYouNeedtoAgreeWithLegalTerms|必须同意使用条款，才能继续。|  
|ButtonLabelAddSubscription|添加订阅|  
|LinkLabelChangeSubscriptionName|更改|  
|ButtonLabelConfirm|确认|  
|TextblockMultipleSubscriptionsCount|有以下产品的 {0} 个订阅:|  
|TextblockSingleSubscriptionsCount|有以下产品的 {0} 个订阅:|  
|TextblockSingleApisCount|以下产品包含 {0} 个API:|  
|TextblockMultipleApisCount|以下产品包含 {0} 个API:|  
|TextblockHeaderSubscribe|订阅产品|  
|TextblockSubscriptionDescription|将按以下方式创建新订阅:|  
|TextblockSubscriptionLimitReached|已达订阅限制。|  
  
###  <a name="ProductsStrings"></a> ProductsStrings  
  
|Name|文本|  
|----------|----------|  
|PageTitleProducts|产品|  
  
###  <a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Name|文本|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|此时管理员已禁用登录。|  
|TextboxExternalIdentitiesSigninInvitation|或者，在登录时使用|  
|TextboxExternalIdentitiesSigninInvitationPrimary|在登录时使用:|  
  
###  <a name="SigninResources"></a> SigninResources  
  
|Name|文本|  
|----------|----------|  
|PrincipalNotFound|找不到主体或签名无效|  
|ErrorSsoAuthenticationFailed|SSO 身份验证失败|  
|ErrorSsoAuthenticationFailedDetailed|提供的令牌无效，或者无法验证签名。|  
|ErrorSsoTokenInvalid|SSO 令牌无效|  
|ValidationErrorSpecificEmailAlreadyExists|电子邮件“{0}”已注册|  
|ValidationErrorSpecificEmailInvalid|电子邮件“{0}”无效|  
|ValidationErrorPasswordInvalid|密码无效。 请更正错误，并重试。|  
|PropertyTooShort|{0} 过短|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|电子邮件地址无效。|  
|ValidationMessageNewPasswordConfirmationRequired|确认新密码|  
|ValidationErrorPasswordConfirmationRequired|确认密码为空|  
|WebAuthenticationEmailChangeNotice|更改确认电子邮件正在发送至 {0}。 请按照其中的说明确认新的电子邮件地址。 如果该电子邮件没有在随后的几分钟内到达收件箱，请查看垃圾邮件文件夹。|  
|WebAuthenticationEmailChangeNoticeHeader|已成功处理电子邮件更改请求|  
|WebAuthenticationEmailChangeNoticeTitle|已请求更改电子邮件|  
|WebAuthenticationEmailHasBeenRevertedNotice|电子邮件已存在。 已还原请求|  
|ValidationErrorEmailAlreadyExists|电子邮件已存在|  
|ValidationErrorEmailInvalid|电子邮件地址无效|  
|TextboxLabelEmail|电子邮件|  
|ValidationErrorEmailRequired|电子邮件是必需的。|  
|WebAuthenticationErrorNoticeHeader|错误|  
|WebAuthenticationFieldLengthErrorMessage|{0} 必须是最大长度 {1}|  
|TextboxLabelEmailFirstName|名字|  
|ValidationErrorFirstNameRequired|名字是必需的。|  
|ValidationErrorFirstNameInvalid|名字无效|  
|NoticeInvalidInvitationToken|请注意，确认链接的有效时间仅为 48 小时。 如果仍在此时间范围内，请确保链接正确。 如果链接已过期，则请重复正在尝试确认的操作。|  
|NoticeHeaderInvalidInvitationToken|邀请令牌无效|  
|NoticeTitleInvalidInvitationToken|确认错误|  
|WebAuthenticationLastNameInvalidErrorMessage|姓氏无效|  
|TextboxLabelEmailLastName|姓氏|  
|ValidationErrorLastNameRequired|姓氏是必需的。|  
|WebAuthenticationLinkExpiredNotice|发送给确认链接已过期。 `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|密码重置链接无效或已过期。|  
|WebAuthenticationLinkExpiredNoticeTitle|链接已发送|  
|WebAuthenticationNewPasswordLabel|新密码|  
|ValidationMessageNewPasswordRequired|新密码是必需的。|  
|TextboxLabelNotificationsSenderEmail|通知发件人电子邮件|  
|TextboxLabelOrganizationName|组织名称|  
|WebAuthenticationOrganizationRequiredErrorMessage|组织名称为空|  
|WebAuthenticationPasswordChangedNotice|已成功更新密码|  
|WebAuthenticationPasswordChangedNoticeTitle|密码已更新|  
|WebAuthenticationPasswordCompareErrorMessage|密码不匹配|  
|WebAuthenticationPasswordConfirmLabel|确认密码|  
|ValidationErrorPasswordInvalidDetailed|密码过弱。|  
|WebAuthenticationPasswordLabel|密码|  
|ValidationErrorPasswordRequired|密码是必需的。|  
|WebAuthenticationPasswordResetSendNotice|更改密码确认电子邮件正在发送至 {0}。 请按电子邮件中的说明继续密码更改过程。|  
|WebAuthenticationPasswordResetSendNoticeHeader|已成功处理密码重置请求|  
|WebAuthenticationPasswordResetSendNoticeTitle|已请求密码重置|  
|WebAuthenticationRequestNotFoundNotice|找不到请求|  
|WebAuthenticationSenderEmailRequiredErrorMessage|通知发件人电子邮件为空|  
|WebAuthenticationSigninPasswordLabel|请输入密码，确认所做的更改|  
|WebAuthenticationSignupConfirmNotice|注册确认电子邮件正在发送至 {0}。<br /\> 请按电子邮件中的说明激活帐户。<br /\> 如果电子邮件没有在随后的几分钟内到达收件箱，请查看垃圾邮件文件夹。|  
|WebAuthenticationSignupConfirmNoticeHeader|已成功创建帐户|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|注册确认电子邮件已再次发送|  
|WebAuthenticationSignupConfirmNoticeTitle|创建的帐户|  
|WebAuthenticationTokenRequiredErrorMessage|令牌为空|  
|WebAuthenticationUserAlreadyRegisteredNotice|似乎某个用户已使用此电子邮件在系统中注册。 如果忘记了密码，请尝试还原该密码，或者与我们的支持团队联系。|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|用户已注册|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|已注册|  
|ButtonLabelChangePassword|更改密码|  
|ButtonLabelChangeAccountInfo|更改帐户信息|  
|ButtonLabelCloseAccount|关闭帐户|  
|WebAuthenticationInvalidCaptchaErrorMessage|输入的文本与图片上的文本不匹配。 请重试。|  
|ValidationErrorCredentialsInvalid|电子邮件或密码无效。 请更正错误，并重试。|  
|WebAuthenticationRequestIsNotValid|请求无效|  
|WebAuthenticationUserIsNotConfirm|请在尝试登录之前确认注册。|  
|WebAuthenticationInvalidEmailFormated|电子邮件无效: {0}|  
|WebAuthenticationUserNotFound|找不到用户|  
|WebAuthenticationTenantNotRegistered|帐户所属的 Azure Active Directory 租户无权访问此门户。|  
|WebAuthenticationAuthenticationFailed|身份验证失败。|  
|WebAuthenticationGooglePlusNotEnabled|身份验证失败。 如果已向应用程序授权，则请联系管理员，确保已正确配置 Google 身份验证。|  
|ValidationErrorAllowedTenantIsRequired|租户必须获得允许|  
|ValidationErrorTenantIsNotValid|Azure Active Directory 租户“{0}”无效。|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|请使用 {0} 帐户登录|  
|WebAuthenticationUserLimitNotice|此服务已达到允许的用户的最大数目。 请`<a href="mailto:{0}"\>contact the administrator</a\>`，让其升级服务并重新启用用户注册。|  
|WebAuthenticationUserLimitNoticeHeader|已禁用用户注册|  
|WebAuthenticationUserLimitNoticeTitle|已禁用用户注册|  
|WebAuthenticationUserRegistrationDisabledNotice|管理员已禁用用户注册。 请使用外部标识提供者登录。|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|已禁用用户注册|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|已禁用用户注册|  
|WebAuthenticationSignupPendingConfirmationNotice|在创建帐户之前，我们需验证电子邮件地址。 我们已将电子邮件发送至 {0}。 请按电子邮件中的说明激活帐户。 如果该电子邮件没有在随后的几分钟内到达，请查看垃圾邮件文件夹。|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|我们发现电子邮件地址为 {0} 的帐户未确认。 为了创建帐户，我们需验证电子邮件地址。 我们已将电子邮件发送至 {0}。 请按电子邮件中的说明激活帐户。 如果该电子邮件没有在随后的几分钟内到达，请查看垃圾邮件文件夹|  
|WebAuthenticationSignupConfirmationAlmostDone|即将完成|  
|WebAuthenticationSignupConfirmationEmailSent|我们已将电子邮件发送至 {0}。 请按电子邮件中的说明激活帐户。 如果该电子邮件没有在随后的几分钟内到达，请查看垃圾邮件文件夹。|  
|WebAuthenticationEmailSentNotificationMessage|电子邮件已成功发送至 {0}|  
|WebAuthenticationNoAadTenantConfigured|尚未为服务配置 Azure Active Directory 租户。|  
|CheckboxLabelUserRegistrationTermsConsentRequired|我同意`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`。|  
|TextblockUserRegistrationTermsProvided|请查看`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|使用条款|  
|ValidationMessageConsentNotAccepted|必须同意使用条款，才能继续。|  
  
###  <a name="SigninStrings"></a> SigninStrings  
  
|Name|文本|  
|----------|----------|  
|WebAuthenticationForgotPassword|忘记密码了?|  
|WebAuthenticationIfAdministrator|如果是管理员，则必须在`<a href="{0}"\>here</a\>`登录。|  
|WebAuthenticationNotAMember|还不是成员? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|在此计算机上记住我|  
|WebAuthenticationSigininWithPassword|使用用户名和密码登录|  
|WebAuthenticationSigninTitle|登录|  
|WebAuthenticationSignUpNow|立即注册|  
  
###  <a name="SignupStrings"></a> SignupStrings  
  
|Name|文本|  
|----------|----------|  
|PageTitleSignup|注册|  
|WebAuthenticationAlreadyAMember|已经是成员?|  
|WebAuthenticationCreateNewAccount|创建新的 API 管理帐户|  
|WebAuthenticationSigninNow|立即登录|  
|ButtonLabelSignup|注册|  
  
###  <a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Name|文本|  
|----------|----------|  
|SubscriptionCancelConfirmation|是否确实要取消此订阅?|  
|SubscriptionRenewConfirmation|是否确实要续订此订阅?|  
|WebDevelopersManageSubscriptions|管理订阅|  
|WebDevelopersPrimaryKey|主密钥|  
|WebDevelopersRegenerateLink|重新生成|  
|WebDevelopersSecondaryKey|辅助密钥|  
|ButtonLabelShowKey|显示|  
|ButtonLabelRenewSubscription|续订|  
|WebDevelopersSubscriptionReqested|请求日期: {0}|  
|WebDevelopersSubscriptionRequestedState|已请求|  
|WebDevelopersSubscriptionTableNameHeader|Name|  
|WebDevelopersSubscriptionTableStateHeader|状态|  
|WebDevelopersUsageStatisticsLink|分析报告|  
|WebDevelopersYourSubscriptions|订阅|  
|SubscriptionPropertyLabelRequestedDate|请求日期:|  
|SubscriptionPropertyLabelStartedDate|开始日期:|  
|PageTitleRenameSubscription|重命名订阅|  
|SubscriptionPropertyLabelName|订阅名称|  
  
###  <a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Name|文本|  
|----------|----------|  
|SectionHeadingCloseAccount|想要关闭帐户?|  
|PageTitleDeveloperProfile|配置文件|  
|ButtonLabelHideKey|隐藏|  
|ButtonLabelRegenerateKey|重新生成|  
|InformationMessageKeyWasRegenerated|是否确实要重新生成此密钥?|  
|ButtonLabelShowKey|显示|  
  
###  <a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Name|文本|  
|----------|----------|  
|ButtonLabelUpdateProfile|更新配置文件|  
|PageTitleUpdateProfile|更新帐户信息|  
  
###  <a name="UserProfile"></a> UserProfile  
  
|Name|文本|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|更改帐户信息|  
|ButtonLabelChangePassword|更改密码|  
|ButtonLabelCloseAccount|关闭帐户|  
|TextboxLabelEmail|电子邮件|  
|TextboxLabelEmailFirstName|名字|  
|TextboxLabelEmailLastName|姓氏|  
|TextboxLabelNotificationsSenderEmail|通知发件人电子邮件|  
|TextboxLabelOrganizationName|组织名称|  
|SubscriptionStateActive|活动|  
|SubscriptionStateCancelled|已取消|  
|SubscriptionStateExpired|Expired|  
|SubscriptionStateRejected|已拒绝|  
|SubscriptionStateRequested|已请求|  
|SubscriptionStateSuspended|已挂起|  
|DefaultSubscriptionNameTemplate|{0} (默认值)|  
|SubscriptionNameTemplate|开发人员访问权限 {0}|  
|TextboxLabelSubscriptionName|订阅名称|  
|ValidationMessageSubscriptionNameRequired|订阅名称不能为空。|  
|ApiManagementUserLimitReached|此服务已达到允许的用户的最大数目。 请升级到更高的定价层。|  
  
##  <a name="glyphs"></a> 字形资源  
 API 管理开发人员门户模板可以使用 [Bootstrap 提供的 Glyphicon](http://getbootstrap.com/components/#glyphicons) 中的字形。 此字形集包括 250 多个字形，采用 [Glyphicon](http://glyphicons.com/) Halflings 集中的字体格式。 若要使用此集中的字形，请使用以下语法。  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 如需字形的完整列表，请参阅 [Bootstrap 提供的 Glyphicon](http://getbootstrap.com/components/#glyphicons)。

## <a name="next-steps"></a>后续步骤
如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](api-management-developer-portal-templates.md)。

---
title: "Azure API 管理中的页模板 | Microsoft Docs"
description: "了解如何在 Azure API 管理中使用一组模板自定义开发人员门户页面的内容。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 77fd7b5b339a8ede8a297bec96f91f0a243cc18d
ms.openlocfilehash: d3c947ec67a6acba3058afcef36a18bf5debd55d
ms.contentlocale: zh-cn
ms.lasthandoff: 01/12/2017

---
# <a name="page-templates-in-azure-api-management"></a>Azure API 管理中的页模板
通过 Azure API 管理，用户能够使用一组用于配置内容的模板自定义开发人员门户页面的内容。 使用 [DotLiquid](http://dotliquidmarkup.org/) 语法和所选编辑器（例如 [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)），以及提供的一组本地化[字符串资源](api-management-template-resources.md#strings)、[字形资源](api-management-template-resources.md#glyphs)和[页面控件](api-management-page-controls.md)，即可根据这些模板的使用需要非常灵活地配置页面内容。  
  
 本部分中的模板可用于自定义开发人员门户中的登录、注册和未找到页面的页面内容。  
  
-   [登录](#SignIn)  
  
-   [注册](#SignUp)  
  
-   [找不到页面](#PageNotFound)  
  
> [!NOTE]
>  示例默认模板包含在后续文档中，但是会因持续改进而有所更改。 通过导航到所需单个模板，可在开发人员门户中查看实时的默认模板。 如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)。  
  
##  <a name="SignIn"></a>登录  
 **登录**模板用于自定义开发人员门户中的登录页面。  
  
 ![登录页](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM 登录页开发人员门户模板")  
  
### <a name="default-template"></a>默认模板  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>控制  
 此模板使用以下[页面控件](api-management-page-controls.md)。  
  
-   [basic-signin](api-management-page-controls.md#basic-signin)  
  
-   [providers](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>数据模型  
 [用户登录](api-management-template-data-model-reference.md#UseSignIn)实体。  
  
### <a name="sample-template-data"></a>示例模板数据  
  
```json  
{  
    "Email": null,  
    "Password": null,  
    "ReturnUrl": null,  
    "RememberMe": false,  
    "RegistrationEnabled": true,  
    "DelegationEnabled": false,  
    "DelegationUrl": null,  
    "SsoSignUpUrl": null,  
    "AuxServiceUrl": "https://manage.windowsazure.com/#Workspaces/ApiManagementExtension/service/contoso5/dashboard",  
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
    ],  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsEnabled": false  
}  
```  
  
##  <a name="SignUp"></a>注册  
 **注册**模板用于自定义开发人员门户中的注册页面。  
  
 ![注册页](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM 注册页开发人员门户模板")  
  
### <a name="default-template"></a>默认模板  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>控制  
 此模板使用以下[页面控件](api-management-page-controls.md)。  
  
-   [sign-up](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>数据模型  
 [用户注册](api-management-template-data-model-reference.md#UserSignUp)实体。  
  
### <a name="sample-template-data"></a>示例模板数据  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="PageNotFound"></a>找不到页面  
 **找不到页面**模板用于自定义开发人员门户中的“找不到页面”页面。  
  
 ![找不到页面](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM 找不到页面开发人员门户模板")  
  
### <a name="default-template"></a>默认模板  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>控制  
 此模板可能不使用任何[页面控件](api-management-page-controls.md)。  
  
### <a name="data-model"></a>数据模型  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|referenceCode|字符串|因内部错误显示页面时生成的代码。|  
|errorCode|字符串|因内部错误显示页面时生成的代码。|  
|emailBody|字符串|因内部错误显示页面时生成的电子邮件正文。|  
|requestedUrl|字符串|找不到页面时请求的 URL。|  
|referrerUrl|字符串|所请求的 URL 的引用 URL。|  
  
### <a name="sample-template-data"></a>示例模板数据  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>后续步骤
如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](api-management-developer-portal-templates.md)。

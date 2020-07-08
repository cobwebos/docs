---
title: 有关自定义策略的开发人员说明
titleSuffix: Azure AD B2C
description: 有关使用自定义策略配置和维护 Azure AD B2C 的开发人员说明。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7defc33f8e3e2daca60fa5c15c41c62333ffc2d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389252"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>有关 Azure Active Directory B2C 中的自定义策略的开发人员说明

Azure Active Directory B2C 中的自定义策略配置现已正式发布。 此配置方法面向构建复杂标识解决方案的高级标识开发人员。 自定义策略使得 Identity Experience Framework 的强大功能可在 Azure AD B2C 租户中使用。
使用自定义策略的高级标识开发人员应该投入一段时间来完成演练并阅读参考文档。

大部分可用的自定义策略选项现已正式发布，不过，技术配置文件类型和内容定义 API 等基础功能仍处于软件生命周期的不同阶段。 有更多的选项会推出正式版。 下表以更高的粒度级指定了可用性级别。

## <a name="features-that-are-generally-available"></a>已推出正式版的功能

- 使用自定义策略创作并上传自定义身份验证用户旅程。
    - 将用户旅程逐步描述为声明提供程序之间的交换。
    - 在用户旅程中定义条件分支。
- 在自定义身份验证用户旅程中与已启用 REST API 的服务互操作。
- 与符合 OpenIDConnect 协议标准的标识提供者联合。
- 与遵守 SAML 2.0 协议的标识提供者联合。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自定义策略功能集开发人员的责任

手动策略配置授予对 Azure AD B2C 基础平台的较低访问级别，因此要求创建唯一的信任框架。 自定义标识提供者、信任关系、与外部服务的集成以及分步工作流的诸多可能组合方式要求在设计和配置方面采用有条理的方法。

使用自定义策略功能集的开发人员应遵守以下指导原则：

- 熟悉自定义策略和密钥/机密管理的配置语言。 有关详细信息，请参阅 [TrustFrameworkPolicy](trustframeworkpolicy.md)。
- 取得方案和自定义集成的所有权。 阐述自己的工作并告知实时站点组织。
- 执行有序的方案测试。
- 在至少一个开发和测试环境以及一个生产环境中遵循软件开发与过渡最佳做法。
- 随时了解与之集成的标识提供程序和服务的新进展。 例如，跟踪机密的更改情况以及对服务的计划内和计划外更改。
- 设置主动监控，监控生产环境的响应能力。 有关与 Application Insights 集成的详细信息，请参阅 [Azure Active Directory B2C：收集日志](analytics-with-application-insights.md)。
- 在 Azure 订阅中保留最新的联系电子邮件地址，并快速回复 Microsoft 活动站点团队的电子邮件。
- 根据 Microsoft 活动站点团队的通知及时采取措施。

## <a name="terms-for-features-in-public-preview"></a>公共预览版功能的使用条款

- 我们建议仅将公共预览版功能用于评估目的。
- 服务级别协议 (SLA) 不适用于公共预览版功能。
- 可以通过常规支持渠道提出公共预览版功能的支持请求。

## <a name="features-by-stage-and-known-issues"></a>按阶段的功能和已知问题

自定义策略/标识体验框架功能正在持续而快速地进行开发。 下表是功能和组件可用性的索引。


### <a name="protocols-and-authorization-flows"></a>协议和授权流

| 功能 | 开发 | 预览 | GA | 说明 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2 授权代码](authorization-code-flow.md) |  |  | X |  |
| 具有 PKCE 的 OAuth2 授权代码 |  |  | X | 仅限移动应用程序  |
| [OAuth2 隐式流](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2 资源所有者密码凭据](ropc-custom.md) |  | X |  |  |
| [OIDC 连接](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |  |X  | POST 和重定向绑定。 |
| OAuth1 |  |  |  | 不支持。 |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>标识提供者联合 

| 功能 | 开发 | 预览 | GA | 说明 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | 例如 Google+。  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | 例如 Facebook。  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | 例如 Twitter。 |
| [SAML2](saml-identity-provider-technical-profile.md) |  |   | X | 例如 Salesforce、ADFS。 |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST API 集成

| 功能 | 开发 | 预览 | GA | 说明 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [具有基本身份验证的 REST API](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [具有客户端证书身份验证的 REST API](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [具有 OAuth2 持有者身份验证的 REST API](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>组件支持

| 功能 | 开发 | 预览 | GA | 说明 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [电话因素身份验证](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure MFA 身份验证](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [一次性密码](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) 用作本地目录 |  |  | X |  |
| 用于电子邮件验证的 Azure 电子邮件子系统 |  |  | X |  |
| [第三方电子邮件服务提供程序](custom-email-mailjet.md) |  |X  |  |  |
| [多语言支持](localization.md)|  |  | X |  |
| [谓词验证](predicates.md) |  |  | X | 例如，密码复杂性。 |
| [显示控件](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>页面布局版本

| 功能 | 开发 | 预览 | GA | 说明 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [JavaScript 支持](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF 集成

| 功能 | 开发 | 预览 | GA | 说明 |
| ------- | :-----------: | :-------: | :--: | ----- |
| 查询字符串参数 `domain_hint` |  |  | X | 作为声明提供时，可以传递给 IDP。 |
| 查询字符串参数 `login_hint` |  |  | X | 作为声明提供时，可以传递给 IDP。 |
| 通过 `client_assertion` 将 JSON 插入用户历程 | X |  |  | 即将弃用。 |
| 将 JSON 作为 `id_token_hint` 插入用户历程 |  | X |  | 用于传递 JSON 的前向方法。 |
| [向应用程序传递标识提供者令牌](idp-pass-through-custom.md) |  | X |  | 例如，从 Facebook 到应用。 |

### <a name="session-management"></a>会话管理

| 功能 | 开发 | 预览 | GA | 说明 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [默认 SSO 会话提供程序](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [外部登录会话提供程序](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO 会话提供程序](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [单一登录](session-overview.md#sign-out)  |  | X |  |  |

### <a name="security"></a>安全性

| 功能 | 开发 | 预览 | GA | 说明 |
|-------- | :-----------: | :-------: | :--: | ----- |
| 策略密钥 - 生成、手动、上传 |  |  | X |  |
| 策略密钥 - RSA/证书、机密 |  |  | X |  |


### <a name="developer-interface"></a>开发人员接口

| 功能 | 开发 | 预览 | GA | 说明 |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure 门户-IEF UX |  |  | X |  |
| 策略上传 |  |  | X |  |
| [Application Insights 用户旅程日志](troubleshoot-with-application-insights.md) |  | X |  | 用于在开发过程中进行故障排除。  |
| [Application Insights 事件日志](application-insights-technical-profile.md) |  | X |  | 用于监视生产中的用户流。 |


## <a name="next-steps"></a>后续步骤

- 请参阅[可用于 Azure AD B2C 的 Microsoft Graph 操作](microsoft-graph-operations.md)
- 详细了解[自定义策略以及与用户流的区别](custom-policy-overview.md)。

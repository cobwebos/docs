---
title: 自定义策略的开发人员说明
titleSuffix: Azure AD B2C
description: 有关使用自定义策略配置和维护 Azure AD B2C 的开发人员说明。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee3b5bd3278412949074b77f9d1c53d63a467280
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189389"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的自定义策略的开发人员说明

Azure Active Directory B2C 中的自定义策略配置现已正式发布。 此配置方法针对构建复杂标识解决方案的高级标识开发人员。 自定义策略使身份体验框架的强大功能 Azure AD B2C 租户中可用。
使用自定义策略的高级标识开发人员应计划投入一些时间来完成演练和阅读参考文档。

尽管大部分可用的自定义策略选项现已正式发布，但在软件生命周期的不同阶段，都有一些基本功能，如技术配置文件类型和内容定义 Api。 还有很多。 下表指定了更精细级别的可用性级别。

## <a name="features-that-are-generally-available"></a>公开发布的功能

- 使用自定义策略创作并上传自定义身份验证用户旅程。
    - 将用户旅程逐步描述为声明提供程序之间的交换。
    - 在用户旅程中定义条件分支。
- 与自定义身份验证用户旅程中启用 REST API 的服务进行互操作。
- 与兼容 OpenIDConnect 协议的标识提供程序联合。
- 与遵循 SAML 2.0 协议的标识提供程序联合。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自定义策略功能集开发人员的责任

手动策略配置授予对 Azure AD B2C 基础平台的较低级别访问权限，并导致创建唯一的信任框架。 自定义标识提供者、信任关系、与外部服务的集成以及分步工作流的许多可能的干扰需要一种设计和配置的一种方法。

使用自定义策略功能集的开发人员应遵循以下准则：

- 熟悉自定义策略和密钥/机密管理的配置语言。 有关详细信息，请参阅[TrustFrameworkPolicy](trustframeworkpolicy.md)。
- 取得方案和自定义集成的所有权。 记录你的工作并通知你的实时站点组织。
- 执行有序的方案测试。
- 在至少一个开发和测试环境以及一个生产环境中遵循软件开发与过渡最佳做法。
- 随时了解与之集成的标识提供程序和服务的新进展。 例如，跟踪机密的更改情况以及对服务的计划内和计划外更改。
- 设置主动监控，监控生产环境的响应能力。 有关与 Application Insights 集成的详细信息，请参阅[Azure Active Directory B2C：收集日志](analytics-with-application-insights.md)。
- 在 Azure 订阅中保留最新的联系电子邮件地址，并快速回复 Microsoft 活动站点团队的电子邮件。
- 根据 Microsoft 活动站点团队的通知及时采取措施。

## <a name="terms-for-features-in-public-preview"></a>公共预览版中的功能条款

- 建议你仅将公共预览版功能用于评估目的。
- 服务级别协议（Sla）不适用于公共预览功能。
- 对公共预览功能的支持请求可以通过常规支持渠道进行归档。

## <a name="features-by-stage-and-known-issues"></a>按阶段的功能和已知问题

自定义策略/标识体验框架功能处于恒定和快速开发下。 下表是功能和组件可用性的索引。

### <a name="identity-providers-tokens-protocols"></a>标识提供者、令牌、协议

| Feature | 开发 | 预览 | GA | 说明 |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | 例如，Google +。  |
| IDP-OAUTH2 |  |  | X | 例如 Facebook。  |
| IDP-OAUTH1 （twitter） |  | X |  | 例如，Twitter。 |
| IDP-OAUTH1 （ex-twitter） |  |  |  | 不支持 |
| IDP-SAML |  |   | X | 例如，Salesforce、ADFS。 |
| IDP-WSFED | X |  |  |  |
| 信赖方 OAUTH1 |  |  |  | 不支持。 |
| 信赖方 OAUTH2 |  |  | X |  |
| 信赖方 OIDC |  |  | X |  |
| 信赖方 SAML |  |X  |  |  |
| 信赖方 WSFED | X |  |  |  |
| REST API 与基本身份验证和证书身份验证 |  |  | X | 例如，Azure 逻辑应用。 |

### <a name="component-support"></a>组件支持

| Feature | 开发 | 预览 | GA | 说明 |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure 多重身份验证 |  |  | X |  |
| Azure Active Directory 用作本地目录 |  |  | X |  |
| 用于电子邮件验证的 Azure 电子邮件子系统 |  |  | X |  |
| 多语言支持|  |  | X |  |
| 谓词验证 |  |  | X | 例如，密码复杂性。 |
| 使用第三方电子邮件服务提供商 |  |X  |  |  |

### <a name="content-definition"></a>内容定义

| Feature | 开发 | 预览 | GA | 说明 |
| ------- | :-----------: | :-------: | :--: | ----- |
| 错误页，api.error |  |  | X |  |
| IDP 选择页，api.idpselections |  |  | X |  |
| 用于注册的 IDP 选择，api.idpselections.signup |  |  | X |  |
| 忘记密码，api.localaccountpasswordreset |  |  | X |  |
| 本地帐户登录，api.localaccountsignin |  |  | X |  |
| 本地帐户注册，api.localaccountsignup |  |  | X |  |
| MFA 页，api.phonefactor |  |  | X |  |
| 自行断言的社交帐户注册，selfasserted |  |  | X |  |
| 自我断言配置文件更新，api.selfasserted.profileupdate |  |  | X |  |
| 统一注册或登录页 signuporsignin.xml，参数 "disableSignup" |  |  | X |  |
| JavaScript/页面布局 |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF 集成

| Feature | 开发 | 预览 | GA | 说明 |
| ------- | :-----------: | :-------: | :--: | ----- |
| 查询字符串参数 domain_hint |  |  | X | 可作为声明提供，可传递给 IDP。 |
| 查询字符串参数 login_hint |  |  | X | 可作为声明提供，可传递给 IDP。 |
| 通过 client_assertion 将 JSON 插入到 UserJourney 中 | X |  |  | 将不推荐使用。 |
| 将 JSON 作为 id_token_hint 插入到 UserJourney 中 |  | X |  | 传递 JSON 的前进方法。 |
| 将 IDP 令牌传递给应用程序 |  | X |  | 例如，从 Facebook 到应用。 |

### <a name="session-management"></a>会话管理

| Feature | 开发 | 预览 | GA | 说明 |
| ------- | :-----------: | :-------: | :--: | ----- |
| SSO 会话提供程序 |  |  | X |  |
| 外部登录会话提供程序 |  |  | X |  |
| SAML SSO 会话提供程序 |  |  | X |  |
| 默认 SSO 会话提供程序 |  |  | X |  |

### <a name="security"></a>安全性

| Feature | 开发 | 预览 | GA | 说明 |
|-------- | :-----------: | :-------: | :--: | ----- |
| 策略密钥 - 生成、手动、上传 |  |  | X |  |
| 策略密钥 - RSA/证书、机密 |  |  | X |  |
| 策略上传 |  |  | X |  |

### <a name="developer-interface"></a>开发人员接口

| Feature | 开发 | 预览 | GA | 说明 |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure 门户-IEF UX |  |  | X |  |
| Application Insights UserJourney 日志 |  | X |  | 用于在开发过程中进行故障排除。  |
| Application Insights 事件日志（通过 orchestration 步骤） |  | X |  | 用于监视生产中的用户流。 |

## <a name="next-steps"></a>后续步骤

了解有关[自定义策略的详细信息以及与用户流的不同之处](custom-policy-overview.md)。

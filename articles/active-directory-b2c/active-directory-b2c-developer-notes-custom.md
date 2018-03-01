---
title: "Azure Active Directory B2C：有关使用自定义策略的开发人员说明 | Microsoft Docs"
description: "有关使用自定义策略配置和维护 Azure AD B2C 的开发人员说明"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/13/2017
ms.author: joroja
ms.openlocfilehash: 7f65ad324e1bf0887ea293254621466a2f1ddaff
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Azure Active Directory B2C 自定义策略公共预览版发行说明
自定义策略功能集现已推出公共预览版，供所有 Azure Active Directory B2C (Azure AD B2C) 客户评估。 此功能集面向构建最复杂标识解决方案的高级标识开发人员。  

目前，此功能集要求开发人员直接通过 XML 文件编辑配置标识体验框架。 这种配置方法非常有效，但也比较复杂。 使用标识体验框架的高级标识开发人员应投入时间完成演练并阅读参考文档。 

## <a name="features-included-in-this-public-preview"></a>此公共预览版包含的功能
公共预览版中引入的新功能可让开发人员执行以下任务：<br>

* 使用自定义策略创作并上传自定义身份验证用户旅程。 
   * 将用户旅程逐步描述为声明提供程序之间的交换。 
   * 在用户旅程中定义条件分支。 
* 在自定义身份验证用户旅程中集成已启用 REST API 的服务。  
* 添加与符合 OpenIDConnect 标准的标识提供者的联合。 <br>
* 添加与遵守 SAML 2.0 协议的标识提供者的联合。 

## <a name="terms-of-the-public-preview"></a>公共预览版条款

* 建议将新功能仅用于评估。<br>
* 不应在生产环境中使用新功能。<br>
* 服务级别协议 (SLA) 不适用于新功能。 <br>
* 可通过普通支持渠道提出支持请求。 <br>
* 尚未确定公开上市的日期。<br>
* Microsoft 可以出于任何原因，根据自身的判断，标识并拒绝或限制已超出充当客户标识和访问管理 (CIAM) 平台的 Azure AD B2C 产品规章范围的方案和用户旅程。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自定义策略功能集开发人员的责任
手动策略配置授予对 Azure AD B2C 基础平台的较低访问级别，因此要求创建唯一的、完全可自定义的信任框架。 自定义标识提供者、信任关系、与外部服务的集成以及分步工作流的各种排列组合方式，对使用这些资源的高级开发人员提出了更高的要求。

为充分利用公共预览版，建议使用自定义策略功能集的开发人员遵守以下准则：
* 熟悉标识体验框架和密钥/机密管理的配置语言。
* 取得方案和自定义集成的所有权。
* 执行有序的方案测试。
* 在至少一个开发和测试环境以及一个生产环境中遵循软件开发与过渡最佳做法。
* 随时了解与之集成的标识提供程序和服务的新进展。 例如，跟踪机密的更改情况以及对服务的计划内和计划外更改。
* 设置主动监控，监控生产环境的响应能力。
* 在 Azure 订阅中保留最新的联系电子邮件地址，并快速回复 Microsoft 活动站点团队的电子邮件。
* 根据 Microsoft 活动站点团队的通知及时采取措施。 

## <a name="features-by-stage-and-known-issues"></a>按阶段的功能和已知问题
自定义策略/标识体验框架功能正在持续而快速地进行开发。  下表是功能 / 组件可用性的索引。

请将问题发布到 Stack Overflow，网址为 [https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>标识提供者、令牌、协议
与外部组件和应用程序的接口

| 功能 | 开发 | 预览 | GA | 说明 |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | 例如 Google+ |
| IDP-OAUTH2 |  | x |  | 例如 Facebook  |
| IDP-OAUTH1 |  | x |  | 例如 Twitter |
| IDP-SAML |  | x |  | 例如 Salesforce、ADFS |
| IDP-WSFED | x |  |  |  |
| 信赖方 OAUTH |  | x |  |  |
| 信赖方 OIDC |  | x |  |  |
| 信赖方 SAML | x |  |  |  |
| 信赖方 WSFED | x |  |  |  |
| 具有基本和证书身份验证的 REST API |  | x |  | 例如 Azure Functions |


### <a name="component-support"></a>组件支持


| 功能 | 开发 | 预览 | GA | 说明 |
|-------------------------------------------|-------------|---------|----|-------|
| Azure 多重身份验证 |  | x |  |  |
| Azure Active Directory 用作本地目录 |  | x |  |  |
| 用于 2FA 的 Azure 电子邮件子系统 |  | x |  |  |
| 多语言支持|  | x |  |  |
| 密码复杂性 | x |  |  |  |


### <a name="content-definition"></a>内容定义

| 功能 | 开发 | 预览 | GA | 说明 |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   错误页，api.error |  | x |  |  |
|   IDP 选择页，api.idpselections |  | x |  |  |
|   用于注册的 IDP 选择，api.idpselections.signup |  | x |  |  |
|   忘记密码，api.localaccountpasswordreset |  | x |  |  |
|   本地帐户登录，api.localaccountsignin |  | x |  |  |
|   本地帐户注册，api.localaccountsignup |  | x |  |  |
|   MFA 页，api.phonefactor |  | x |  |  |
|   自我断言 - 例如社交帐户注册，api.selfasserted |  | x |  |  |
|   自我断言配置文件更新，api.selfasserted.profileupdate |  | x |  |  |
|   统一的注册或登录页，api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>App-IEF 集成
| 功能 | 开发 | 预览 | GA | 说明 |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| 查询字符串参数 id_token_hint | x |  |  |  |
| 查询字符串参数 domain_hint |  | x |  | 作为声明提供时，可以传递给 IDP |
| 查询字符串参数 login_hint |  | x |  | 作为声明提供时，可以传递给 IDP |
| 通过 client_assertion 将 JSON 插入到 UserJourney 中 | x |  |  | 即将弃用 |
| 将 JSON 作为 id_token_hint 插入到 UserJourney 中 | x |  |  | 用于传递 JSON 的前向方法 |


### <a name="session-management"></a>会话管理

| 功能 | 开发 | 预览 | GA | 说明 |
|---------------------------------|-------------|---------|----|-------|
| SSO 会话提供程序 |  | x |  |  |
| 外部登录会话提供程序 |  | x |  |  |
| SAML SSO 会话提供程序 |  | x |  |  |


### <a name="security"></a>“安全”
| 功能 | 开发 | 预览 | GA | 说明 |
|---------------------------------------------|-------------|---------|----|-------|
| 策略密钥 - 生成、手动、上传 |  | x |  |  |
| 策略密钥 - RSA/证书、机密 |  | x |  |  |


### <a name="developer-interface"></a>开发人员接口
| 功能 | 开发 | 预览 | GA | 说明 |
|---------------------------------------------|-------------|---------|----|-------|
| Azure 门户-IEF UX |  | x |  |  |
| Application Insights UserJourney 日志  |  | x |  |  |
| Application Insights 事件日志 |x|  |  |  |



## <a name="next-steps"></a>后续步骤
[自定义策略入门](active-directory-b2c-get-started-custom.md)。
